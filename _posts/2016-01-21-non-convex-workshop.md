---
layout:     post
title:      NIPS 2015 workshop on non-convex optimization
date:       2016-01-21 10:00:00
author:     Anima Anandkumar
visible:    false
---

While convex analysis has received much attention from the machine learning community, theoretical analysis of non-convex optimization  is still nascent. This blog as well as the recent [NIPS 2015 workshop on non-convex optimization](https://sites.google.com/site/nips2015nonconvexoptimization/home) aim to  accelerate research in this area. Along with [Kamalika Chaudhuri](http://cseweb.ucsd.edu/~kamalika/),  [Percy Liang](http://cs.stanford.edu/~pliang/),  [Niranjan U Naresh](http://www.ics.uci.edu/~numanare/), and [Sewoong Oh](http://web.engr.illinois.edu/~swoh/), [I](http://newport.eecs.uci.edu/anandkumar/) was one of the organizers of this  NIPS workshop. 

The workshop started with [my talk](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxuaXBzMjAxNW5vbmNvbnZleG9wdGltaXphdGlvbnxneDo0OGYxMDE2ZjFhNjlkNGRi) on recent progress and challenges. Many interesting machine learning tasks are non-convex, e.g. maximum likelihood estimation of  latent variable models or training  multi-layer neural networks. As the input dimension grows, the  number of [critical points](https://en.wikipedia.org/wiki/Critical_point_(mathematics)) can grow exponentially, making an analysis difficult. In contrast, convex optimization has a unique critical point corresponding to the globally optimal solution.

# Spectral methods

Spectral decomposition of matrices and tensors forms a special class of non-convex problems that admits guaranteed recovery of a globally optimal solution under transparent conditions. These conditions roughly require non-degeneracy of the parameters to be estimated. For details, see [Rong Ge’s post](http://www.offconvex.org/2015/12/17/tensor-decompositions/) on this blog, and [additional material](http://newport.eecs.uci.edu/anandkumar/tensor.html). Spectral methods have spurred much research over the past few years as witnessed by several popular workshops on spectral methods ([ICML 2014](https://sites.google.com/site/momentsicml2014/), [NIPS 2013](https://sites.google.com/site/spectrallearningworkshop/) and [ICML 2013](https://sites.google.com/site/spectralworkshopicml2013/)).  

Guarantees for tensor methods have implications for a range of machine learning problems even if, at first glance, these learning tasks appear to have nothing to do with tensors. As a case in point, tensor methods have been very effective for unsupervised learning of probabilistic latent variable models such as [topic models](http://jmlr.org/papers/volume15/anandkumar14b/anandkumar14b.pdf), [Gaussian and product mixtures](http://jmlr.org/papers/volume15/anandkumar14b/anandkumar14b.pdf), [network community models](http://newport.eecs.uci.edu/anandkumar/pubs/AnandkumarCommunityCOLT13.pdf), [mixture of ranking models](http://arxiv.org/pdf/1411.0073v1.pdf), and [latent tree graphical models](http://newport.eecs.uci.edu/anandkumar/pubs/latent-struct-param.pdf). In all these applications, suitable moment tensors (usually of third or fourth order) are decomposed to estimate the model parameters consistently with polynomial sample and computational complexity. 

## Tensor methods for neural network training

A [recent work](http://newport.eecs.uci.edu/anandkumar/pubs/NN_GeneralizationBound.pdf) shows that tensor methods provide guarantees for training neural networks, when we change the objective  to tensor decomposition, instead of the usual loss minimization. More precisely, for a network with one hidden layer and a known generative model on the input, we provide risk bounds under some conditions. Thus, by changing the objective to tensor decomposition, we can overcome the hardness of training neural networks.  Extending these results to multiple layers, as well as relaxing  the generative assumptions on the input are some of the open problems in this area. 

## Scaling up spectral and tensor methods

Spectral decomposition methods are not only of theoretical interest, they are also making an impact in many applications.  There are now many open source packages for spectral methods now, including a recent [one](https://github.com/FurongHuang/SpectralLDA-TensorSpark) on Apache Spark for distributed implementation. In addition, [randomized dimensionality methods for tensors](http://arxiv.org/pdf/1506.04448v2.pdf) are making it practical to deploy them on large datasets. [Kevin Chen](https://genfaculty.rutgers.edu/kcchen/home) gave a compelling talk on the superiority of spectral methods in genomics for training hidden Markov models (HMM)  over traditional approaches such as [expectation maximization](https://en.wikipedia.org/wiki/Expectation%E2%80%93maximization_algorithm). In particular, spectral methods provided better biological interpretation, were more robust to class imbalance, and were orders of magnitude faster compared to EM.

# Avoiding saddle points
 
Moving on to more general non-convex optimization, in my [talk](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxuaXBzMjAxNW5vbmNvbnZleG9wdGltaXphdGlvbnxneDo0OGYxMDE2ZjFhNjlkNGRi), I pointed out the difficulty in even converging to a local optimum due to the existence of *saddle points*. Saddle points are critical points which are not local minima, meaning there exist directions where the objective value decreases (for minimization problems). Saddle points can slow down gradient descent arbitrarily. Alternatively, if  Newton's method is run, it converges to an arbitrary critical point, and does not  distinguish between a local minimum and a saddle point.

## Second-order methods

One solution to escape saddle points is to  use the second order [Hessian](https://en.wikipedia.org/wiki/Hessian_matrix) information to find the direction of escape when the gradient value is small: the  Hessian eigenvectors with negative eigenvalues   provide such directions of escape. These methods, sometimes known as  *trust region methods*, and have appeared in different works, e.g. [here](https://www.computer.org/csdl/proceedings/focs/1996/7594/00/75940359.pdf) and [here](http://arxiv.org/abs/1405.4604). A beautiful [paper](http://link.springer.com/article/10.1007%2Fs10107-006-0706-8) by Nestorov proposes an objective function based on cubic regularization that automatically "switches" between first order and second order steps, and converges to a local optimum in bounded time. 

## Noise to the rescue

A recent  [work](http://arxiv.org/abs/1503.02101) presented at COLT surprisingly shows that it is possible to escape saddle points using *only* first order information based on noisy stochastic gradient descent (SGD). In many applications, this is far cheaper than (approximate) computation of the Hessian eigenvectors.  However, one unresolved issue is handling *degenerate* saddle points, where there are only positive and zero eigenvalues in the Hessian matrix. For such points, even distinguishing saddle points from local optima is hard. It is also an open problem to establish the presence or absence of  such degenerate saddle points for particular non-convex problems, e.g. in deep learning.

# Optimization landscape for deep learning

[Yann LeCun](http://yann.lecun.com/) talked about how the success of deep learning has shown that non-convexity should not be treated as an obstacle. See slides [here](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxuaXBzMjAxNW5vbmNvbnZleG9wdGltaXphdGlvbnxneDo0YmNiNjM0OGU0NTlmMDll). An open problem is to understand the optimization landscape for deep learning. While classical [works](http://www.dsi.unifi.it/~paolo/ps/pinn.pdf) have shown the presence of bad local optima even in simple low-dimensional problems, a recent [work](http://arxiv.org/abs/1412.0233) argues that the picture is quite different in high dimensions, suggesting that the loss function for training deep neural networks can be approximated as random Gaussian polynomials under a set of (strong) assumptions. A beautiful math [paper](https://projecteuclid.org/euclid.aop/1384957786) by Auffinger and Ben Arous characterizes the critical points of such random Gaussian  polynomials, proving that the objective values of all local minima concentrate in a "narrow" band, and  differ significantly from the values attained at saddle points. However, to add a word of caution, this does *not* imply that a random Gaussian polynomial  is computationally easy to optimize. Current theory does not give a good characterization of the basins of attraction for the local optima, and requires exponential number of initializations to guarantee successful convergence. Moreover, degenerate saddle points can be present, and they are hard to escape from, as I discussed earlier. 

Yann also talked about a number of other exciting developments in deep learning, including the use of asynchronous methods such as elastic average SGD for distributed computation, and compared it with the popular ADMM methods. Another topic of his talk was how to speed up inference in probabilistic models. Sparse coding models involve decoding steps that are computationally expensive to solve due to non-convexity. He contrasted such models with  [predictive sparse coding](http://yann.lecun.com/exdb/publis/pdf/koray-psd-08.pdf) models   where efficient inference is possible. A similar philosophy can also be seen behind the work of [Pedro Domingos](http://homes.cs.washington.edu/~pedrod/) on [sum-product networks](http://spn.cs.washington.edu/pubs.shtml) where inference is tractable and the goal is to train such efficient models.  

## Everything old is new again

[Andrew Barron](http://www.stat.yale.edu/~arb4/) took us back in time, and described how he got interested in neural networks when he discovered that they were training 6-8 layer neural networks back in the 1960's in his dad's company. Andrew provided a glimpse of the [classical results](http://link.springer.com/article/10.1023%2FA%3A1022650905902), which bound both the approximation and estimation errors for a shallow neural network (with one hidden layer). The approximation error is related to the  Fourier spectrum of the target function: intuitively, a smoother signal has a lower amount of high frequency content, and has a better approximation error under the class of   networks of a fixed size. The estimation bound gives the risk achieved under a finite amount of training data, but  achieving this  bound is   computationally hard. Andrew then talked about [his recent efforts](https://drive.google.com/file/d/0Bxz8_SW7cMO-Z2Nob1htbTNyUG8/view?usp=sharing) to develop computationally efficient algorithms for training neural networks based on the use of generative models of the input. 

## Do not accept the given non-convex problem as is

[Sanjeev Arora](https://www.cs.princeton.edu/~arora/) made many excellent points on overcoming the challenges of non-convexity. Unlike traditional computer science theory which has focused on characterizing worst-case problem instances, machine learning offers a much more flexible framework. In fact, even the objective function can be changed!  He focused on the problem of non-negative matrix factorization (NMF), where the original objective function of quadratic loss minimization is hard. On the other hand,  under a so-called  separability assumption, the objective  can  be changed as *finding a simplex with non-negative vertices that contains the observations*, which can be solved efficiently. A similar philosophy holds for spectral methods, which I discussed earlier, where the original objective function is abandoned, and instead spectral decompositions are employed. Sanjeev also made the excellent point that the assumptions needed for success are often something we can control as data collectors. For instance, separability holds for the NMF problem when more features are collected, e.g. for learning [latent variable PCFGs via NMF techniques](http://homepages.inf.ed.ac.uk/scohen/acl14pivot+supp.pdf). 

In a similar spirit,  [Chris Re](http://cs.stanford.edu/people/chrismre/)  showed how we can strengthen the current theory to remove some of the pessimism behind the hardness of many problems that have good solutions  in practice. He described the notion of combinatorial width or fractional hypertree width, a notion from logic, that can provide a better analysis of Gibbs sampling and belief propagation. He also talked about martingale methods for analyzing the convergence of  some mildly non-convex problems. [Gregory Valiant](http://theory.stanford.edu/~valiant/) showed that by assuming more structure on the class of probability distributions we can do much better compared to the unstructured setting. This includes topic models, hidden Markov models, and word embeddings. 

## Convex envelopes, smoothing, annealing and sum of squares

[Hossein Mobahi](http://people.csail.mit.edu/hmobahi/index2.html) talked about smoothing approaches for convexification. The [convex envelope](http://www.tandfonline.com/doi/abs/10.1080/03605309908821476?journalCode=lpde20#.VnH1dEorK00) of a function  is  the convex function that provides the tightest lower bound. The convex envelope achieves the globally optimal solution of the original objective for continuous functions. While we are mostly familiar with the characterization of convex envelopes through duality (i.e. dual of the dual is the convex envelope), its characterization through partial differential equation (PDE) has been mostly unknown to the machine learning community. Hossein introduced the PDE form for convex envelope, and gave a nice interpretation in terms of carrying out diffusion along  non-convex regions of the objective function to obtain the convex envelope.  However, the convergence time for this PDE can be in general  exponential in the input dimension, and therefore is not tractable. Hossein showed that instead we can perform Gaussian smoothing efficiently, and gave a novel characterization of Gaussian smoothing as linearization of the convex envelope. This involves analyzing the PDE of the convex envelope, and showing that its linearization results in the [heat equation](http://people.csail.mit.edu/hmobahi/pubs/gaussian_convenv_2015.pdf), which corresponds to Gaussian smoothing. 

Annealing methods are a form of  probabilistic search  for non-convex optimization, and [Andrew Barron](http://www.stat.yale.edu/~arb4/) briefly mentioned about its application to neural network training (see [here](http://www.stat.yale.edu/~arb4/publications_files/AdaptiveAnnealing.pdf)). An entirely different framework for handling non-convex optimization is via sum of squares, which involves a hierarchy of higher order semi-definite programs (SDP). We unfortunately did not cover sum of squares methods in our workshop. [Boaz Barak](http://www.boazbarak.org/) has some excellent tutorials and talks on this topic. Currently, sum of squares approaches require high computational complexity, and more research needs to be done to make them practical for solving machine learning problems; see, [this recent work](http://arxiv.org/abs/1512.02337) for progress on this front.

# Conclusion 

NP-hardness should not deter us from analyzing non-convex optimization in the
context of machine learning. We should be creative in making new assumptions,
we should try to change the problem structure and the objective function, and
collect relevant data to make the learning problem easier.

As I conclude this blog post, I am reminded of my discussion with [Leon
Bottou](http://leon.bottou.org/). He said that there are three levels of
thinking, with increasingly more sophistication. At the first level, we merely
aim to prove statements that are already formulated. Almost all formal
education focuses on this type of skill. At the second level, we try to draw
implications, given a fixed set of assumptions. At the third level, we need to
simultaneously come up with reasonable assumptions as well as their
implications, and this level requires the highest level of creativity. In the
area of machine learning, there is tremendous need and opportunity for such creativity, and I am hoping that the workshop managed to foster more of it. 