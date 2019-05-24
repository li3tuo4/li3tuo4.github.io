# TODOs and Thoughts

## How to accelerate AI/ML domain

### Linear Algebra Package (LAPACK) and Basic Linear Algebra Subprograms (BLAS)
These two are the major math libraries used in ML for linear algebra and matrix operations. 
This is suggested by Python acceleration suggestions (hardware acceleration is typically applied to these two packages).
The reason why I think about accelerating python is from the example given by the Turing Award lecture (Patterson and Hennessy).
Apparently, BLAS is a prerequisite of Caffe. 

#### Try to build Caffe on Rocket-chip
Caffe has two variants of programming interface: python Caffe and MATLAB Caffe. 
Caffe also has two targets based on hardware: 
[cuDNN](https://developer.nvidia.com/cudnn) Caffe (integrate NVIDIA cuDNN, faster) and CPU-only Caffe.

### HLS for TPU?
Customize and quick deployment of a cloud TPU on AWS F1? Regarding this track, 
check out [open TPU](https://github.com/UCSBarchlab/OpenTPU) project.
