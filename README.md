# Scene Labeling
This code base contains the S2T system constructed in CITE PAPER.

This project was created and tested using Python version 3.10. 

This code is free to use but the authors ask that if you make use of any of the code during research you cite the work using PAPER CITATION. 

# Installation 
## Virtual environment
The codebase was created using Python virtual environments. These environments are recommended as they will not install packages directly to your system. A Python virtual environment can be created using the following command from a terminal: 

`python -m venv venv` 

## YOLOv3 Object Detection Model 
The code base uses the YOLOv3 object detection model. Due to size constraints on the repository, this model could not be uploaded. The model can be downloaded from the [YOLOv3 site](https://pjreddie.com/darknet/yolo/). 

The files required are: 
- [yolov3.weights](https://pjreddie.com/media/files/yolov3.weights)

The code base originally stored these files in the input/models/yolo/ directory alongside the yolov3.cfg file.

# Usage 
The first script to execute is the `import_coco.py` script. This script will download the specified number of images
from the COCO 17 data set. These images will be saved in the coco_images/ directory. This repository contains example 
output for each of the three stages of the pipeline as ran on 25 sample images. These files are: 

- input/object_detection.csv
- input/person_object_detection.csv
- input/metadata.csv
- output/level_one_summaries.csv
- output/general_level_two_summaries.csv
- output/person_level_two_summaries.csv

After the images are downloaded, the `app.py` script can be ran and will process each of the input images using the 
three stages of the pipeline. 

1. Object detection
   1. Performs object localization using the YOLOv3 [[1]](#1) model 
   2. Performs meta data generation using the Inception model 
      1. The Inception model will be downloaded at runtime to the appropriate directory if the model does not exist
   3. The object localization and meta data results will be stored in CSV files
2. Level One Summaries
   1. Generates information corresponding to proximity, overlap, and spatial relationships pertaining to each object two-tuple in an image
   2. Promity and Overlap information is computed using the Generalized Intersection Over Union (GIOU) [[5]](#5) algorithm
   3. Spatial Relationship information is computed using the Histogram of Forces (HOF) [[2]](#2) [[3]](#3) [[4]](#4) algorithm
   4. The level one summaries will be stored to CSV file after computation 
   5. This is a computationally expensive process, and as such there is a boolean flag in `app.py`
      1. `FINALIZED_LEVEL_ONE` is set to false initially for level one summary computation 
         1. Level one summaries will be written to disk every 10 computations to avoid recomputing results
         2. `FINALIZED_LEVEL_ONE` should be set to true after the level one summaries have been computed
3. Level Two Summaries
   1. In the general case, level two summaries indicate if two objects are or are not interacting 
      1. The general level two summaries are stored in a CSV file after computation
   2. The person domain level two summaries indicate interactions between a person and an object in the scene
      1. These level two summaries are stored to disk after computation

# Visualization 
The scripts located in the visualizations/ directory can be used to visualize each of the system outputs generated.

# Example System Output 
Below are some example outputs generated by the S2T system. Each table contains the localization results on the right 
with the **General** domain level two summaries and **Person** domain level two summaries on the right. 

| Object Localization | Level Two Summaries | 
| ------------------- | ------------------- | 
| ![](./bench.jpg)    | **General:** Person_1 interacting with bench_1 <br /> **Person:** Person_1 sitting on bench_1 | 
| ![](./umbrella.jpg) | **General:** Person_1 interacting with umbrella_1 <br /> **Person:** Person_1 carrying umbrella_1 | 
| ![](./cell_phone.jpg)| **General:** Person_1 interacting with cell_phone_1 <br /> **Person:** person_1 talking on cell_phone_1 | 

# Attribution
Citation information here 

# References 
<a id="1">[1]</a>
Redmon, J. and Farhadi, A., "YOLOv3: An Incremental Improvement," arXiv, 2018. 

<a id="2">[2]</a>
Matsakis, P. and Wendling, L., “New Way to Represent the Relative Position between Areal Objects,” IEEE Transactions on Pattern Analysis and Machine Intelligence, Vol. 21, No. 7, 1999, pp. 634-643.

<a id="3">[3]</a>
Matsakis, P., Keller, J., Wendling, L., Marjamaa, J. and Sjahputera, O., "Linguistic Description of Relative Positions of Objects in Images", IEEE Transactions on Systems, Man, and Cybernetics, Vol. 31, No. 4, 2001, pp. 573-588.

<a id="4">[4]</a>
Matsakis, P., Keller, J., Sjahputera, O., and Marjamaa, J. “The Use of Force Histograms for Affine-Invariant Relative Position Description”, IEEE Transactions on Pattern Analysis and Machine Intelligence, Vol. 26, No. 1, 2004, pp.1-18.

<a id="5">[5]</a>
Rezatofighi, H., Tsoi, N., Gwak, J., Sadeghian, A., Reid, I., and Savarese, S, "Generalized Intersection Over Union: A Metric and A Loss for Bounding Box Regression", IEEE Conference on Computer Vision and Pattern Recognition (CVPR), June 2019.
