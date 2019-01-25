---
layout: post
title: Final Year Project
category: study
date: 2019-01-25 12:44
---

Over the course of the university year of 2018 I worked on my final
year project. Other universities and other degrees will also call it 
the capstone project, or perhaps, an honours project. I think this 
was definitely one of the most educational work I did while I was at 
university. The project challenged me to develop research skills, 
especially skills related to reading technical documents (journal papers, 
white papers, reports, theses etc.). My supervisor and I decided to 
investigate how adaptive filtering 
techniques can be used to correct slowly time varying distortions 
introduced by the wireless communication channel in a time varying 
environment. My full report can be read 
[here](https://drive.google.com/open?id=1kEsqf81pFY29jzULkzd2qBHHLghtqtLW) 
although it could be a bit of a slog at around 60 pages long. I'm hoping 
that this short article can provide a kind of introduction to my project 
without digging too much into any kind of details.

### Time Varying Nature of Wireless Channel Distortion

The wireless communication channel between mobile phones and base 
stations, or your laptop computer and the wifi router. Is usually not 
static, this is because the mobile phone and laptop can be picked up 
and moved around, but also, because the environment between your laptop 
and the router could change, maybe your cat has decided to get up from 
the spot by the window sill to a more comfortable place in the house. The 
wireless channel is also not typically a straight line like other 
communication technologies such as twisted pair, or optical fibre, this 
introduces lots of distortion due to the wireless signal reflecting off 
and bending around objects between the sender and the receiver.

There are number of ways to model this distortion and it's time-varying 
nature. A traditional way of modelling the wireless channel distortion 
called fading, is as a finite impulse response filter with time-varying 
taps whose magnitudes vary following a Rayleigh distribution. This 
Rayleigh fading model and it's variant the Rician fading channel was how I 
chose to model this environment.

#### Finite Impulse Response Filter

The finite impulse response (FIR) filter. Can be thought of and visualised 
as a tapped delay line as shown in the following picture.  
<image title="finite impulse response filter" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/FIR_Filter.svg/500px-FIR_Filter.svg.png" alt="finite impulse response filter" class="img-fluid mb-3" style="background: #fff" />  
Where `x[n]` is the output of the transmitter at discrete time `n` and 
each delay can be thought of as a discrete time delay caused by the 
extra time taken for the signal to arrive at the receiver caused by one 
of the paths the signal takes. This is clearly a simple model for 
understanding how reflections distort a transmitted wireless signal, in 
reality, the signal arrives on a continuum of different paths, however, 
as an ideal representation of the distortion phenomena this model is 
useful for understanding how the receiver can be improved, importantly, 
in practice this model quite closely approximates the true behaviour of a 
wireless environment and can be used as a model for the digital signal 
processing to undo the distortion. A Rayleigh or Rician
distribution can be applied 
to the magnitudes of each of the taps to give the channel under simulation 
a sense of time variation.

### Signal Processing

Since the channel filter can be modeled as a finite impulse response 
filter it can be undone with an inverse FIR filter. A true inverse filter 
would be the 
[zero forcing](https://en.wikipedia.org/wiki/Zero_forcing_equalizer) 
filter. This filter was in fact in my simulations and experiment an 
effective filter, however, because the zero forcing filter suffers from 
a noise amplification phenomenon, and the received signal has added 
random noise, this filter is only effective at low signal-to-noise (SNR) 
ratios. The alternative approach I took was to try a minimum mean square 
error (MMSE) filter, which doesn't truly invert the channel filter, but 
instead minimises the mean square error between the desired output and 
the output of the corrective filter undoing the channel effects as much 
as possible without amplifying random noise.

#### Adaptive Filtering

The adaptive part of adaptive filtering means that instead of calculating 
an optimal MMSE filter prior to undoing the channel effects, we let the 
filter figure out the best solution. This is a very rudimentary form of 
machine learning, in fact, the 
[least mean square filter](https://en.wikipedia.org/wiki/Least_mean_squares_filter), arguably the 
simplest adaptive filter operates very similarly to the 
[perceptron](https://en.wikipedia.org/wiki/Perceptron) of a neural net. The
main advantage of using an adaptive filter in a slowly time-varying 
scenario is that if the assumption is made that most incoming signals will 
be classified correctly, an adaptive filter can track the minimum mean 
square error solution over time as the wireless channel changes.

The least mean square filter and one of it's variants the normalised least 
mean square filter are what I investigated in my project. Both estimate 
the optimal filter coefficients to find the minimum mean square error 
solution, but do it in slightly different ways with slightly different 
results. I found that the least mean square filter performed slightly 
better at tracking solutions particularly in lower SNR regions.  
![MSE-LMS]({{ site.baseurl }}/img/posts/2019-01-25-Final-Year-Project/MeanSquareError-LMS.png "Low SNR Performance of LMS when tracking solution")  
And that the normalised variant was much faster at finding a solution but 
is not suitable for tracking time variation at lower SNR's.  
![MSE-NLMS]({{ site.baseurl }}/img/posts/2019-01-25-Final-Year-Project/MeanSquareError-NLMS.png "Low SNR Performance of NLMS when tracking solution")  
I include many more details about how my experiments were designed, as 
well as what my overall results were in my [report](https://drive.google.com/open?id=1kEsqf81pFY29jzULkzd2qBHHLghtqtLW). 
There were some other 
interesting behaviours that I observed such as an odd stepping away from 
the optimal solution even at high SNR's that the NLMS algorithm exhibited.  
![MSE-step]({{ site.baseurl }}/img/posts/2019-01-25-Final-Year-Project/MeanSquareError-Step.png)
I produced all these simulation plots in [MATLAB](https://www.mathworks.com/products/matlab.html). 
I also implemented a proof of concept on a national instruments software 
defined radio.




