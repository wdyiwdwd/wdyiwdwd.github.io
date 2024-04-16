---
title: 'ROS节点内存泄漏问题调试'
date: 2022-06-10
permalink: /posts/research-journal-2022-06-10-leak-debug
tags:
  - research journal
---

ROS节点内存泄漏问题调试.

## `data_pretreat_node`节点内存泄漏问题

### 内存泄漏原因

原代码`VisionData`析构函数中`if (!image) delete image;`没有达到释放内存的目的，即`VisionData`中`new`的内存在程序运行期间一直没有释放，导致内存泄漏。

```c++
// sensor_date/vision_data.hpp

class VisionData
{
public:
  VisionData()
  {
    image = new uchar[channel * width * height]();
  }
  ~VisionData()
  {
    if (!image)  delete  image;
  }

public:
  double time = 0.0;
  double seq = 0;
  int width = 600;
  int height = 600;
  int channel = 3;
  uchar *image;
};
```

### 代码修改

`VisionData`析构函数修改如下。

不在析构函数中进行`image`指针的内存释放，这样可以减少后面大量的内存拷贝操作，提高运行效率。

```c++
// sensor_date/vision_data.hpp

class VisionData
{
public:
  VisionData()
  {
    image = new uchar[channel * width * height]();
  }
  ~VisionData()
  {
    // if (!image)  delete  image;
  }
    
  void clear()
  {
    delete[] image;
  }

public:
  double time = 0.0;
  double seq = 0;
  int width = 600;
  int height = 600;
  int channel = 3;
  uchar *image;
};
```

修改`VisionSubscriber::msg_callback`函数如下（第7行与第25行）。

~~临时变量在析构时会调用`delete`，释放指针`image`所指向的内存，当通过`push_back`将指针复制到`new_vision_data_`中，指针`image`将指向无效内存。~~

~~因此直接在`new_vision_data_`中进行`VisionData`的构造，再对其末尾元素进行引用，这样不会改变指针所指向的存在空间，在对`new_vision_data_`进行`clear`等操作时，也可以对内存进行有效释放。~~

若不在析构函数中进行内存释放，则不会出现上述问题，使用临时变量对指针进行拷贝的操作也是可行的。

```c++
// subscriber/vision_subscriber.cpp

void VisionSubscriber::msg_callback(const sensor_msgs::ImageConstPtr &msg_ptr)
{
  buff_mutex_.lock();
    
  // VisionData avm_img;
  new_vision_data_.resize(new_vision_data_.size()+1);
  VisionData& avm_img=new_vision_data_.back();
    
  avm_img.time = msg_ptr->header.stamp.toSec() + delay_time_to_ins_;
  avm_img.seq = msg_ptr->header.seq;
  cv_bridge::CvImageConstPtr cv_ptr;
  try {
    cv_ptr = cv_bridge::toCvCopy(msg_ptr, sensor_msgs::image_encodings::BGR8); 
  } catch (cv_bridge::Exception &ex) {
    ROS_ERROR("cv_bridge exception in rgbcallback: %s", ex.what());
    exit(-1);
  }
    
  ...

  memcpy(avm_img.image, cv_ptr->image.data, sizeof(uchar) * avm_img.channel * avm_img.width * avm_img.height);

  // new_vision_data_.push_back(avm_img);
    
  buff_mutex_.unlock();
}
```

`data_pretreat_flow.cpp`中的相关代码修改如下。

在删除`vision_data_buff_`队列前面的数据时，需要对内存进行释放，因此调用`clear`函数。

第28行将`vision_data_buff_`最前面的元素拷贝给`current_vision_data_`时，需要对`current_vision_data_`原有数据指针指向的内存进行释放。

```c++
// data_pretreat/data_pretreat_flow.cpp

bool DataPretreatFlow::ReadData()
{
  ...

  if (start_ins_time > vision_time) {
    vision_data_buff_.front().clear();
    vision_data_buff_.pop_front();
    return false;
  }
  ...
      
  if (!sensor_inited) {
    if (!valid_ins) {
      vision_data_buff_.front().clear();
      vision_data_buff_.pop_front();
      return false;
    }
    ...
  }
  return true;
}

bool DataPretreatFlow::ValidData()
{
  current_vision_data_.clear();
  current_vision_data_ = vision_data_buff_.front();
  ...

  if (diff_ins_time < -0.05) {
    vision_data_buff_.front().clear();
    vision_data_buff_.pop_front();
    return false;
  }
  ...

  vision_data_buff_.pop_front();
  ins_data_buff_.pop_front();

  return true;
}

```

### 问题解决

修改后内存泄漏问题解决，如下图所示。

<img src="http://sunqinxuan.github.io/images/posts-research-journal-2022-06-10-img1.png" alt="memory_leak_solve"  />


