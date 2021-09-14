# Generating CloudSat Reflectivity Profiles using Global Precipitation Measurement Satellite and cGANs

## Motivation

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/cloud.png?raw=true" width="600" height="400">
</p>

Firstly, passive sensors struggle to observe vertical profiles of hydrometeors, including rain and clouds, where Bayesian retrievals, the standard formulation for rainfall algorithms, have shown to have model uncertainty. Secondly, its known that modern passive sensors can struggle with retrievals of cloud profiles. So, the big question we’re trying to answer is if deep learning can become a cheaper and alternate approach, which would ultimately allow cheaper and more available passive microwave sensors to determine various cloud properties. 

The code for this research is currently sensitive, but may be released at a later date. 

## Data

For this project, we are using two NASA satellites: CloudSat and Global Precipitation Measurement.

### NASA Global Precipitation Measurement (GPM) Microwave Imager (GMI)

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/gpm.png?raw=true" >
</p>

From GPM, we are using the GPM Microwave Imager, which provides a 13-channel (10-183 GHz) information along its swath path (885 km wide). 

### Cloudsat


<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/cloudat.jpg?raw=true">
</p>

From CloudSat, we are utilizing the 94-GHz (W-band) profiling radar, or CPR, which provides an along path 2 dimensional vertical reflectivity profile.

### Coincidence Dataset

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/coincident.png?raw=true" width="700" height="500">
</p>

The coincidence dataset is derived from the CloudSat-GPM Coincidence Dataset prepared by Joe Turk from JPL in 2016. The dataset spans from March 2014 to June 2016 and is matched within 15 minute time intervals. Though, one restriction is that this is only a daytime dataset, as CloudSat is on an ascending orbit and can only retrieve data during the day.

Above is a visual of the coincident path of both CloudSat and GPM for a few frequencies from GMI, noting the difference in ranges for each frequency. The hope is that there is enough information from each frequency to allow us to build a complete picture of the 2d reflectivity profiles. 

### Input Data Example

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/inputdata.png?raw=true">
</p>

Above image shows nearly what the actual input data will be when inputting it into the cGAN, only, instead of feeding it the entire span of each scene, we crop it into 64 by 64 pixel chunks.

## ML Model

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/gan.png?raw=true">
</p>

GANs are widely used as a deep learning method for the generation of images and includes two components: the Generator and Discriminator, both based on convolutional neural networks. The ultimate goal of a GAN is to generate or synthesize images that cannot be distinguished from real images. GANs are widely popular for their use in the generation of life-like human faces. 

cGANs are different from regular GANs because they can synthesize images based on a specific class. So, you can imagine a GAN creating images of cars, where a GAN will generate random car images, you can tell a cGAN that I want a specific car class, such as a sedan or a SUV or a pickup truck. 


## Results

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/results1.png?raw=true">
</p>

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/results2.png?raw=true">
</p>

## Evaluation

So, unfortunately, generative models, and especially in our case, are difficult to evaluate. Nevertheless, we have found a couple ways to do so. 

### Contoured Frequency by Altitude Diagram


<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/cfad.png?raw=true">
</p>

One of which is a contoured frequency by altitude diagram or CFAD, which shows the frequency of reflectivity values at different heights and then normalized by the total number of samples. CFADs are helpful because they can show us where the model is biased. In our case, we see the biggest bias at higher reflectivity values, telling us that the model is overpredicting the occurrence of convective systems at low altitudes


### Confusion Matrix-esque Graph

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/correlation.jpg?raw=true" width="500" height="400">
</p>

Another metric of accuracy we can use is the evaluation of Cloud top heights. From the confusion matrix-esque graph above, we see that the heights are positively linearly correlated, which is the hopeful expected outcome. 

### Box and Whisker Plot

<p align="center">
  <img src="https://github.com/danilopez0111/cgan-cloudsat-gpm/blob/main/images/boxwhisker.jpg?raw=true" width="700" height="700">
</p>

Here a box and whisker plot graph going a little more in depth to the relative errors for each cloud top height bin. We see a shift in error from positive to negative as cloud top height increases. This is likely due to biases within the training dataset, where most clouds are typically located in the mid to lower troposphere. This makes sense in the error, as we should expect the higher altitude cloud top heights to be underpredicted. In the future, we will expect to sort out these biases to create a model that is equally capable of producing accurate results for low clouds as well as high clouds. 

# Summary

Overall, we see a mean cloud top bias of about -105m for all scenes. The cloud top bias was calculated by first determining whether or not a pixel wide column of a scenes contains a cloud, where we know it’s an actual cloud prediction instead of an artifact by checking if two vertical pixels contain non-nan reflectivity values. Then we average the heights of all pixel columns and obtain the cloud top height for the scene. 

One of our benchmarks that we wanted to beat was the MODIS retrieval of cloud top heights, which was determined to have an average bias of -540m. So, I believe the work is on an encouraging path. 

Firstly, our model has the capability to produce spatially plausible CloudSat 2d reflectivity profiles based on GMI.  Secondly, the model has strong performance when looking at the entire dataset wholistically. Lastly, I believe that this project has the potential to showcase the usability performance of deep learning in remote sensing
 
Though, there are some weaknesses still, including some scenes where the model seems to completely miss the mark. Additionally, there are still improvements to be made in the scope of performing wider parameter sweeps, developing the model trustworthiness, incorporating more values such as MODIS, and creating more model evaluations and performing more model analysis and why the model is outputting what it is.
