FAQs & Discussion
Each week the subject coordinator will choose one or two questions posted here about the content of the subject and answer here.

Please consult this page before asking a question.

Click on each of the tabs below to read the FAQs related to each topics.

SUBJECT DELIVERY, MATERIALS, TUTORIAL/LAB Etc.
ASSIGNMENT-1
ASSIGNMENT-2
ASSIGNMENT-3
Assignment-2 Questions
How many datasets do we need to use for Assignment-2 ?
ANSWER: As mentioned on the Assignment-2 specification: there are two tasks, 1. Image Classification, 2. Object Detection. There are different datasets for each of the tasks. You will get one unique dataset for Image Classification and one unique dataset for Object Detection task based on your Student ID. Please note that the datasets for image classification is different from object detection. Object detection datasets have the annotation files as well.

Please refer to the assignment specifications and the assignment submission page for more details.


Do we need to split the image classification dataset into train, validation and test sets for assignment-2? Should I write code to split the dataset (train, validation and test sets) for Assignment-2, or do it manually?
ANSWER: YES, specification for Image Classification mentions: “You must use your student ID as random seed to split the image classification dataset into train, test and validation sets”. Any of the approach (use code, or manual split) is fine given that you meet the above criteria. However, it will be good if code is written to create the splits.


Training is taking too long for Assignment-2 experiments? is it normal? I am worried
ANSWER: As mentioned several times in the lectures/tutorials, it is normal for a deep learning training session to take longer time (5-10hrs). It is important to use checkpointing (Discussed and demonstrated in the tutorial sessions) and save the model weight directly into AWS/Google Drive (when using Colab), so that in-case the session stops for some reason, the training can be resumed from the last epoch/iteration weights.

It is important to check the intermediate outputs (loss, validation accuracy, etc.) to make sure that the training is happening in the right way, and decide accordingly.


Some images don’t have Annotations, or Annotation files are empty, what should I do?
ANSWER: Some images might not have the object of interest, hence the annotation or the files are empty. In any case you see any incomplete annotations – i.e., object of interest is there, but there are none or less annotations in the files, you have two options: 1) Feel free to annotate the remaining. 2) feel free to use the empty and/or partial annotation files. Note: DO NOT Remove images with empty annotation files (where possible), as some data might need that to avoid false positive detection.


Am I allowed to use any third-party annotation/augmentation/visualisation tool/software/website?
ANSWER: NO, you are only allowed to use CVAT, LabelImg, and other locally hosted annotation/augmentation/visualisation software/tool. Using the websites like Roboflow, MakeSense, HuggingFace, etc. would result in the customised dataset becoming available to the public which will violate the rights mentioned in the readme files causing major penalty and action.


Do we need to split the object detection dataset into train, validation and test sets for assignment-2?
ANSWER: NO, you are already provided with the train, validation and test split according to your student ID. You must use the same split and perform experiments.


Can I use transfer learning in assignment-2?
ANSWER: Yes, transfer learning can be used to initialize the weights, followed by re-training all the layers in the model. Please refer to Week-7 lecture slides, transfer learning option-4.


Can I just train for 5-10 epochs for each tasks in assignment-2 and submit the assignment?
ANSWER: No, training must be done until the model is fairly stable. For image classification: 100+ Epochs, Object Detection: 300+ Epochs, with an appropriate early stopping option. Not training your model enough will result in significant negative marking.


VERY IMPORTANT: Suggestions for assignment-2?
ANSWER: 1. Start your assignment earlier than later
2. It takes time to train Deep Learning model
3. Train your model for longer epochs, so they are stable (100+ epochs for Image classification, 300+ epochs for object detection) 4. Use early stopping criteria


Why do I need to submit the code and report separately in two different links for Assignment-2?
ANSWER: There are two parts in the assignment-2 submission: Part-A (Code) and Part-B(Report). The submissions are separate, as the report will undergo plagiarism check. Students should use the appropriate links to submit the code and report.


Will I get marks if I just submit the code or report for Assignment-2?
ANSWER: There are two parts in the assignment-2 submission: Part-A (Code) and Part-B(Report). Assignment submission is incomplete without both report and code submission, via the correct link. If you just submit either code or report, the assignment submission will not be considered and marked as incomplete, and no marks will be provided. Additionally, your colab notebooks should have the outputs/results of code execution, which will be matched with the report for marking. Code/Colab Notebook not having results will be considered incomplete, as the results cannot be matched with the report.


Can I use YOLO for the Object Detection task of Assignment-2?
ANSWER: Yes, Use of YOLO for the object detection task is allowed. Check Specification document or ask for clarification.


The number or image files and xml file doesn't match for the object detection dataset, how to proceed ?
ANSWER: A small pre-processing is expected on the dataset. Either remove the images not having the corresponding xml files, or manually create the xml files for the corresponding images. You can choose any one of the options.  For the second option, annotations should to be done manually to create the xml files.


What you mean by Customization of CNN and fine-tuning?
ANSWER: Customization refers to addition/deletion of layers (CONV, etc.) with an intention to find a better performing CNN for a particular problem/dataset.
Example: Layers are added/deleted to a baseline Alexnet architecture.

Fine-tuning refers to update/adjustment of weights in the CNN layers, with transfer learning.
Example: Baseline model used is Alexnet, weights initialized using pre-tranined model, and the whole network weights are updated after training cycle. This is fine-tuning the weights based on a dataset used. Here, instead of starting from a random weight matrix or zero, the training starts from pre-trained weights, helping in faster convergence of the training process. Freezing some layers and just training few higher layers, is also not considered as fine-tuning, without deletion/addition of layer to the baseline CNN architecture.

Refer to the tutorial Week-7 on transfer learning.



How to understand the Object Detection API performance evaluation ??
ANSWER: Please check the stackoverflow and Github links given below:

https://stackoverflow.com/questions/58291557/tensorflow-object-detection-api-evaluate-training-performanceLinks to an external site.

https://github.com/Cartucho/mAPLinks to an external site.


How can I estimate the testing accuracy for object detection for performance evaluation ??
ANSWER: Please check the stackoverflow and Github links given below:

https://github.com/Cartucho/mAPLinks to an external site.


Do you have some general feedback on Assignment-2?
ANSWER: The following are some general feedback for students for future improvement, although you should have received specific comments on your assignments:

Expected Submission	 Actual Submissions
As mentioned in the assignment specification ipynb/colab notebook where required as part of the submission.	Dataset, trained model (H5, PB etc.), ipynb/colab notebook etc. submitted, making the submission file quite large which takes longer time to submit/of exceeding the max file size limit.
AWS Sagemaker/ipynb/colab notebooks with results after cell execution, showing accuracy/performance/training steps etc.	AWS Sagemaker/ipynb/colab notebooks submission without results/execution.
Custom architecture for image classification: Atleast CONV layer addition/deletion was required for customization along with other changes in the FC layer part. This was clarified in the lectures, and clearly mentioned on FAQs.	CONV layer addition/deletion not done, used a different baseline architecture as a customized architecture, etc.
Assumptions/intuition leading to the customization of the CNN architecture for image classification.	No assumptions/intuitions provided which should be based on the analysis of baseline CNN, inappropriate assumptions/intuitions provided.
Sample result images for both image classification and object detection task in the discussion section, as mentioned in the report template provided.	Sample images not provided.
Experimental settings (hyper-parameter settings, data augmentation settings (if applied), transfer learning (if used, provide information on pre- trained model, etc.)), as mentioned in the report template.	Experimental settings partially/not provided appropriately.
Discuss about your understanding of the accuracy/performance obtained from experiments.	Very basic discussion, without providing appropriate basis/intuitions on the accuracy/performance.
Dataset for image classification is split into train, test, valid using student ID as a random seed.	Dataset for image classification is either split into train, test OR without using student ID as a random seed.