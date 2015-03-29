---
layout: post
title:  Disparity between training and testing sets in INRIA pedestrian dataset
---

The <a href="http://pascal.inrialpes.fr/data/human/">INRIA pedestrian dataset</a> was introduced in the famous Histograms of Oriented Gradients (HOG) <a href="http://lear.inrialpes.fr/pubs/2005/DT05/">paper of Dalal and Triggs</a> at CVPR 2005.
While they presented their results in terms of Miss Rate versus False Positives Per Window (FPPW), Piotr Doll&aacute;r has since argued in his CVPR 2009 paper and <a href="http://www.vision.caltech.edu/Image_Datasets/CaltechPedestrians/files/PAMI12pedestrians.pdf">subsequent PAMI 2012 paper</a> that it can be important to count False Positives Per Image (FPPI) not per window.
His re-evaluation of the HOG detector is <a href="http://www.vision.caltech.edu/Image_Datasets/CaltechPedestrians/rocs/InriaTestRocReasonable.pdf">available online</a>, along with the current top performers and another historic baseline, the Viola-Jones detector.
The single number which they use to rank the algorithms is the average miss rate between 0.01 and 1 FPPI (lower is better).
For the Dalal and Triggs detector, this number was 46%.
Even though there are better methods these days, I'm interested in replicating this original result.

Here's as much detail as possible about the setup:
I'm using the <a href="http://www.cs.berkeley.edu/~rbg/latent/">HOG implementation</a> from the Deformable Part Models of Felzenszwalb, Girshick, Ramanan and McAllester (FGMR).
