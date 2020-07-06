# Graph Convolutional Transformer

This repository contains an implementation of Graph Convolutional Transformer, as described in “Learning the Graphical Structure of Electronic Health Records with Graph Convolutional Transfomer” (Choi et al. 2020). Code was written using Python 2.7 and Tensorflow 1.13.

#### Relevant Publication

Graph Convolutional Transformer implements an algorithm introduced in the following [paper](https://arxiv.org/pdf/1906.04716.pdf):

	Learning the Graphical Structure of Electronic Health Records with Graph Convolutional Transformer
	Edward Choi, Zhen Xu, Yujia Li, Michael W. Dusenberry, Gerardo Flores, Yuan Xue, Andrew M. Dai  
	AAAI 2020

The code sample provided here is minimally executable using TensorFlow Estimator. Additional functionalities (e.g. TensorBoard monitoring) must be added by the user. The input data format is TensorFlow SequenceExample packed in the TFRecord format. We provide a Python script that generates trainable SequenceExamples from Philips eICU Collaborative Dataset. The current implementation only supports graph-level binary prediction (e.g. readmission prediction or mortality prediction based on a single visit).

This is not an officially supported Google product.

## Overview and Usage Guidelines

### Step-by-step to train the model
* Clone the repository. 
* Request access to the eICU dataset from [eICU website](https://eicu-crd.mit.edu/gettingstarted/access/).
  * Note that you are required to participate in the CITI training.
* Download the patient, admissionDx, diagnosis, treatment CSV files.
* Generate TFRecords using `eicu_samples/process_eicu.py`.
  * `python process_eicu.py <path to CSV files> <output path>`
  * By default, this will generate 5 randomly sampled sets of train/validation/test data.
* Train model using `train.py`
  * `python train.py <path to TFRecords> <output path>`.
  * Note!! You need to reference one of the folds in TFRecords, you cannot use that directory instantly because it expects tf checkpoints

### Implementation detail
We implement the following files to run the model:
* `train.py`
  * This file is the entry point for training the model. Model is trained usinv TensorFlow Estimator.
  * It is currently written to train the model for readmission prediction. To change the task to mortality prediction, set the label_key to "label.readmission".
* `graph_convolutional_transformer.py`
  * This file contains the Graph Convolutional Transformer implementation, along with input_fn and model_fn to be used by TensorFlow Estimator.
* `eicu_samples/process_eicu.py`
  * This file preprocesses Philips eICU Collaborative Dataset in order to obtain TFRecords of SequenceExamples that can be used to test the model.
* `synthetic_samples/process_synthetic.py`
  * This file generates TFRecords of synthetic SequenceExamples. If you are interested in replicating synthetic experiments in the paper, please use this script to generate synthetic samples. However, the source code `graph_convolutional_transformer.py` also needs modifications as it is currently written for binary prediction tasks only.
  * To use `process_synthetic.py`, first download [visit_list.p](https://github.com/Google-Health/records-research/releases/download/v0.1/visit_list.p) from the binary release, then execute `python process_synthetic.py <path to visit_list.p> <output path>`
  * By default, this will generate 5 randomly sampled sets of train/validation/test data.
