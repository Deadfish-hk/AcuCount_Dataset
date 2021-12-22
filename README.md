# AcuCount_Dataset
 
This repository contains all nesscessary files and scripts required for running the telegram bot. Follow the instructions, and execute the corresponding cells in the .ipynb scripts to reproduce the result of our AcuCount paper.

The *dataset* folder contains the raw images for the *training set*, *testing set* and the *validation set*. For the *training set* and the *testing set*, the needle annoatations are also provided in .xml and .csv files.

The *research* folder contains the validations records infered by the respective models. We have also provided the sample config file for the RetinaNet model and the tfrecord file for the training of AcuCount dataset.

This repository is tested under Colab Pro enviroment with TensorFlow 2.5 and CUDA 11.2.

## Install the TensorFlow Packages

Import the lastest version of TensorFlow and clone the model repository. Modify the anchor scale (if needed) before the TensorFlow installation. You can check which GPU is running now by testing the installation.

## Dataset Preparation

By cloning this repository, you will have the *dataset* folder, splited in *test* and *train* subset, which contains the needle images (.jpg) and the needle labels (.xml) generated by [LabelImg](https://github.com/tzutalin/labelImg). The tfrecord files used for our needle detector are saved under  *research* folder. 

### Custom dataset training

If you want to train a custom dataset, gather your own images and labels, then configure the [generate_tfrecord.py](generate_tfrecord.py) by editing the labelmap within the `class_text_to_int` function (in line 28-32) with your own object classes.

For Single-Class:
```python
# TO-DO replace the row_label with your custom object class
def class_text_to_int(row_label):
    if row_label == 'needle':
        return 1
    else:
        return None
```
For Multiple-Classes:
```python
def class_text_to_int(row_label):
    if row_label == 'apple':
        return 1
    elif row_label == 'strawberry':
        return 2
    elif row_label == 'orange':
        return 3
    else:
        return None
```
Open the [labelmap.pbtxt](labelmap.pbtxt) and edit the item id to match the class label specificied in the above `class_text_to_int` function.

For Single-Class:
```python
item {
    id: 1
    name: 'needle'
}

```
For Multiple-Classes:
```python
item {
    id: 1
    name: 'apple'
}
item {
    id: 2
    name: 'strawberry'
}
item {
    id: 3
    name: 'orange'
}
```

After the configuration of label maps, run the [xml_to_csv.py](xml_to_csv.py) script to generate a new train_labels.csv and test_labels.csv for the subsequent execution of [generate_tfrecord.py](generate_tfrecord.py). You should now have a train.record file and a test.record file for later use.  

## Training Configuration

Download a pre-trained model of your choice from [TensorFlow model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md). Match your selected model with the sample .config file from the [tf2 sample configs](https://github.com/tensorflow/models/tree/master/research/object_detection/configs/tf2) folder. Make sure that you are pointing to the correct pathway in the .config file.

Modify the hyperparameters in the corresponding proto message. You can find the supported data augmentation strategy in the [preprocessor.proto](https://github.com/tensorflow/models/blob/master/research/object_detection/protos/preprocessor.proto) folder.  

## Model Training

Execute `model_main_tf2.py` to initate the training. If you want to monitor the training progress, you can run the tensorboard to check up on the learning losses.

Type Ctrl+C to terminate the training. 

## Model Evaluation (Optional)

Install the COCO API to use COCO detection metrics for model evaluation. Add argument `checkpoint_dir` and `eval_timeout` to `model_main_tf2.py`. Run the command again to compare the trained model on the test.record generated with the test images and labels. Restart the tensorboard on `--logdir /content/training/eval` to view the evaluation result.

## Export the Inference Graph

Specify the pathway directory of where you want to save the inference graph in argument `--output_directory`, preferably on Google Drive. This will allow easy access for later hosting on Google Colab. You may also download the exported model for local usage.

Below are the download links for the pre-trained models on the AcoCount dataset.

[Original Faster R-CNN](https://drive.google.com/drive/folders/1VQIPrqSPEGuhwaWJzKjiS9fiS3h8R5wd?usp=sharing) 

[Modified Faster R-CNN](https://drive.google.com/drive/folders/1uj6-ikp83D-ZcLBUhpxfG9hGiOBp9TUA?usp=sharing)

[RetinaNet](https://drive.google.com/drive/folders/1nFnb9OTaFS2q5zXLpAOiI6a4Y_I4rBMY?usp=sharing)

## Host the Model

Switch to telegram_bot.ipynb for hosting the telegram bot on Google Colab. Install the nesscessary packages ([python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot), [TensorFlow model repository](https://github.com/tensorflow/models)). Mount the Google Drive for accessing the previosuly saved inference graph, and storing the inward/outward files. 

Follow the instutions on [telegram botfather](https://core.telegram.org/bots#6-botfather) to create a new telegram bot of your own and place the token in the script. 

Some pointing drectory contain `xx/yy` in their pathname. Replace them with your links in Google Drive.  

This script uses `logging` modlule for recording the inward/outward timestamp. Place an empty .log file in your Google Drive for proper record. 

## Acknowledgement

The authors would like to thank [EdjeElectronics](https://github.com/EdjeElectronics) and [TannerGilbert](https://github.com/TannerGilbert) for their clean and well-written tutorials on TensorFlow API.