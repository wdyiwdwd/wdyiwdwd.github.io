---
title: "后端全局图优化位姿参数化及雅可比求解方案"
collection: projects
type: "Project"
permalink: /projects/2021-11-05-pose-graph-optimization
venue: "CICV"
date: 2021-11-05
location: "Beijing, China"
---

后端全局图优化相关方案

## 后端全局图优化位姿参数化及雅可比求解方案

### 基于旋转四元数和位移向量参数表示和自动微分求导法的实现方案

假设对于时刻\\(i\\)，车体位姿表示为\\(x_i=[p_i^T,q_i^T]^T\\)，其中\\(p_i\\)表示三维位置向量，\\(q_i\\)为单位四元数，表示车体姿态。对于两个时刻\\(i\\)和\\(j\\)，车体相对位姿表示为\\(z_{ij}=[\hat{p}_{ij}^T,\hat{q}_{ij}^T]^T\\)，对应残差项定义为

$$
r_{ij}=
\begin{bmatrix}
R(q_i)^T(p_j-p_i)-\hat{p}_{ij} \\
2\cdot \rm{vec}\left((q_i^{-1}q_j)\hat{q}_{ij}^{-1}\right)
\end{bmatrix}
$$

其中，\\(\rm{vec}()\\)表示取四元数的虚部向量，即\\([q_x,q_y,q_z]^T\\)，\\(R(q)\\)表示四元数\\(q\\)所对应的旋转矩阵。相应代码实现在Functor类`EdgePoseError`中，用于Ceres `CostFunction`类中对残差向量以及对应雅可比的计算。

```c++
	template <typename T>
	bool operator()(const T* const position_i, const T* const orientation_i, 
					const T* const position_j, const T* const orientation_j, 
					T* residual_ptr) const
	{
		Eigen::Map<const Eigen::Matrix<T,3,1>> p_i(position_i);
		Eigen::Map<const Eigen::Quaternion<T>> q_i(orientation_i);

		Eigen::Map<const Eigen::Matrix<T,3,1>> p_j(position_j);
		Eigen::Map<const Eigen::Quaternion<T>> q_j(orientation_j);

		Eigen::Quaternion<T> q_i_inv=q_i.conjugate();
		Eigen::Quaternion<T> q_ij=q_i_inv*q_j;
		Eigen::Matrix<T,3,1> p_ij=q_i_inv*(p_j-p_i);

		Eigen::Quaternion<T> q_ij_meas(pose_ij_meas_.linear().template cast<T>());
		Eigen::Quaternion<T> delta_q=q_ij_meas*q_ij.conjugate();

		Eigen::Map<Eigen::Matrix<T,6,1>> residual(residual_ptr);
		Eigen::Map<Eigen::Matrix<T,3,1>> residual_trs(residual_ptr);
		Eigen::Map<Eigen::Matrix<T,3,1>> residual_rot(residual_ptr+3);

		residual_trs=p_ij-pose_ij_meas_.translation().template cast<T>();
		residual_rot=T(2.0)*delta_q.vec();
		residual.applyOnTheLeft(sqrt_information_.template cast<T>());

		return true;
	}
```

雅可比求解采用Ceres中提供的自动微分(Automatic Derivatives)求导方法，其算法原理及性能评估详见文档《基于对偶数与Jet的微分求导方法》。

```c++
	static ceres::CostFunction* Create(const Eigen::Isometry3d& pose_ij,const Matrix6d& sqrt_information)
	{
		return new ceres::AutoDiffCostFunction<EdgePoseError,6,3,4,3,4>
			(new EdgePoseError(pose_ij,sqrt_information));
	}
```

#### 基于位姿李代数参数化和解析微分求导法的实现方案

此方案中，车体位姿表示为最小化参数形式，即\\(\xi_i\in{\mathbb R}^6\\)，其对应的三维车体位姿的变换矩阵形式表示为\\(T_i\in\mathbb{SE}(3)\\)，则有

$$
\xi_i^\wedge\in\mathfrak{se}(3) 
$$

$$
T_i=\exp(\xi_i^\wedge)\in\mathbb{SE}(3)
$$

基于此，两个节点间的相对位姿变换可以表示为

$$
\begin{aligned}
\delta\xi_{ij}
&=\log(\delta T_{ij})^\vee=\log(T_i^{-1}T_j)^\vee \\
&=\log\left(\exp(-\xi_i^\wedge)\exp(\xi_j^\wedge)\right)^\vee
\end{aligned}
$$

对应边的残差项定义为

$$
\begin{aligned}
r_{ij}
&=\log(\delta T_{ij}^{-1}T_i^{-1}T_j)^\vee \\
&=\log(\exp(-\xi_{ij}^\wedge)\exp(-\xi_i^\wedge)\exp(\xi_j^\wedge))^\vee
\end{aligned}
$$

对应雅可比矩阵可以计算为

$$
\begin{aligned}
& \frac{\partial r_{ij}}{\partial \xi_i}
=-{\mathcal J}^{-1}(r_{ij})\rm{Ad}(T_j^{-1})\\
& \frac{\partial r_{ij}}{\partial \xi_j}
={\mathcal J}^{-1}(r_{ij})\rm{Ad}(T_j^{-1})
\end{aligned}
$$

在方案实现中，定义目标函数类`EdgePoseSE3CostFunction`，其对应残差以及雅可比矩阵计算部分的代码如下。

```c++
	virtual bool Evaluate(double const* const* parameters,
						  double *residuals, double **jacobians) const
	{
		Sophus::SE3 pose_i = Sophus::SE3::exp(Vector6d(parameters[0]));
		Sophus::SE3 pose_j = Sophus::SE3::exp(Vector6d(parameters[1]));
		Sophus::SE3 estimate = pose_i.inverse() * pose_j;

		Eigen::Map<Vector6d> residual(residuals);
		residual = sqrt_information_ * ((measurment_.inverse() * estimate).log());

		if(jacobians)
		{
			if(jacobians[0]) {
				Eigen::Map<Eigen::Matrix<double,6,6, Eigen::RowMajor>> jacobian_i(jacobians[0]);
				Matrix6d J = JRInv(Sophus::SE3::exp(residual));
				jacobian_i = (-J) * pose_j.inverse().Adj();
				jacobian_i = sqrt_information_*(-J) * pose_j.inverse().Adj();
			}
			if(jacobians[1]){
				Eigen::Map<Eigen::Matrix<double,6,6, Eigen::RowMajor>> jacobian_j(jacobians[1]);
				Matrix6d J = JRInv(Sophus::SE3::exp(residual));
				jacobian_j = J * pose_j.inverse().Adj();
				jacobian_j = sqrt_information_*J * pose_j.inverse().Adj();
			}
		}
		return true;
	}
```

## 基于对偶数和Jet的自动微分求解算法

### 对偶数(Dual Number)和Jet

对偶数是实数的一种扩展，引入微分单元\\(\epsilon\\)满足\\(\epsilon^2=0\\)，对偶数定义为

$$
a+v\epsilon
$$

其中包含两个部分，即实数部分\\(a\\)和微分部分\\(v\\)。

基于对偶数的定义，可以在不给出显式微分形式的情况下，实现函数精确微分求导。具体地，考虑任意形式的连续可微函数\\(f(x)\\)，对\\(f(x+\epsilon)\\)在\\(x\\)处进行泰勒展开，可得

$$
\begin{aligned}
f(x+\epsilon)&=f(x)+Df(x)\epsilon
+D^2f(x)\frac{\epsilon^2}{2!}
+D^3f(x)\frac{\epsilon^3}{3!}
+\cdots \\
&=f(x)+Df(x)\epsilon
\end{aligned}
$$

接着，扩展实数到\\(n\\)个微分单位\\(\epsilon_i,i=1,\dots,n\\)，满足\\(\forall i,j:\epsilon_i\epsilon_j=0\\)。定义Jet为

$$
x=a+\sum_j{v_j\epsilon_j}
$$

其中包含实数部分\\(a\\)和\\(n\\)维微分部分\\(\bf v\\)，即

$$
x=a+\bf v
$$

对\\(f(a+{\bf v})\\)进行泰勒展开可得

$$
f(a+{\bf v})=f(a)+Df(a){\bf v}
$$

对于多变量的函数\\(f:{\mathbb R}^n\rightarrow{\mathbb R}^m\\)，在\\(x_i=a_i+{\bf v}_i,\forall i=1,\dots,n\\)处的泰勒展开为

$$
f(x_1,\dots,x_n)=f(a_1,\dots,a_n)+
\sum_i{D_if(a_1,\dots,a_n){\bf v}_i}
$$

令\\({\bf v}_i=e_i\\)，即为各个维度的基向量，则有

$$
f(x_1,\dots,x_n)=f(a_1,\dots,a_n)+
\sum_i{D_if(a_1,\dots,a_n)\epsilon_i}
$$

基于此，可以根据各个微分分量$\epsilon_i$对应的系数，即可得到相应值处的雅可比。

### Ceres实现方案

Ceres非线性优化库提供基于对偶数和Jet的精确导数求解方案，即自动微分法(Automatic Derivatives)。首先，须对Jet结构进行定义。

```c++
template<int N> struct Jet {
  double a;
  Eigen::Matrix<double, 1, N> v;
};

template<int N> Jet<N> operator+(const Jet<N>& f, const Jet<N>& g) {
  return Jet<N>(f.a + g.a, f.v + g.v);
}

template<int N> Jet<N> operator-(const Jet<N>& f, const Jet<N>& g) {
  return Jet<N>(f.a - g.a, f.v - g.v);
}

template<int N> Jet<N> operator*(const Jet<N>& f, const Jet<N>& g) {
  return Jet<N>(f.a * g.a, f.a * g.v + f.v * g.a);
}

template<int N> Jet<N> operator/(const Jet<N>& f, const Jet<N>& g) {
  return Jet<N>(f.a / g.a, f.v / g.a - f.a * g.v / (g.a * g.a));
}

template <int N> Jet<N> exp(const Jet<N>& f) {
  return Jet<T, N>(exp(f.a), exp(f.a) * f.v);
}

// This is a simple implementation for illustration purposes, the
// actual implementation of pow requires careful handling of a number
// of corner cases.
template <int N>  Jet<N> pow(const Jet<N>& f, const Jet<N>& g) {
  return Jet<N>(pow(f.a, g.a),
                g.a * pow(f.a, g.a - 1.0) * f.v +
                pow(f.a, g.a) * log(f.a); * g.v);
}
```

接着，以[Rat43](http://www.itl.nist.gov/div898/strd/nls/data/ratkowsky3.shtml)问题为例，定义仿函数类如下。

```c++
struct Rat43CostFunctor {
  Rat43CostFunctor(const double x, const double y) : x_(x), y_(y) {}

  template <typename T>
  bool operator()(const T* parameters, T* residuals) const {
    const T b1 = parameters[0];
    const T b2 = parameters[1];
    const T b3 = parameters[2];
    const T b4 = parameters[3];
    residuals[0] = b1 * pow(1.0 + exp(b2 -  b3 * x_), -1.0 / b4) - y_;
    return true;
  }

  private:
    const double x_;
    const double y_;
};

CostFunction* cost_function =
      new AutoDiffCostFunction<Rat43CostFunctor, 1, 4>(
        new Rat43CostFunctor(x, y));
```

与数值微分算法实现不同的是，`operator()`的定义中使用了模版的形式。

```c++
template <typename T> bool operator()(const T* parameters, T* residuals) const;
```

基于此定义方式，便可以将定义的Jet类型传入`Rat43CostFunctor`函数，来计算误差函数相对于求解变量的雅可比矩阵。

### 性能对比

Ceres非线性优化库共提供了三种雅可比求解方法，即解析微分法(Analytic Derivatives)、数值微分法(Numeric Derivatives)和自动微分法(Automatic Derivatives)。

以[Rat43](http://www.itl.nist.gov/div898/strd/nls/data/ratkowsky3.shtml)问题为例，三种微分方法的性能对比结果列于表3.1，其中Rat43AutomaticDiff为自动微分的求解方案，Rat43Analytic和Rat43AnalyticOptimized是解析微分的求解方法，其实现方案可参考[Ceres - Analytic Derivatives](http://www.ceres-solver.org/analytical_derivatives.html)，Rat43NumericDiffForward、Rat43NumericDiffCentral及Rat43NumericDiffRidders为数据微分求解，其实现可参考[Ceres - Numeric Derivativs](http://www.ceres-solver.org/numerical_derivatives.html)。

|      CostFunction       | Time (ns) |
| :---------------------: | :-------: |
|      Rat43Analytic      |    255    |
| Rat43AnalyticOptimized  |    92     |
| Rat43NumericDiffForward |    262    |
| Rat43NumericDiffCentral |    517    |
| Rat43NumericDiffRidders |   3760    |
| **Rat43AutomaticDiff**  |  **129**  |

### Some Tips

Since this is a large sparse problem (well large for `DENSE_QR` anyways), one way to solve this problem is to set `Solver::Options::linear_solver_type` to `SPARSE_NORMAL_CHOLESKY` and call [`Solve()`](http://www.ceres-solver.org/gradient_solver.html#_CPPv45SolveRKN21GradientProblemSolver7OptionsERK15GradientProblemPdPN21GradientProblemSolver7SummaryE). And while this is a reasonable thing to do, bundle adjustment problems have a special sparsity structure that can be exploited to solve them much more efficiently. Ceres provides three specialized solvers (collectively known as Schur-based solvers) for this task. The example code uses the simplest of them `DENSE_SCHUR`.

[`Problem`](http://www.ceres-solver.org/nnls_modeling.html#_CPPv4N5ceres7ProblemE) by default takes ownership of the `cost_function`, `loss_function` and `local_parameterization` pointers. These objects remain live for the life of the [`Problem`](http://www.ceres-solver.org/nnls_modeling.html#_CPPv4N5ceres7ProblemE). If the user wishes to keep control over the destruction of these objects, then they can do this by setting the corresponding enums in the [`Problem::Options`](http://www.ceres-solver.org/nnls_modeling.html#_CPPv4N5ceres7Problem7OptionsE) struct.


## 协方差计算与传递

### 基于Hessian矩阵的协方差估计方法

对于线性最小二乘问题，假设目标函数为

$$
E(\hat{X})=(Y-M\hat{X})^T(Y-M\hat{X})
$$

变量\\(\hat{X}\\)及其协方差的最优估计为

$$
\begin{aligned}
&\hat{X}=(M^TM)^{-1}M^TY \\
&C(\hat{X})=(M^TM)^{-1}\sigma^2
\end{aligned}
$$

上式提供了\\(\sigma^2\\)的无偏估计，其中\\(N\\)为观测数据的数量，\\(\dim(\cdot)\\)表示变量的维度。

$$
s^2=
\frac{E_{min}(\hat{X})}{N-\dim(X)}
$$

对上式进行二阶导求解，可以得到Hessian矩阵为

$$
\begin{aligned}
&H=\frac{\rm{d}E^2(\hat{X})}{\rm{d}\hat{X}^2}=2M^TM \\
&\rightarrow M^TM=\frac{1}{2}H
\end{aligned}
$$

代入协方差估计结果可得

$$
C(\hat{X})=(\frac{1}{2}H)^{-1}\sigma^2
$$

对于点云的ICP扫描匹配方法，其优化目标为

$$
E(\xi)
=\sum^{N}_{i=1}\|\boldsymbol e_i\|^2
=\sum^{N}_{i=1}\|\boldsymbol R\boldsymbol p_i+\boldsymbol t-\boldsymbol p_i'\|^2
$$

求解\\(E(\xi)\\)相对于\\(\xi\\)的一阶导可得

$$
\frac{\partial E(\xi)}{\partial\xi}
=2\sum^{N}_{i=1}\frac{\partial\boldsymbol e_i}{\partial\xi}^T\boldsymbol e_i
$$

其中

$$
\frac{\partial\boldsymbol e_i}{\partial\xi}=
\begin{bmatrix}
\boldsymbol I & -\boldsymbol p_i^\wedge
\end{bmatrix}
$$

在此基础上求解\\(E(\xi)\\)相对于\\(\xi\\)的二阶导，即可得到Hessian矩阵为

$$
\boldsymbol H=\frac{\partial^2E}{\partial\xi^2}
=2\sum^{N}_{i=1}
\begin{bmatrix}
\boldsymbol I & \boldsymbol p_i^{\wedge T} \\
\boldsymbol p_i^\wedge & \boldsymbol p_i^\wedge\boldsymbol p_i^{\wedge T}
\end{bmatrix}
$$

### 代码实现方案

```c++
Eigen::Matrix<float,6,6> ICPRegistration::GetCovariance()
{
//	CloudData::CLOUD_PTR cloud;
//	cloud=icp_ptr_->getInputSource();
	Eigen::Matrix<float,6,6> hessian;
	hessian.setZero();
	for(int i=0;i<input_source_->size();i++)
	{
		Eigen::Vector3f p(input_source_->at(i).x,input_source_->at(i).y,input_source_->at(i).z);
		Eigen::Matrix3f p_skew=Converter::toSkewSym(p);
		hessian.block<3,3>(0,0)+=Eigen::Matrix3f::Identity();
		hessian.block<3,3>(0,3)+=-p_skew;
		hessian.block<3,3>(3,0)+=p_skew;
		hessian.block<3,3>(3,3)+=-p_skew*p_skew;
	}
	return hessian.inverse() * GetFitnessScore() / (input_source_->size()-6);
}
```


## 相关链接

代码：
- [pose-graph](https://github.com/sunqinxuan/pose-graph)
- [semantic-gicp](https://github.com/sunqinxuan/semantic-gicp)