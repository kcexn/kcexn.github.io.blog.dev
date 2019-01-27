---
layout: post
title: Final Year Project
category: study
date: 2019-01-25 12:44
lead: "Over the course of 2018 I worked 
on my final year project at Monash University, with a focus 
on wireless communication."
---

Other universities and other degrees will also call it
the capstone project, or perhaps, an honours project. This
was definitely one of the most educational work I did while I was at
university. The project challenged me to develop research skills,
especially skills related to reading technical documents (journal papers,
white papers, reports, theses etc.).

My supervisor and I decided to
investigate how adaptive filtering
techniques can be used to correct slowly time varying distortions
introduced by the wireless communication channel in a time varying
environment. My full report can be read
[here](https://drive.google.com/open?id=1kEsqf81pFY29jzULkzd2qBHHLghtqtLW)
although it could be a bit of a slog at around 60 pages long.
I'm hoping
that this short article can provide a kind of introduction to my project
without digging too much into any kind of details and provide a general
sense of what was learned.

### Time Varying Nature of Wireless Channel Distortion

The first requirement was really to understand what the wireless
communication channel is and how it works. There are two characteristics
of the link between our laptop and the wifi router or our smart phone
and the base station that are interesting. The first is that the link
is usually not "line of sight", e.g. as I write this, my laptop is not in
the
same room as the router, so somehow the signal has to bounce its way
around my apartment to get to me. This obviously impairs the wifi signal
and introduces distortion. The second interesting characteristic to me, is
that the signal is probably going to be a little time-varying, my laptop
is not really that steady on my knees and my girlfriend moving around the
apartment changes the way the wifi signal reaches me from moment to
moment. It's this kind of a wireless communication link that I chose to
study for my project, one that was impaired due to obstacles in the way
and varied quite slowly with time. My supervisor and I thought it would
be worth investigating how well an adpative receiver filter would perform
in such an environment without re-training the filter coefficients
periodically.

There are a number of ways to model this kind of distortion. I chose to
use a [Rayleigh fading](https://en.wikipedia.org/wiki/Rayleigh_fading)
model, since it's fairly well understood and wasn't super hard for me
to implement a simulation in [MATLAB](https://www.mathworks.com/).
Essentially I chose to simulate my environment as a finite impulse
response filter where the filter coefficient magnitudes vary following a
Rayleigh distribution I also had a bit of a look at how Rician distribution
performed as well.

#### Finite Impulse Response Filter

The finite impulse response (FIR) filter is something I think that anyone
who did a signal processing subject at university probably can vaguely
remember. I did signal processing two years prior to this project, and it
also wasn't one of my best subjects, so things were definitely a little
fuzzy to begin with. But it really isn't a terribly complicated idea, and
the filter can be visualised as a tapped delay line as shown below.
<image title="finite impulse response filter" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/FIR_Filter.svg/500px-FIR_Filter.svg.png" alt="finite impulse response filter" class="img-fluid mb-3" style="background: #fff" />
In the context of my problem where signals suffer from
[multipath propagation](https://en.wikipedia.org/wiki/Multipath_propagation)
, `x[n]` is the output of the transmitter at a discrete time `n` and each
delay (`z^(-1)`) represents the extra excess delay introduced by each
of the paths in multipath propagation model. A discrete and finite model
like this is clearly simplified from the reality, my wifi router obviously
doesn't transmit along a discrete set of paths to my laptop. However, it's
a useful way to model the distortion phenomena and can be used for
understanding how the receiver can be improved. Importantly, in practice,
this model quite closely approximates the true behaviour of a wireless
channel and can be used as a model for the digital signal processing to
undo the distortion.

### Signal Processing

Since I'm modelling the channel as a finite impulse response filter, the
received signal can be thought of as a some kind of signal containing
information being filtered and having some additional white noise added
to it. In this case, a simple solution would be to find the inverse filter
to undo the channel effects. A true inverse filter would be the
[zero forcing](https://en.wikipedia.org/wiki/Zero_forcing_equalizer)
filter. In my simulations and experiments this filter turned out to be a
reasonable filter, however, because it suffers from the noise
amplification phenomenon, only effective at low signal-to-noise (SNR)
ratios. My second approach was to try a minimum mean square error (MMSE)
filter, which doesn't truly invert the channel filter, but instead
estimates a solution that minimises the mean of the square of the error
between the desired outputs and the output of the corrective filter
undoing the channel effects.

#### Adaptive Filtering

I chose to look into the [least mean square filter](https://en.wikipedia.org/wiki/Least_mean_squares_filter)
as the filter of choice. The least mean square filter is one of the
simplest forms of adaptive filter, it's adaptive nature means that it
can learn the optimal corrective filter coefficients given enough
iterations. My main hope in investigating adaptive filter performance was
to see that the adaptive filter would be able to track the best filter
coefficients over time even as the channel changed.

It was great to see then after setting up experiments in MATLAB with a
variety of different channel parameters that the least mean square (LMS)
filter and one of its variants the normalised least mean square (NLMS)
filter did just this. The picture below shows the LMS filters tracking
behaviour compared to the optimal solution at a low-ish SNR.
![MSE-LMS]({{ site.baseurl }}/img/posts/2019-01-26-Final-Year-Project/MeanSquareError-LMS.png "Low SNR Performance of LMS when tracking solution")
There were a couple of notable differences between the LMS filter and the
NLMS filter that I thought were interesting. The most dramatic was the
fact that the NLMS filter simply wasn't capable of tracking the optimal
solution over time at low SNR's even though it's initialisation time was
much faster.
![MSE-NLMS]({{ site.baseurl }}/img/posts/2019-01-26-Final-Year-Project/MeanSquareError-NLMS.png "Low SNR Performance of NLMS when tracking solution")

I include many more details about how my experiments were designed, as
well as what my overall results were in my [report](https://drive.google.com/open?id=1kEsqf81pFY29jzULkzd2qBHHLghtqtLW).
There were some other
interesting behaviours that I observed such as an odd stepping away from
the optimal solution even at high SNR's that the NLMS algorithm exhibited.  
![MSE-step]({{ site.baseurl }}/img/posts/2019-01-26-Final-Year-Project/MeanSquareError-Step.png)
I produced all these simulation plots in [MATLAB](https://www.mathworks.com/products/matlab.html).
I also implemented a proof of concept on a national instruments software
defined radio.
