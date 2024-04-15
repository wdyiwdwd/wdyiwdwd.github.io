---
title: 'Caltech Lane Detection源码学习笔记'
date: 2022-04-26
permalink: /posts/2022-04-26-research-journal-caltech-learning
tags:
  - research journal
---

Caltech Lane Detection源码学习笔记.


直线参数化方法，以线段端点形式进行表示和存储：

```c++
/// Line structure with start and end points
typedef struct Line
{
  ///start point
  FLOAT_POINT2D startPoint;
  ///end point
  FLOAT_POINT2D endPoint;
  ///color of line
  LineColor color;
  ///score of line
  float score;
} Line;
```

初始化二维直线参数\\((r,\theta)\\)的Bins：

```c++
  //define the accumulator array: rows correspond to r and columns to theta
  int rBins = int((rMax-rMin)/rStep);
  int thetaBins = int((thetaMax-thetaMin)/thetaStep);
  CvMat *houghSpace = cvCreateMat(rBins, thetaBins, CV_MAT_TYPE(image->type)); //FLOAT_MAT_TYPE);
  assert(houghSpace!=0);
  //init to zero
  cvSet(houghSpace, cvRealScalar(0));

  //init values of r and theta
  FLOAT *rs = new FLOAT[rBins];
  FLOAT *thetas = new FLOAT[thetaBins];
  FLOAT r, theta;
  int ri, thetai;
  for (r=rMin+rStep/2,  ri=0 ; ri<rBins; ri++,r+=rStep)
    rs[ri] = r;
  for (theta=thetaMin, thetai=0 ; thetai<thetaBins; thetai++,
    theta+=thetaStep)
    thetas[thetai] = theta;

  //get non-zero points in the image
  int nzCount = cvCountNonZero(image);
  CvMat *nzPoints = cvCreateMat(nzCount, 2, CV_32SC1);
  int idx = 0;
  for (int i=0; i<image->width; i++)
    for (int j=0; j<image->height; j++)
      if ( cvGetReal2D(image, j, i) )
      {
        CV_MAT_ELEM(*nzPoints, int, idx, 0) = i;
        CV_MAT_ELEM(*nzPoints, int, idx, 1) = j;
        idx++;
      }
```

IPM图像像素空间到Hough空间的转换：

```c++
    //calculate r values for all theta and all points
    int i, k; //j
    for (i=0; i<nzCount; i++)
      for (k=0; k<thetaBins; k++)
      {
        //compute the r value for that point and that theta
        theta = thetas[k];
        float rval = CV_MAT_ELEM(*nzPoints, int, i, 0) * cos(theta) +
        CV_MAT_ELEM(*nzPoints, int, i, 1) * sin(theta); //x y
        int r = (int)( ( rval - rMin) / rStep);
        //	    CV_MAT_ELEM(*rPoints, int, i, k) =
        //(int)( ( rval - rMin) / rStep);

        //accumulate in the hough space if a valid value
        if (r>=0 && r<rBins)
          if(binarize)
            CV_MAT_ELEM(*houghSpace, INT_MAT_ELEM_TYPE, r, k)++;
          //CV_MAT_ELEM(*image, INT_MAT_ELEM_TYPE, j, i);
        else
          CV_MAT_ELEM(*houghSpace, FLOAT_MAT_ELEM_TYPE, r, k)+=
          CV_MAT_ELEM(*image, FLOAT_MAT_ELEM_TYPE,
                      CV_MAT_ELEM(*nzPoints, int, i, 1),
                      CV_MAT_ELEM(*nzPoints, int, i, 0));
      }
```

`Stoplines.conf`文件中Hough聚类各参数设置情况：

```yaml
##Hough Transform settings
rMin = 0 #0
rMax = 120 #120
rStep = 3 #3#1
thetaMin = 80 #80#85
thetaMax = 100 #100#95
thetaStep = 1 #2#1
```

第8、9行的几何理解：

![微信图片_20220425111455](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img1.jpg)

![微信图片_20220425111450](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img2.jpg)

Hough空间平滑，并寻找局部极值，作为候选直线参数：

```c++
  //smooth hough transform
  if (smoothScores)
    cvSmooth(houghSpace, houghSpace, CV_GAUSSIAN, 3);

  //get local maxima
  vector <double> maxLineScores;
  vector <CvPoint> maxLineLocs;
  if (localMaxima)
  {
    //get local maxima in the hough space
    mcvGetMatLocalMax(houghSpace, maxLineScores, maxLineLocs, detectionThreshold);
  }
  else
  {
    //get the maxima above the threshold
    mcvGetMatMax(houghSpace, maxLineScores, maxLineLocs, detectionThreshold);
  }

  //get the maximum value
  double maxLineScore;
  CvPoint maxLineLoc;
  cvMinMaxLoc(houghSpace, 0, &maxLineScore, 0, &maxLineLoc);
  if (maxLineScores.size()==0 && maxLineScore>=detectionThreshold)
  {
    maxLineScores.push_back(maxLineScore);
    maxLineLocs.push_back(maxLineLoc);
  }
```

`mcvGetMatLocalMax()`函数实现：

```c++
/** This function gets the local maxima in a matrix and their positions
 *  and its location
 *
 * \param inMat input matrix
 * \param localMaxima the output vector of local maxima
 * \param localMaximaLoc the vector of locations of the local maxima,
 *       where each location is cvPoint(x=col, y=row) zero-based
 *
 */

void mcvGetMatLocalMax(const CvMat *inMat, vector<double> &localMaxima,
		     vector<CvPoint> &localMaximaLoc, double threshold)
{

  double val;

#define MCV_MAT_LOCAL_MAX(type)  \
    /*loop on the matrix and get points that are larger than their*/ \
    /*neighboring 8 pixels*/ \
    for(int i=1; i<inMat->rows-1; i++) \
	for (int j=1; j<inMat->cols-1; j++) \
	{ \
	    /*get the current value*/ \
	    val = CV_MAT_ELEM(*inMat, type, i, j); \
	    /*check if it's larger than all its neighbors*/ \
	    if( val > CV_MAT_ELEM(*inMat, type, i-1, j-1) && \
		val > CV_MAT_ELEM(*inMat, type, i-1, j) && \
		val > CV_MAT_ELEM(*inMat, type, i-1, j+1) && \
		val > CV_MAT_ELEM(*inMat, type, i, j-1) && \
		val > CV_MAT_ELEM(*inMat, type, i, j+1) && \
		val > CV_MAT_ELEM(*inMat, type, i+1, j-1) && \
		val > CV_MAT_ELEM(*inMat, type, i+1, j) && \
		val > CV_MAT_ELEM(*inMat, type, i+1, j+1) && \
                val >= threshold) \
	    { \
		/*found a local maxima, put it in the return vector*/ \
		/*in decending order*/ \
		/*iterators for the two vectors*/ \
		vector<double>::iterator k; \
		vector<CvPoint>::iterator l; \
		/*loop till we find the place to put it in descendingly*/ \
		for(k=localMaxima.begin(), l=localMaximaLoc.begin(); \
		    k != localMaxima.end()  && val<= *k; k++,l++); \
		/*add its index*/ \
		localMaxima.insert(k, val); \
		localMaximaLoc.insert(l, cvPoint(j, i)); \
	    } \
	}
}
```

`mcvGetMatMax()`函数实现：

```c++
/** This function gets the locations and values of all points
 * above a certain threshold
 *
 * \param inMat input matrix
 * \param maxima the output vector of maxima
 * \param maximaLoc the vector of locations of the maxima,
 *       where each location is cvPoint(x=col, y=row) zero-based
 *
 */

void mcvGetMatMax(const CvMat *inMat, vector<double> &maxima,
                  vector<CvPoint> &maximaLoc, double threshold)
{

  double val;

#define MCV_MAT_MAX(type)  \
    /*loop on the matrix and get points that are larger than their*/ \
    /*neighboring 8 pixels*/ \
    for(int i=1; i<inMat->rows-1; i++) \
	for (int j=1; j<inMat->cols-1; j++) \
	{ \
	    /*get the current value*/ \
	    val = CV_MAT_ELEM(*inMat, type, i, j); \
	    /*check if it's larger than threshold*/ \
	    if (val >= threshold) \
	    { \
		/*found a maxima, put it in the return vector*/ \
		/*in decending order*/ \
		/*iterators for the two vectors*/ \
		vector<double>::iterator k; \
		vector<CvPoint>::iterator l; \
		/*loop till we find the place to put it in descendingly*/ \
		for(k=maxima.begin(), l=maximaLoc.begin(); \
		    k != maxima.end()  && val<= *k; k++,l++); \
		/*add its index*/ \
		maxima.insert(k, val); \
		maximaLoc.insert(l, cvPoint(j, i)); \
	    } \
	}
}
```

对Hough空间中距离较近的直线进行融合。

论文相关段落：

> This sum is then smoothed by a Gaussian filter, local maxima are detected to get positions of lines, and then this is further refined to get sub-pixel accuracy by fitting a parabola to the local maxima and its two neighbors. At last, nearby lines are grouped together to eliminate multiple responses to the same line.

代码：

```c++
  //group detected maxima
  if (group && maxLineScores.size()>1)
  {
    //flag for stopping
    bool stop = false;
    while (!stop)
    {
      //minimum distance so far
      float minDist = groupThreshold+5, dist = 0.;
      vector<CvPoint>::iterator iloc, jloc,
      minIloc=maxLineLocs.begin(), minJloc=minIloc+1;
      vector<double>::iterator iscore, jscore, minIscore, minJscore;
      //compute pairwise distance between detected maxima
      for (iloc=maxLineLocs.begin(), iscore=maxLineScores.begin();
      iloc!=maxLineLocs.end(); iloc++, iscore++)
      for (jscore=iscore+1, jloc=iloc+1; jscore!=maxLineScores.end();
           jloc++, jscore++)
      {
        //add pi if neg
        float t1 = thetas[iloc->x]<0 ? thetas[iloc->x] : thetas[iloc->x]+CV_PI;
        float t2 = thetas[jloc->x]<0 ? thetas[jloc->x] : thetas[jloc->x]+CV_PI;
        //get distance
        dist = fabs(rs[iloc->y]-rs[jloc->y]) +
        0.1 * fabs(t1 - t2);//fabs(thetas[iloc->x]-thetas[jloc->x]);
        //check if minimum
        if (dist<minDist)
        {
          minDist = dist;
          minIloc = iloc; minIscore = iscore;
          minJloc = jloc; minJscore = jscore;
        }
      }
      //check if minimum distance is less than groupThreshold
      if (minDist >= groupThreshold)
        stop = true;
      else
      {
        //combine the two minimum ones with weighted average of
        //their scores
        double x =  (minIloc->x * *minIscore + minJloc->x * *minJscore) /
        (*minIscore + *minJscore);
        double y =  (minIloc->y * *minIscore + minJloc->y * *minJscore) /
        (*minIscore + *minJscore);
        //put into the first
        minIloc->x = (int)x;// ((minJloc->x + minJloc->x)/2.0); // (int) x;
        minIloc->y = (int)y;// ((minJloc->y + minIloc->y)/2.0); // (int) y;
        *minIscore = (*minJscore + *minIscore)/2;///2;
        //delete second one
        maxLineLocs.erase(minJloc);
        maxLineScores.erase(minJscore);

        //check if to put somewhere else depending on the changed score
        for (iscore=maxLineScores.begin(), iloc=maxLineLocs.begin();
             iscore!=maxLineScores.end() && *minIscore <= *iscore;
             iscore++, iloc++);
        //swap the original location if different
        if (iscore!=minIscore )
        {
          //insert in new position
          maxLineScores.insert(iscore, *minIscore);
          maxLineLocs.insert(iloc, *minIloc);
          //delte old
          maxLineScores.erase(minIscore);
          maxLineLocs.erase(minIloc);
        }
      }
    }
  }
```

检测直线输出，以线段端点形式：

```c++
  //process detected maxima and return detected line(s)
  for(int i=0; i<int(maxLineScores.size()); i++)
  {
    //check if above threshold
    if (maxLineScores[i]>=detectionThreshold)
    {
      //get sub-pixel accuracy
      //
      //get the two end points from the r-theta
      Line line;
      assert(maxLineLocs[i].x>=0 && maxLineLocs[i].x<thetaBins);
      assert(maxLineLocs[i].y>=0 && maxLineLocs[i].y<rBins);
      mcvIntersectLineRThetaWithBB(rs[maxLineLocs[i].y],
                                   thetas[maxLineLocs[i].x],
                                   cvSize(image->cols, image->rows), &line);
      //get line extent
      //put the extracted line
      lines->push_back(line);
      if (lineScores)
        (*lineScores).push_back(maxLineScores[i]);

    }
    //not above threshold
    else
    {
      //exit out of the for loop, as the scores are sorted descendingly
      break;
    }
  }
```

`mcvIntersectLineRThetaWithBB`函数实现：

```c++
/** This function intersects the input line (given in r and theta) with
 *  the given bounding box where the line is represented by:
 *  x cos(theta) + y sin(theta) = r
 *
 * \param r the r value for the input line
 * \param theta the theta value for the input line
 * \param bbox the bounding box
 * \param outLine the output line
 *
 */
void mcvIntersectLineRThetaWithBB(FLOAT r, FLOAT theta, const CvSize bbox,
                                  Line *outLine)
{
  //hold parameters
  double xup, xdown, yleft, yright;

  //intersect with top and bottom borders: y=0 and y=bbox.height-1
  if (cos(theta)==0) //horizontal line
  {
    xup = xdown = bbox.width+2;
  }
  else
  {
    xup = r / cos(theta);
    xdown = (r-bbox.height*sin(theta))/cos(theta);
  }

  //intersect with left and right borders: x=0 and x=bbox.widht-1
  if (sin(theta)==0) //horizontal line
  {
    yleft = yright = bbox.height+2;
  }
  else
  {
    yleft = r/sin(theta);
    yright = (r-bbox.width*cos(theta))/sin(theta);
  }

  //points of intersection
  FLOAT_POINT2D pts[4] = { {xup, 0}, {xdown,bbox.height},
        {0, yleft},{bbox.width, yright}};

  //get the starting point
  int i;
  for (i=0; i<4; i++)
  {
    //if point inside, then put it
    if(mcvIsPointInside(pts[i], bbox))
    {
	    outLine->startPoint.x = pts[i].x;
	    outLine->startPoint.y = pts[i].y;
	    //get out of for loop
	    break;
    }
  }
  //get the ending point
  for (i++; i<4; i++)
  {
    //if point inside, then put it
    if(mcvIsPointInside(pts[i], bbox))
    {
	    outLine->endPoint.x = pts[i].x;
	    outLine->endPoint.y = pts[i].y;
	    //get out of for loop
	    break;
    }
  }
}
```

相关几何理解：

![微信图片_20220426101043](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img3.jpg)

对Hough聚类输出的直线进行融合，以及边界框的计算：

```c++
  int width = image->width-1;
  int height = image->height-1;
  //try grouping the lines into regions
  //float groupThreshold = 15;
  mcvGroupLines(lines, lineScores, lineConf->groupThreshold,
                cvSize(width, height));

  //group bounding boxes of lines
  float overlapThreshold = lineConf->overlapThreshold; //0.5; //.8;
  vector<CvRect> boxes;
  mcvGetLinesBoundingBoxes(lines, lineType, cvSize(width, height),
                           boxes);
  mcvGroupBoundingBoxes(boxes, lineType, overlapThreshold);
```

`mcvGroupLines`函数实现：

```c++
/** This function groups nearby lines
 *
 * \param lines vector of lines
 * \param lineScores scores of input lines
 * \param groupThreshold the threshold used for grouping
 * \param bbox the bounding box to intersect with
 */
void mcvGroupLines(vector<Line> &lines, vector<float> &lineScores,
                   float groupThreshold, CvSize bbox)
{

  //convert the lines into r-theta parameters
  int numInLines = lines.size();
  vector<float> rs(numInLines);
  vector<float> thetas(numInLines);
  for (int i=0; i<numInLines; i++)
    mcvLineXY2RTheta(lines[i], rs[i], thetas[i]);

  //flag for stopping
  bool stop = false;
  while (!stop)
  {
    //minimum distance so far
    float minDist = groupThreshold+5, dist;
    vector<float>::iterator ir, jr, itheta, jtheta, minIr, minJr, minItheta, minJtheta,
    iscore, jscore, minIscore, minJscore;
    //compute pairwise distance between detected maxima
    for (ir=rs.begin(), itheta=thetas.begin(), iscore=lineScores.begin();
    ir!=rs.end(); ir++, itheta++, iscore++)
    for (jr=ir+1, jtheta=itheta+1, jscore=iscore+1;
    jr!=rs.end(); jr++, jtheta++, jscore++)
    {
      //add pi if neg
      float t1 = *itheta<0 ? *itheta : *itheta+CV_PI;
      float t2 = *jtheta<0 ? *jtheta : *jtheta+CV_PI;
      //get distance
      dist = 1 * fabs(*ir - *jr) + 1 * fabs(t1 - t2);//fabs(*itheta - *jtheta);
      //check if minimum
      if (dist<minDist)
      {
        minDist = dist;
        minIr = ir; minItheta = itheta;
        minJr = jr; minJtheta = jtheta;
        minIscore = iscore; minJscore = jscore;
      }
    }
    //check if minimum distance is less than groupThreshold
    if (minDist >= groupThreshold)
      stop = true;
    else
    {
      //put into the first
      *minIr = (*minIr + *minJr)/2;
      *minItheta = (*minItheta + *minJtheta)/2;
      *minIscore = (*minIscore + *minJscore)/2;
      //delete second one
      rs.erase(minJr);
      thetas.erase(minJtheta);
      lineScores.erase(minJscore);
    }
  }//while

  //put back the lines
  lines.clear();
  //lines.resize(rs.size());
  vector<float> newScores=lineScores;
  lineScores.clear();
  for (int i=0; i<(int)rs.size(); i++)
  {
    //get the line
    Line line;
    mcvIntersectLineRThetaWithBB(rs[i], thetas[i], bbox, &line);
    //put in place descendingly
    vector<float>::iterator iscore;
    vector<Line>::iterator iline;
    for (iscore=lineScores.begin(), iline=lines.begin();
    iscore!=lineScores.end() && newScores[i]<=*iscore; iscore++, iline++);
    lineScores.insert(iscore, newScores[i]);
    lines.insert(iline, line);
  }
  //clear
  newScores.clear();
}
```

`mcvLineXY2RTheta`函数实现：

```c++
/** This functions converts a line defined by its two end-points into its
 *   r and theta (origin is at top-left corner with x right and y down and
 * theta measured positive clockwise(with y pointing down) -pi < theta < pi )
 *
 * \param line input line
 * \param r the returned r (normal distance to the line from the origin)
 * \param outLine the output line
 *
 */
void mcvLineXY2RTheta(const Line &line, float &r, float &theta)
{
  //check if vertical line x1==x2
  if(line.startPoint.x == line.endPoint.x)
  {
    //r is the x
    r = fabs(line.startPoint.x);
    //theta is 0 or pi
    theta = line.startPoint.x>=0 ? 0. : CV_PI;
  }
  //check if horizontal i.e. y1==y2
  else if(line.startPoint.y == line.endPoint.y)
  {
    //r is the y
    r = fabs(line.startPoint.y);
    //theta is pi/2 or -pi/2
    theta = (float) line.startPoint.y>=0 ? CV_PI/2 : -CV_PI/2;
  }
  //general line
  else
  {
    //tan(theta) = (x2-x1)/(y1-y2)
    theta =  atan2(line.endPoint.x-line.startPoint.x,
                   line.startPoint.y-line.endPoint.y);
    //r = x*cos(theta)+y*sin(theta)
    float r1 = line.startPoint.x * cos(theta) + line.startPoint.y * sin(theta);
    r = line.endPoint.x * cos(theta) + line.endPoint.y * sin(theta);
    //adjust to add pi if necessary
    if(r1<0 || r<0)
    {
      //add pi
      theta += CV_PI;
      if(theta>CV_PI)
        theta -= 2*CV_PI;
      //take abs
      r = fabs(r);
    }
  }
}
```

`mcvLineXY2RTheta`函数几何理解：

![微信图片_20220426120928](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img4.jpg)

对于Hough聚类中每一条候选直线上的像素点，使用RANSAC算法对直线参数进行拟合，输出参数形式为\\((r,\theta)\\)：

```c++
/** This functions implements RANSAC algorithm for line fitting
 *   given an image
 *
 *
 * \param image input image
 * \param numSamples number of samples to take every iteration
 * \param numIterations number of iterations to run
 * \param threshold threshold to use to assess a point as a good fit to a line
 * \param numGoodFit number of points close enough to say there's a good fit
 * \param getEndPoints whether to get the end points of the line from the data,
 *  just intersect with the image boundaries
 * \param lineType the type of line to look for (affects getEndPoints)
 * \param lineXY the fitted line
 * \param lineRTheta the fitted line [r; theta]
 * \param lineScore the score of the line detected
 *
 */
void mcvFitRansacLine(const CvMat *image, int numSamples, int numIterations,
                      float threshold, float scoreThreshold, int numGoodFit,
                      bool getEndPoints, LineType lineType,
                      Line *lineXY, float *lineRTheta, float *lineScore)
{

  //get the points with non-zero pixels
  CvMat *points;
  points = mcvGetNonZeroPoints(image,true);
  if (!points)
    return;
  //check numSamples
  if (numSamples>points->cols)
    numSamples = points->cols;
  //subtract half
  cvAddS(points, cvRealScalar(0.5), points);

  //normalize pixels values to get weights of each non-zero point
  //get third row of points containing the pixel values
  CvMat w;
  cvGetRow(points, &w, 2);
  //normalize it
  CvMat *weights = cvCloneMat(&w);
  cvNormalize(weights, weights, 1, 0, CV_L1);
  //get cumulative    sum
  mcvCumSum(weights, weights);

  //random number generator
  CvRNG rng = cvRNG(0xffffffff);
  //matrix to hold random sample
  CvMat *randInd = cvCreateMat(numSamples, 1, CV_32SC1);
  CvMat *samplePoints = cvCreateMat(2, numSamples, CV_32FC1);
  //flag for points currently included in the set
  CvMat *pointIn = cvCreateMat(1, points->cols, CV_8SC1);
  //returned lines
  float curLineRTheta[2], curLineAbc[3];
  float bestLineRTheta[2]={-1.f,0.f}, bestLineAbc[3];
  float bestScore=0, bestDist=1e5;
  float dist, score;
  Line curEndPointLine={ {-1.,-1.},{-1.,-1.}},
  bestEndPointLine={ {-1.,-1.},{-1.,-1.}};
  //variabels for getting endpoints
  //int mini, maxi;
  float minc=1e5f, maxc=-1e5f, mind, maxd;
  float x, y, c=0.;
  CvPoint2D32f minp={-1., -1.}, maxp={-1., -1.};
  //outer loop
  for (int i=0; i<numIterations; i++)
  {
    //set flag to zero
    //cvSet(pointIn, cvRealScalar(0));
    cvSetZero(pointIn);
    //get random sample from the points
    #warning "Using weighted sampling for Ransac Line"
    // 	cvRandArr(&rng, randInd, CV_RAND_UNI, cvRealScalar(0), cvRealScalar(points->cols));
    mcvSampleWeighted(weights, numSamples, randInd, &rng);

    for (int j=0; j<numSamples; j++)
    {
      //flag it as included
      CV_MAT_ELEM(*pointIn, char, 0, CV_MAT_ELEM(*randInd, int, j, 0)) = 1;
      //put point
      CV_MAT_ELEM(*samplePoints, float, 0, j) =
      CV_MAT_ELEM(*points, float, 0, CV_MAT_ELEM(*randInd, int, j, 0));
      CV_MAT_ELEM(*samplePoints, float, 1, j) =
      CV_MAT_ELEM(*points, float, 1, CV_MAT_ELEM(*randInd, int, j, 0));
    }

    //fit the line
    mcvFitRobustLine(samplePoints, curLineRTheta, curLineAbc);

    //get end points from points in the samplePoints
    minc = 1e5; mind = 1e5; maxc = -1e5; maxd = -1e5;
    for (int j=0; getEndPoints && j<numSamples; ++j)
    {
      //get x & y
      x = CV_MAT_ELEM(*samplePoints, float, 0, j);
      y = CV_MAT_ELEM(*samplePoints, float, 1, j);

      //get the coordinate to work on
      if (lineType == LINE_HORIZONTAL)
        c = x;
      else if (lineType == LINE_VERTICAL)
        c = y;
      //compare
      if (c>maxc)
      {
        maxc = c;
        maxp = cvPoint2D32f(x, y);
      }
      if (c<minc)
      {
        minc = c;
        minp = cvPoint2D32f(x, y);
      }
    } //for

    // 	fprintf(stderr, "\nminx=%f, miny=%f\n", minp.x, minp.y);
    // 	fprintf(stderr, "maxp=%f, maxy=%f\n", maxp.x, maxp.y);

    //loop on other points and compute distance to the line
    score=0;
    for (int j=0; j<points->cols; j++)
    {
      // 	    //if not already inside
      // 	    if (!CV_MAT_ELEM(*pointIn, char, 0, j))
      // 	    {
        //compute distance to line
        dist = fabs(CV_MAT_ELEM(*points, float, 0, j) * curLineAbc[0] +
        CV_MAT_ELEM(*points, float, 1, j) * curLineAbc[1] + curLineAbc[2]);
        //check distance
        if (dist<=threshold)
        {
          //add this point
          CV_MAT_ELEM(*pointIn, char, 0, j) = 1;
          //update score
          score += cvGetReal2D(image, (int)(CV_MAT_ELEM(*points, float, 1, j)-.5),
                               (int)(CV_MAT_ELEM(*points, float, 0, j)-.5));
        }
        // 	    }
    }

    //check the number of close points and whether to consider this a good fit
    int numClose = cvCountNonZero(pointIn);
    //cout << "numClose=" << numClose << "\n";
    if (numClose >= numGoodFit)
    {
        //get the points included to fit this line
        CvMat *fitPoints = cvCreateMat(2, numClose, CV_32FC1);
        int k=0;
        //loop on points and copy points included
        for (int j=0; j<points->cols; j++)
      if(CV_MAT_ELEM(*pointIn, char, 0, j))
      {
          CV_MAT_ELEM(*fitPoints, float, 0, k) =
        CV_MAT_ELEM(*points, float, 0, j);
          CV_MAT_ELEM(*fitPoints, float, 1, k) =
        CV_MAT_ELEM(*points, float, 1, j);
          k++;

      }

      //fit the line
      mcvFitRobustLine(fitPoints, curLineRTheta, curLineAbc);

      //compute distances to new line
      dist = 0.;
      for (int j=0; j<fitPoints->cols; j++)
      {
        //compute distance to line
        x = CV_MAT_ELEM(*fitPoints, float, 0, j);
        y = CV_MAT_ELEM(*fitPoints, float, 1, j);
        float d = fabs( x * curLineAbc[0] +
        y * curLineAbc[1] +
        curLineAbc[2])
        * cvGetReal2D(image, (int)(y-.5), (int)(x-.5));
        dist += d;
      }

      //now check if we are getting the end points
      if (getEndPoints)
      {

        //get distances
        mind = minp.x * curLineAbc[0] +
        minp.y * curLineAbc[1] + curLineAbc[2];
        maxd = maxp.x * curLineAbc[0] +
        maxp.y * curLineAbc[1] + curLineAbc[2];

        //we have the index of min and max points, and
        //their distance, so just get them and compute
        //the end points
        curEndPointLine.startPoint.x = minp.x
        - mind * curLineAbc[0];
        curEndPointLine.startPoint.y = minp.y
        - mind * curLineAbc[1];

        curEndPointLine.endPoint.x = maxp.x
        - maxd * curLineAbc[0];
        curEndPointLine.endPoint.y = maxp.y
        - maxd * curLineAbc[1];

        // 		SHOW_MAT(fitPoints, "fitPoints");
        //  		SHOW_LINE(curEndPointLine, "line");
      }

      //dist /= score;

      //clear fitPoints
      cvReleaseMat(&fitPoints);

      //check if to keep the line as best
      if (score>=scoreThreshold && score>bestScore)//dist<bestDist //(numClose > bestScore)
      {
        //update max
        bestScore = score; //numClose;
        bestDist = dist;
        //copy
        bestLineRTheta[0] = curLineRTheta[0];
        bestLineRTheta[1] = curLineRTheta[1];
        bestLineAbc[0] = curLineAbc[0];
        bestLineAbc[1] = curLineAbc[1];
        bestLineAbc[2] = curLineAbc[2];
        bestEndPointLine = curEndPointLine;
      }
    } // if numClose

    //debug
    if (DEBUG_LINES) {//#ifdef DEBUG_GET_STOP_LINES
      char str[256];
      //convert image to rgb
      CvMat* im = cvCloneMat(image);
      mcvScaleMat(image, im);
      CvMat *imageClr = cvCreateMat(image->rows, image->cols, CV_32FC3);
      cvCvtColor(im, imageClr, CV_GRAY2RGB);

      Line line;
      //draw current line if there
      if (curLineRTheta[0]>0)
      {
        mcvIntersectLineRThetaWithBB(curLineRTheta[0], curLineRTheta[1],
                                    cvSize(image->cols, image->rows), &line);
        mcvDrawLine(imageClr, line, CV_RGB(1,0,0), 1);
        if (getEndPoints)
          mcvDrawLine(imageClr, curEndPointLine, CV_RGB(0,1,0), 1);
      }

      //draw best line
      if (bestLineRTheta[0]>0)
      {
        mcvIntersectLineRThetaWithBB(bestLineRTheta[0], bestLineRTheta[1],
                                    cvSize(image->cols, image->rows), &line);
        mcvDrawLine(imageClr, line, CV_RGB(0,0,1), 1);
        if (getEndPoints)
          mcvDrawLine(imageClr, bestEndPointLine, CV_RGB(1,1,0), 1);
      }
      sprintf(str, "scor=%.2f, best=%.2f", score, bestScore);
      mcvDrawText(imageClr, str, cvPoint(30, 30), .25, CV_RGB(255,255,255));

      SHOW_IMAGE(imageClr, "Fit Ransac Line", 10);

      //clear
      cvReleaseMat(&im);
      cvReleaseMat(&imageClr);
    }//#endif
  } // for i

  //return
  if (lineRTheta)
  {
    lineRTheta[0] = bestLineRTheta[0];
    lineRTheta[1] = bestLineRTheta[1];
  }
  if (lineXY)
  {
    if (getEndPoints)
      *lineXY = bestEndPointLine;
    else
      mcvIntersectLineRThetaWithBB(lineRTheta[0], lineRTheta[1],
                                   cvSize(image->cols-1, image->rows-1),
                                   lineXY);
  }
  if (lineScore)
    *lineScore = bestScore;

  //clear
  cvReleaseMat(&points);
  cvReleaseMat(&samplePoints);
  cvReleaseMat(&randInd);
  cvReleaseMat(&pointIn);
}
```

![微信图片_20220426180956](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img5.jpg)

![微信图片_20220426180952](http://sunqinxuan.github.io/images/posts-research-journal-2022-04-26-img6.jpg)



