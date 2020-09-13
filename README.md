# Behold.ai

## Introduction

This project involves the implementation and training of a model in order to correctly classify brain CT images with three different types of intracranial hemmorhage (ICH), namely epidural, intraparenchymal, and subarachnoid. As each patient can present with more than one type of ICH, this problem is essentially a multi-label image classification problem.

Data are adapted from a Kaggle competition (see references), and consists of 8542 training and 4019 test images. These images are 128x128 CT scan images of a patient's brain. The scripts to run the model are in this repo as a Jupyter notebook and I chose to run it using Google Colab as it allowed use of a free GPU which is very valuable when training large networks. The script follows most standard data science workflows: first, I preprocessed the data. Then I built and trained a model, which in this case was a convolutional neural network (discussed more in detail later). Finally I evaluated and ran predictions. 

## Data Preprocessing

As always, data preprocessing is the most important step in any project. Bad data in gets bad results out. Fortunately in this case we were lucky. The data was already cleaned (as they're images there is little chance of a null value) and they were all the same size. Images were loaded via a specially designed load_datasets function. This loads the image, changes it to an array (as a neural network works on numbers rather than directly from the pixels), and then normalises the values. The shape of the datasets was also checked at this point to ensure we had the correct number of images in the dataset and the dimensions of the image were correct. Images were already split into a training and a test set (test set without the accompanying ICH data), and I further split the training set into a training subset and a validation subset in order to obtain validation loss and accuracy. This can give a clearer idea if the model is ready for application to a broader setting, or if it is overfitting to the training set. One thing that could be checked in more detail here is the balance of classes within the data set. Too much imbalance caused by one label being present a lot more than others will cause any model to simply predict that label for every example, and will score reasonably well on accuracy measures (until applied outside of the imbalanced data). Various solutions, such as adapting the error function or upsampling, have been proposed, but were not implemented here due to time constraints.

## Model Building

The approach I chose for this project was to use a CNN. CNNs work really well on image problems, as they mirror certain aspects of the human brain's ability to extract higher-level features in the deeper layers of the network. This allows a CNN to draw inferences from these higher-level features that might not be present in a shallower network or other type of model. CNNs are also fairly flexible (for exampe they don't even have to be applied to images), and they can be adapted for this multi-label task with key changes. Using sigmoid activation functions alows prediction of each label independently (as opposed to a softmax activation function in a multi-class classification task), and we have to use binary cross-entropy as the loss function. Keras was chosen here for simplicity and ease of use, along with significant documentation being available for this approach (always useful when debugging). The network architecture was initialised with only two convolutional layers and two dense layers, but later changed to three convolutional layers and three dense layers. This would theoretically help with extraction of higher-level and more abstract features but would also take longer to train and there is also a limit to how much increasing the depth of the network produces gains in the performance metric which we have to be aware of. 

Another approach I could have chosen here was transfer learning. Pre-defined architectures with pre-trained weights are extremely useful as they severely cut down on time needed to train the model. With a few adjustments and some retraining of the output layers, a model previously trained on much larger datasets can be fine-tuned to the specific task at hand. This approach would have likely given better performance than a newly-built model, and almost definitely with the first few results. However I opted not to go for this approach because I have just used transfer learning in a previous task and I wanted to try something different, which would allow me to learn and gain more experience. Additonally, the problem did not immediately present as complex enough to warrant the involvement of large-scale pretrained models and the complexity of dragging them into a notebook. If the task was to build a production-grade model then an adaptation of an existing model would have undoubtedly been the best approach here. 

## Evaluation and Further Improvements

The model did not perform particularly well (and maybe I regret not going down the transfer learning path now). Final results showed an accuracy of 0.455 which is only slightly better than random guesswork. While the training loss steadily decreases and the accuracy steadily increases, the gains are relatively slow. Further epochs also revealed that the validation accuracy stayed static and the validation loss began to increase dramatically after around 20 epochs. This means the network is overfitting on the training set. The model showed slight improvements when the network architecture depth was increased, but additional layers did produce much benefit. Further experimentation with the number of layers, as well as the dropout regularisation, is definitely required here. This would be my first path to improving the accuracy of predictions. Starting with the smallest possible network and slowly increasing the complexity until the law of diminishing returns is reached would allow the discovery of the most optimal architecture for this problem, however it would also be time-consuming. Experimentation with activation functions would also be useful here. While the output layer has to be sigmoid, the intermidiate layers could be altered. The current all-purpose solution, and used here, is the ReLU function. 

Hyperparameter optimisation is a key component of improving the network. Further experimentation with the batch size, learning rate, as well as possible L1 or L2 regularisation parameters, would produce benefits. This would also be a key line of enquiry for further improvements. 

In terms of the data, the question I would ask immediately is 'can we get more data?'. Although ~12000 images sound like a lot, state-of-the-art networks with good performance on metrics are trained on hundreds of thousands, if not millions, of images. More data will allow for a more robust and better-peforming network. Failing this, we could turn to data augmentation. Skewing, flipping, or otherwise manipulating the data can allow for the network to 'see' the data in new ways and enhance the performance. It would increase the diversity of images and would allow the network to respond appropriately in new situations (such as during predictions in the test set, and if applied to the real world.) This is something that could be implemented here with relative ease. 

Although the experimentation would be time-consuming, and was thus outside the scope of this project, and require diligent documentation, implementation of these experiments could lead to much better performance in the metrics, and also more importantly more valid predictions.  


by Alex Whelan


## References

https://www.kaggle.com/c/rsna-intracranial-hemorrhage-detection/overview

