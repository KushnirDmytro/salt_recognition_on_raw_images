# salt_recognition_on_raw_images
course project on "Basics of data processing" cource from UCU bachelour programme. 

# Content:
### 1. Description of data and problem conserned.
### 2. Description of method used.
### 3. Method to compare described.
### 4. Obtained results.
### 5. How to reproduce.
### 6. Conclusion.



#               1. Description of data and problem concerned

  ## Problem description

### Project aim: 
Compareing the state-of-art approach to segmentaion of seismic images with such a classic approach as application of Grayscale Covariance matrix and Haralic textural featuresх [1,2].

### The source of dataset:
is https://www.kaggle.com/c/tgs-salt-identification-challenge/ and 'state-of-art' approach is one of the award-winning models from the result of named competiotion.

https://www.kaggle.com/c/tgs-salt-identification-challenge/data

Kaggle API download: 'kaggle competitions download -c tgs-salt-identification-challenge'


  ## Motivation

Several areas of Earth with large accumulations of oil and gas also have huge deposits of salt below the surface.
But unfortunately, knowing where large salt deposits are precisely is very difficult. Professional seismic imaging requires manual labeling of a data, which is time consuming, expensive, not scalable to bigger amounts of data and often results in subjective, highly variable renderings. More alarmingly, it leads to potentially dangerous situations for oil and gas company drillers.  
Therefore, automatic and accurate salt regions segmentation seems like a worthwhile enterprise.

  ## Data analysis
    
A seismic image is produced from imaging the reflection coming from rock boundaries, caused by a controlled seismic source of energy, such as compressed air or a seismic vibrator. Since usually salt is an amorphous rock without much internal structure there is typically not much reflectivity inside the salt, unless there are sediments trapped inside it. (Therefore no clear pattern)The unusually high anisotropy of salt velocities can also create problems with seismic imaging. [11]
The dataset consists of images cropped from the larger-scale seismic snapshot into 101 x 101 pixels images and each pixel is classified as either salt or sediment. In addition to the seismic images, each sample is accompanied with depth value on which it was taken. 4000 images with salt masks are available for training and 18000 are used for testing (no ground-truth masks provided).

![seismic_example](https://user-images.githubusercontent.com/17781705/50963982-d32cdb80-14d6-11e9-9818-5e5beabc70c7.png)


#                 2. Description of main method used
Rem: tutorial and explanatory materials from [https://prism.ucalgary.ca/handle/1880/51900]

What is texture? 
Everyday texture terms - rough, silky, bumpy - refer to touch.

### A texture that is rough to touch has:

- a large difference between high and low points, and 
- a space between highs and lows approximately the same size as a finger.

Silky would have little difference between high and low points, and the differences would be spaced very close together relative to finger size.

Image texture works in the same way, except the highs and lows are brightness values (also called grey levels, GL,  or digital numbers, DN) instead of elevation changes. Instead of probing a finger over the surface, a "window" - a (usually square) box defining the size of the probe - is used.

### Textures in images quantify:

 - Grey level differences (contrast)
 - Defined size of area where change occurs (window)
 - Directionality or lack of it.
 
### Order:

The GLCM described here is used for a series of "second order" texture calculations.

First order texture measures are statistics calculated from the original image values, like variance, and do not consider pixel neighbour relationships.
Second order measures consider the relationship between groups of two (usually neighbouring) pixels in the original image.
Third and higher order textures (considering the relationships among three or more pixels) are theoretically possible but not commonly implemented due to calculation time and interpretation difficulty.

This matrix is square with dimension Ng, where Ng is the number of gray levels in the image. Element [i,j] of the matrix is generated by counting the number of times a pixel with value i is adjacent to a pixel with value j and then dividing the entire matrix by the total number of such comparisons made. Each entry is therefore considered to be the probability that a pixel with value i will be found adjacent to a pixel of value j. 

### Spatial relationship between two pixels:
GLCM texture considers the relation between two pixels at a time, called the reference and the neighbour pixel. In the illustration below, the neighbour pixel is chosen to be the one to the east (right) of each reference pixel. This can also be expressed as a (1,0) relation: 1 pixel in the x direction, 0 pixels in the y direction.

![grayscale_neigbours](https://user-images.githubusercontent.com/17781705/50964093-1edf8500-14d7-11e9-9b9a-2aac29cdf84b.gif)

The following figure shows how graycomatrix calculates several values in the GLCM of the 4-by-5 image I. Element (1,1) in the GLCM contains the value 1 because there is only one instance in the image where two, horizontally adjacent pixels have the values 1 and 1. Element (1,2) in the GLCM contains the value 2 because there are two instances in the image where two, horizontally adjacent pixels have the values 1 and 2. graycomatrix continues this processing to fill in all the values in the GLCM.

![referenceetoh38](https://user-images.githubusercontent.com/17781705/50964101-22730c00-14d7-11e9-8280-4305829e2f87.gif)

#                3. Method to compare described

### CNN: [in collaboration with Dzvenymyra-Marta Yarish]
  In recent years, neural networks have found successful application in various tasks and problems. Solution of those tasks require the construction of good internal representations of the world (or ”features”), which must be invariant to irrelevant variations of the input while preserving relevant information. A major question for machine learning is how to learn such good features automatically. In our case it is how to learn the distinctive features of salt and other sediments, which do not depend on brightness, orientation etc. Convolutional Neural Networks (CNNs) are a biologically-inspired trainable architecture that can learn such invariant features.
  Each stage in a CNN is composed of several filter layers, some non-linear transformations, and feature pooling layers. With multiple stages, a CNN can learn multi-level hierarchies of features.[11] which proved to be highly effective representations of data for computer vision tasks, such as image classification, segmentation and object detection.  One of the main problems with using CNNs for segmentation is pooling layers. Pooling layers increase the field of view and are able to aggregate the context while discarding the ‘where’ information. However, semantic segmentation requires the exact alignment of class maps and thus, needs the ‘where’ information to be preserved. Two different classes of architectures evolved in the literature to tackle this issue.
  Architectures in the first class use what are called as dilated/atrous convolutions[12] and discard pooling layers.
  Second one is encoder-decoder architecture. Encoder gradually reduces the spatial dimension with pooling layers and decoder gradually recovers the object details and spatial dimension. There are usually shortcut connections from encoder to decoder to help decoder recover the object details better. U-Net is a popular architecture from this class[10]. In our approach we chose to use U-Net architecture, as it enables us to easily change and experiment with different, possibly pretrained encoder types, leaving the decoder unchanged. 
Concerning the architecture of encoders we explored, three are worth mentioning here:
 - Res-Net-34[8]
 - Dense-Net-121 [9]
 - SE-ResNext-50[10]  
 
The motivations behind ResNet and DenseNet are quite similar: to allow training of much deeper nets tackling the problem of vanishing gradient. This is an important issue, since deep nets are more capable of good enough mapping approximation. ResNet achieves this by exploiting the idea that the residual mapping F(x):=H(x)−x is easier to optimize than the original, unreferenced mapping H(x).  x here is realised through introducing skip-connections in feed-forward neural network, which perform identity mapping.



#                 4. Final results

As we have lots of pixels in those iamges, processing them with XGboost is very time and computational consuming.
To get the best features subset we used Forvard Features Selection approach to get rid of extensive computing

![image](https://user-images.githubusercontent.com/17781705/51254363-9e72c580-19a9-11e9-838a-48b6ef52542a.png)

Using approach proposed in [3] we've took features with parameter radius 3, 9, 18  in order to grasp features-over-neighbourhood in a manner as CNN grasps surrounding context with convoluitions.


![prediction_example](https://user-images.githubusercontent.com/17781705/50965143-d1b0e280-14d9-11e9-82d1-257c15d1ef7c.png)


metric used is IoU score or 'Jaccard similarity'
![IoU](https://upload.wikimedia.org/wikipedia/commons/c/c7/Intersection_over_Union_-_visual_equation.png)

### Application example:
![image](https://user-images.githubusercontent.com/17781705/51253363-b9900600-19a6-11e9-9e1c-c0a100163c32.png)

## Examples of exatracted features:
### (format: <subclass of features>_<window_size>_<feature_name>_<features_index_in_tiff_file>)
  
![image](https://user-images.githubusercontent.com/17781705/51253554-4a66e180-19a7-11e9-9dad-8fc1a74226e0.png)
![image](https://user-images.githubusercontent.com/17781705/51253898-530be780-19a8-11e9-9d69-653ecaf3a345.png)


### Resulting 'best' features rankings:
![image](https://user-images.githubusercontent.com/17781705/51252853-8e58e700-19a5-11e9-94c5-94adcdf7d622.png)


### Results from Kaggle Top competitors (ansembling and postprocessing used):
![image](https://user-images.githubusercontent.com/17781705/51253732-d2e58200-19a7-11e9-8d2c-20d0f5869003.png)

### Result from one CNN model iou 0.857432
### Result from our model: (obtained on 100 images) 0.856856


#                5. How to reproduce
1) Install Haralic Toolbox according to tutorial from https://www.orfeo-toolbox.org/CookBook/Installation.html 
additionally you may need to have .tiff files processing library installed on your system.
2) Prepare data via downloading from sources and unpacking into '/data' directory in the same directory as working script.
3) Launch notebook.
3) To reproduce deep convilutional network approaches use recomendations from stated repository: [https://github.com/DzvinkaYarish/kaggle_tgs_salt_challenge].


#               6. Conclusion:

## Comparison table:
![image](https://user-images.githubusercontent.com/17781705/51252268-0e7e4d00-19a4-11e9-8920-c35d1f4e892a.png)

## Overall:
Statistical-based image preprocessing is still great for image 
Competitive data science and real-world tasks may not use the same notion of state-of-art

## Future work:
- automated postprocessing
- exhaustive search of best parameters for generating features
- XGBoost fine-tuning

# List of refferences:
[1] Haralick, R.M., K. Shanmugan, and I. Dinstein, "Textural Features for Image Classification", IEEE Transactions on Systems, Man, and Cybernetics, Vol. SMC-3, 1973, pp. 610-621.

[2] Haralick, R.M., and L.G. Shapiro. Computer and Robot Vision: Vol. 1, Addison-Wesley, 1992, p. 459

[3] Ferreira, Rodrigo Da Silva et al. “Multi-scale Evaluation of Texture Features for Salt Dome Detection.” 2016 IEEE International Symposium on Multimedia (ISM) (2016): 632-635. [URL: http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7823702&isnumber=7823367]

[4] Hegazy, T., & AlRegib, G. (2014, October 29). Texture Attributes for Detecting Salt Bodies in Seismic Data. Society of Exploration Geophysicists. [presentation: https://pdfs.semanticscholar.org/d87c/5ff49a870dbd8087206169f142f595f45f85.pdf]

[5] Berthelot, Angélique & Solberg, Anne & Gelius, Leiv-J. (2013). Texture attributes for detection of salt. Journal of Applied Geophysics. 88. 52-69. 10.1016/j.jappgeo.2012.09.006. [https://pdfs.semanticscholar.org/2d7b/cf86509cff8460fd916df564e3710a0e7d7c.pdf]

[6]  Aina J. Bugge, Stuart R. Clark, Jan E. Lie, and Jan I. Faleide (2018). ”A case study on semiautomatic seismic interpretation of unconformities and faults in the southwestern Barents Sea.” Interpretation, 6(2), SD29-SD40.
[https://doi.org/10.1190/INT-2017-0152.1 ]

[7]  Xinming Wu (2016). ”Methods to compute salt likelihoods and extract salt boundaries from 3D seismic images.” GEOPHYSICS, 81(6), IM119-IM126.[https://doi.org/10.1190/geo2016-0250.1 ]

[8] Olaf Ronneberger, Philipp Fischer & Thomas Brox (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. CoRR, abs/1505.04597. [https://arxiv.org/abs/1505.04597] 

[9] Gao Huang, Zhuang Liu, Laurens van der Maaten, Kilian Q. Weinberger. (2016). Densely Connected Convolutional Networks. , abs/1608.06993    [https://arxiv.org/abs/1608.06993]

[10] Jie Hu (2017). Squeeze-and-Excitation Networks. CoRR, abs/1709.01507 [https://arxiv.org/abs/1709.01507] 

[11] Ilya Loshchilov & Frank Hutter (2016). SGDR: Stochastic Gradient Descent with Restarts. CoRR, abs/1608.03983 [https://arxiv.org/abs/1608.03983] 

[12] Gao Huang, Yixuan Li, Geoff Pleiss, Zhuang Liu, John E. Hopcroft Kilian Q. Weinberger (2017). Snapshot Ensembles: Train 1, get M for free. CoRR, abs/1704.00109, [https://arxiv.org/abs/1704.00109] 

# Libraries and implementations used:

1. Orfeo Toolbox (Textural Features extraction) https://www.orfeo-toolbox.org/packages/nightly/latest/CookBook-stable/index.html
2. XGboost - desision trees
3. Pandas - data organisation processing
3. ScikitLearn - other data processing utilities
4. OpenCV - image processing
