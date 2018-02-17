---
title:  "Rotations"
---

# Transforms!

Watch 3blue1brown videos

### Rotation matrices (aka Direction Cosine Matrices)

_Notation_

$R_G^F$ is "frame F expressed in frame G". The columns of this matrix are the basis vectors ($x,y,z$ axes) of frame F expressed in frame G, and the rows are the G basis vectors re-expressed in F:

$R_G^F = [F_{x_G}, F_{y_G}, F_{z_G}] = [G_{x_F}; G_{y_F}; G_{z_f}]$, where $F_{x_G}=F_x\cdot G_x$

Multiplying by a rotation matrix = projecting onto another set of orthogonal basis vectors.

* See [Drake documentation](http://drake.mit.edu/doxygen_cxx/group__multibody__spatial__pose.html) for good review
* columns of rotation matrix are orthonormal vectors (orthogonal vectors (dot product zero between each of them), norm of 1)
    * set of all orthogonal matrices of size n with determinant +1 forms the special orthogonal group $SO(n)$
    * multiplication of a vector by a rotation matrix is finding the projection of the vector onto each of these basis vectors
    - since matrices are orthogonal, $R^{-1} = R^T$
* right-multiply for operations in local frame, left-multiply for operations in global frame
    - Doing Transform 2 in local frame after Transform 1 (in global frame) is the same as doing Transform 2 in global frame first, then Transform 1 in global frame
    * [slides reviewing left/right multiplication of transformation matrices](http://web.cse.ohio-state.edu/~wang.3602/courses/cse5542-2013-spring/6-Transformation_II.pdf)

### Quaternions

* composed of one real part and three imaginary parts
* Quaternions are related to the axis-angle representation $\{v_x,v_y,v_z,\theta\}$ with: $\{w,x,y,z\} = \{cos(0.5\theta),v_x sin(0.5\theta),v_y sin(0.5\theta),v_z sin(0.5\theta)\}$

* [Mirroring quaternions](https://stackoverflow.com/questions/32438252/efficient-way-to-apply-mirror-effect-on-quaternion-rotation)

### Tools

* [Eigen's geometry module](https://eigen.tuxfamily.org/dox/group__TutorialGeometry.html)
    * I wrote a rotation frame visualizer for this with GNU Plot. Largely useless except for sanity checks
