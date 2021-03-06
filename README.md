# Facial Emotion Recognition with Apache MXNet GLUON and MXNet Model Server

This repo is an Apache MXNet GLUON implementation of state of the art [FER+ paper by Barsoum et. al.](https://arxiv.org/abs/1608.01041) for facial emotion recognition.

This repo consists of following resources:
1. Scripts for data pre-processing as suggested in the paper.
2. Notebook for model training with MXNet GLUON and exporting the trained model.
3. Large scale productionization of the trained model using MXNet Model Server(MMS) - https://github.com/awslabs/mxnet-model-server

You can see final demo at - http://bit.ly/mxnet-fer
Note: Please use firefox or safari browser. Chrome is not supported yet.

In this implementation, we use majority voting (MV) technique illustrated in the paper and achieve `81.25%` validation accuracy.(paper achieves 83.85%)

# Prerequisites / Installation

```
# Install MXNet

    pip install mxnet-mkl # for CPU machines
    pip install mxnet-cu92 # for GPU machines with CUDA 9.2
    
# Other Dependencies

    pip install Pillow # For image processing
    pip install graphviz # For MXNet network visualization
    pip install matplotlib # For plotting training graphs
```

Please refer [MXNet installation guide](http://mxnet.incubator.apache.org/install/index.html?platform=Linux&language=Python&processor=CPU) for more detailed installation instructions.

# Model Training

## Step 1 - Data preparation

* Clone this repository

```
    git clone https://github.com/sandeep-krishnamurthy/facial-emotion-recognition-gluon
    cd facial-emotion-recognition-gluon
```

* Download FER dataset `fer2013.tar.gz` from - https://www.kaggle.com/c/challenges-in-representation-learning-facial-expression-recognition-challenge/data

Note: You cannot download the dataset with `wget`. You have to register on Kaggle and download the dataset due to licencing issue.

* Extract the tar file - `fer2013.tar.gz`
* Copy `fer2013.csv` dataset to `facial-emotion-recognition-gluon/data` directory. 
* Generate `FER+` train/test/validation dataset from downloaded `FER` data.

```
    # -d : path to "data" folder in this repository. It has folder for Train/Test/Validation data with corrected labels.
    # -fer : path to fer dataset that you have extracted.
    # -ferplus : path to fer2013new.csv file that comes with this repository in the data folder
    
    python utils/prepare_data.py -d ./data -fer ./data/fer2013.csv -ferplus ./data/fer2013new.csv
```
In this step, we read the raw FER data, correct the labels using FER+ labels, and save as png images.

* Process the `FER+` train/test/validation dataset

```
    # -d : path to data folder. This is where we have created data from the previous step.
    python utils/process_data.py -d ./data
```
In this step, we read the FER+ dataset (png images) we prepared in the previous step, apply the transformation suggested in the [paper by Boursom et. al.](https://arxiv.org/abs/1608.01041) (Crop, Flip, Rotate, Affine Transformation, Scale).

Processed training/test/validation data are saved as numpy binaries (`*.npy`). We use these processed data in the model training notebook.

 
## Step 2 - Model Training and Saving

Open the `notebook/Gluon_FERPlus.ipynb` notebook and follow the instructions to build the VGG13 network suggested in the paper, train the model using the processed images.

In the notebook, we will also export the trained model as MXNet symbol and params files (MXNet's representation of the model).

# Inference

TODO

# Next Steps (TODOs)

Below are few areas of improvements and next steps. All contributions are welcome!

* Hyper-parameter optimization - In this implementation, I have not optimized hyper-parameters (learning rate scheduler for SGD) for best possible result.
* Implement multi-gpu version of model training. This script provides single GPU implementation only. Time per epoch on single GPU is around 1 minute => approx 50 minutes for full model training (Model converges at around 50th epoch)

# Contributors

* [Sandeep Krishnamurthy](https://github.com/sandeep-krishnamurthy/) 
* [Saravana Kumar](https://github.com/codewithsk)
* [Hagay Lupesko](https://github.com/lupesko/sentiment-analysis-with-sagemaker-mxnet)

# Citation / Credits

* FER+ paper
@inproceedings{BarsoumICMI2016,
    title={Training Deep Networks for Facial Expression Recognition with Crowd-Sourced Label Distribution},
    author={Barsoum, Emad and Zhang, Cha and Canton Ferrer, Cristian and Zhang, Zhengyou},
    booktitle={ACM International Conference on Multimodal Interaction (ICMI)},
    year={2016}
}

* CNTK implementation of FER+ paper - https://github.com/Microsoft/FERPlus
* FER demo built by https://github.com/codewithsk. (GitHub repo will be public soon)

# Resources

* Apache MXNet (incubating) - http://mxnet.incubator.apache.org/
* Learn Deep Learning with Gluon - https://gluon.mxnet.io/
* Productionizing Deep Learning Models with MXNet Model Server - https://github.com/awslabs/mxnet-model-server
