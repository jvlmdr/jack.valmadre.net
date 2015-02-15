---
layout: page
title:  General trajectory prior for non-rigid reconstruction
math:   true
---

<a href="paper.pdf">Paper</a>,
<a href="poster.pdf">Poster</a>

Consider a 3D object deforming non-rigidly over time, for example:

<iframe width="560" height="315" src="http://www.youtube.com/embed/EUBbfGhD4FQ?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>

The object is observed by a single moving camera.
We want to recover the 3D structure from these 2D projection correspondences.

<iframe width="560" height="315" src="http://www.youtube.com/embed/-ttg_OPLDls?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>

Let's assume that the camera motion relative to the background can be recovered using rigid structure from motion.
With known cameras, each 2D projection of a point defines a 2x3 linear system of equations for its 3D position in that frame.

$$
\mathbf{u}_{t} = \mathbf{Q}_{t} \mathbf{x}_{t}
$$

The 1D nullspace of this system corresponds geometrically to a ray departing from the camera centre.

<iframe width="560" height="315" src="http://www.youtube.com/embed/Lt8YT9ADrCA?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>

This means that using only projection constraints, there are infinite solutions for each point in each frame (anywhere along those rays).
However, we know intuitively that points should not jump around arbitrarily from frame to frame.
Assuming that the points have mass, their motion should be smooth (since they must be accelerated by a finite force).
This notion was leveraged by <a href="http://cvlab.lums.edu.pk/nrsfm/">Akhter et al (2008)</a>, who required that the trajectory of each point be expressed as a sum of low frequency sinusoids i.e. lie on the subspace of a truncated DCT basis.

Stacking the equations for the observation of a point in all F frames yields a 2Fx3F system.

$$
\begin{bmatrix} \mathbf{u}_{1} \\ \vdots \\ \mathbf{u}_{F} \end{bmatrix}
= \begin{bmatrix} \mathbf{Q}_{1} \\ & \ddots \\ & & \mathbf{Q}_{F} \end{bmatrix}
  \begin{bmatrix} \mathbf{x}_{1} \\ \vdots \\ \mathbf{x}_{F} \end{bmatrix},
\qquad
\mathbf{u} = \mathbf{Q} \mathbf{x}
$$

Introducing a K-dimensional DCT basis for the x, y and z components of the trajectory, the system of equations becomes 2Fx3K.

$$
\mathbf{x}^{*} = \mathbf{\Theta} \boldsymbol{\beta}^{*},
\qquad
\boldsymbol{\beta}^{*} = \arg \min_{\boldsymbol{\beta}} \; \left\lVert \mathbf{u} - \mathbf{Q} \mathbf{\Theta} \boldsymbol{\beta} \right\rVert^{2}
$$

While this problem is not necessarily underconstrained if 3K &le; 2F, <a href="http://www.cs.cmu.edu/~hyunsoop/trajectory_reconstruction.html">Park et al (2010)</a> found that significant camera motion was also required to obtain an accurate reconstruction, establishing the notion of <em>reconstructability</em>.
The following figure shows reconstruction error versus camera speed for varying basis size K, generated from a synthetic experiment in which human motion capture sequences (of 100 frames) are observed by an orbiting camera.

<img src="/static/projects/trajectory-filters/dct.png" />
<img src="/static/projects/trajectory-filters/dct-legend.png" />

The key observation here is that faster moving cameras enable more accurate reconstruction.
However, you have to choose the basis size correctly or else you will either run out of capacity to represent the points' motion (to the right) or revert to an ill-posed problem (to the left).

Park et al defined a measure of reconstructability depending on how well the camera trajectory was represented by the basis, since, if the camera centre lies on the basis, its trajectory is a trivial solution.
We defined a new measure based on a theoretical bound on the reconstruction error.
Our measure does not depend on the camera centre, rather, it incorporates the <a href="http://en.wikipedia.org/wiki/Condition_number">condition number</a> of the linear system of equations, the ratio of the largest eigenvalue to the smallest.

The implications of this become clearer when we alternatively consider minimising the distance of the trajectory from the DCT subspace, subject to the 2D projections being exactly satisfied.

$$
\mathbf{x}^{*} = \arg \min_{\mathbf{x}} \; \left\lVert \left(\mathbf{I} - \mathbf{\Theta} \mathbf{\Theta}^{T}\right) \mathbf{x} \right\rVert^{2}
\; \text{s.t.} \; \mathbf{u} = \mathbf{Q} \mathbf{x}
$$

Since the DCT matrix is rank 3K, the orthogonal projection matrix in the objective will have a nullspace of dimension 3K.
When we reduce K, we reduce the size of this nullspace and therefore reduce the likelihood of having a poorly-conditioned system.
Consider the more general form

$$
\mathbf{x}^{*} = \arg \min_{\mathbf{x}} \; \mathbf{x}^{T} \mathbf{M} \mathbf{x}
\; \text{s.t.} \; \mathbf{u} = \mathbf{Q} \mathbf{x}.
$$

We propose that the conditioning problem can be mostly avoided by choosing

$$
\mathbf{M} = \mathbf{G}^{T} \mathbf{G},
\qquad \mathbf{G} \mathbf{x} = \mathbf{g} * \mathbf{x}
$$

where multiplication by the matrix G is equivalent to convolution with the filter g (note that we define vector convolution to operate independently in x, y, z.)
We typically choose simple finite difference filters: some combination of (-1, 1) and (-1, 2, -1).
If the filter has support m, then the matrix M has a nullspace of size 3(m-1), since we only compute the convolution with parts of the signal where the two inputs overlap completely (i.e. "valid" mode in <a href="http://www.mathworks.com/help/matlab/ref/conv.html">Matlab's conv() function</a>).

Using these &ldquo;trajectory filters,&rdquo; we are able to achieve 3D error at the limit of reconstructability without having to choose the basis size K.
<img src="/static/projects/trajectory-filters/dct-filters.png" /><img src="/static/projects/trajectory-filters/dct-filters-legend.png" />

The reason that this works becomes evident examining the eigenspectra of the DCT matrix (left) versus the first-difference (middle) and second-difference (right) filters.
The filters do not have the many zero eigenvalues which cause the system to become poorly conditioned.

<img src="/static/projects/trajectory-filters/basis-spectrum.png" />
<img src="/static/projects/trajectory-filters/first-diff-spectrum.png" />
<img src="/static/projects/trajectory-filters/second-diff-spectrum.png" />

A nice twist here is that the Discrete Cosine Transform diagonalises symmetric convolution in an analogous way to the Fourier transform diagonalising periodic convolution, so the eigenspectra above are actually the DCT transform of each filter.
This means we can enforce the filters as a weighting in the DCT domain, although in practice it is usually more efficient to work in the time domain because the systems are sparse.
This also makes it possible to reveal the filter which is equivalent to using a particular truncation of the DCT basis.
<img src="/static/projects/trajectory-filters/basis-impulse-wsws.png" />

The final results from our example are shown below.
Red points denote the output and black the ground truth.

<iframe width="560" height="315" src="http://www.youtube.com/embed/UrWNdChPI3s?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>
<iframe width="560" height="315" src="http://www.youtube.com/embed/8r4WHDzrfV4?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>
<iframe width="560" height="315" src="http://www.youtube.com/embed/ZdH7OKfyWhM?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>
<iframe width="560" height="315" src="http://www.youtube.com/embed/MwpkZucILTQ?rel=0&showinfo=1&fs=1&modestbranding=1" frameborder="0" allowfullscreen></iframe>

### Code

I've tried to provide sufficient code and data to reproduce the figures in the paper.
It's all Matlab.
Never tested with GNU Octave.
This code is provided for free use, no warranty whatsoever.
<ul>
<li><a href="/static/projects/trajectory-filters/trajectory-filters.tar.gz" />trajectory-filters.tar.gz</a></li>
</ul>

Run <tt>setup.m</tt> to set the path as required.

The main figures (reconstruction error versus camera speed) can be generated by running <tt>solver_experiment.m</tt>.
Note that averaging these experiments over as many trials as we did probably requires a compute cluster or significant hours.
You can reduce the number of trials though, for slightly less-smooth curves.

This experiment depends on Honglak Lee et al's <a href="http://ai.stanford.edu/~hllee/softwares/nips06-sparsecoding.htm">implementation of their feature-sign search algorithm</a> for the comparison to sparse coding methods.
Ensure that <tt>l1ls_featuresign.m</tt> is found in <tt>src/lee-2006/</tt>.

I'm also providing the exact 100-frame mocap sequences from the <a href="http://mocap.cs.cmu.edu/">CMU Motion Capture Database</a> which we used.
Thanks to <a href="http://www.ict.csiro.au/staff/mark.cox/">Mark Cox</a> for converting them to point clouds.
Put this file in <tt>data/</tt> before running <tt>solver_experiment.m</tt>.
<ul>
<li><a href="/static/projects/trajectory-filters/mocap-data.mat" />mocap-data.mat</a> (43MB)</li>
</ul>

The actual reconstruction code is super simple and happens in <tt>reconstruct_filters.m</tt> and <tt>reconstruct_linear.m</tt>.
The linear systems for projection constraints are constructed in <tt>trajectory_projection_equations.m</tt>, <tt>projection_equation.m</tt> and <tt>independent_to_full.m</tt>.

The videos on this webpage are generated by running <tt>filters_demo.m</tt>.
To save the movies you'll need the <a href="http://www.imagemagick.org/">ImageMagick convert utility</a> and <a href="http://www.ffmpeg.org/">ffmpeg</a> (both free).

The qualitative examples from real photos are generated by running <tt>real_scene_demo.m</tt>.
To do this, you'll need to download the &ldquo;Real scene&rdquo; data from <a href="http://www.cs.cmu.edu/~hyunsoop/trajectory_reconstruction.html">Hyun Soo Park's project page</a> and unzip it to <tt>data/RealSceneData/</tt>.
I've also re-distributed two small functions from his code in <tt>src/park-2010/</tt>.
