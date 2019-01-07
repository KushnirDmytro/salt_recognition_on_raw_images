# salt_recognition_on_raw_images
course project on "Basics of data processing" cource from UCU bachelour programme. 

# Content:
1) Description of method used.
2) Description of data and problem conserned.
3) Method to compare described.
4) Final results.

# How to reproduce:
1) Install Haralic Toolbox according to tutorial from https://www.orfeo-toolbox.org/CookBook/Installation.html 
additionally you may need to have .tiff files processing library installed on your system.
2) Prepare data via downloading from sources and unpacking into '/data' directory in the same directory as working script.
3) Launch notebook.
3) To reproduce deep convilutional network approaches use recomendations from stated repository: [#TODO Dzvinka's repo].

# ?

Project aim: compare state-of-art approach to segmentaion of seismic images with such a classic approach as application of Grayscale Covariance matrix and Haralic textural features.

The source of dataset is https://www.kaggle.com/c/tgs-salt-identification-challenge/ and 'state-of-art' approach is one of the award-winning models from the result of named competiotion.

#TODO add link to implimentation used

https://www.kaggle.com/c/tgs-salt-identification-challenge/data

Kaggle API download: 'kaggle competitions download -c tgs-salt-identification-challenge'

For final accuracy evaluations had been used 'late submission' tool from mentionned Kaggle competition.

# List of refferences:
[1] Haralick, R.M., K. Shanmugan, and I. Dinstein, "Textural Features for Image Classification", IEEE Transactions on Systems, Man, and Cybernetics, Vol. SMC-3, 1973, pp. 610-621.

[2] Haralick, R.M., and L.G. Shapiro. Computer and Robot Vision: Vol. 1, Addison-Wesley, 1992, p. 459
