# Multi-accent-English-Speech-Recognition

## Conformer Model
As a type of end-to-end model, it combines the advantages of CNN and Transformer, allowing for both local and global modeling of audio sequences and demonstrating superior recognition rate on many speech recognition datasets.

Conformer model first processes the input audio through spectral enhancement, convolutional subsampling, a linear layer, and dropout before feeding the features into the Conformer block, which is the core module of the Conformer model's encoder.

The Conformer block contains multiple self-attention modules, convolutional modules, and two feed-forward modules. Given an input $x_i$ entering the Conformer block, we can get the output of conformer blocks $y_i$.


<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/Conformer_model.png" width=75% height=75%>

## Multi-task Learning

To enhance the model's discrimination ability for different accents, we incorporates an auxiliary accent classification task into various layers of the Conformer encoder.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/accent_classifer.png" width=75% height=75%>

After incorporating the accent classification task, for the output features $y_i$ of the Conformer module, we can get the predicted $y_{accentID}$ through the accent classifer:

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/loss_accent.png" width=75% height=75%>

Where "mean" refers to average pooling, "std" refers to standardization, and "concat" means vector concatenation. $FC$ represents the fully connected layer, which, in this experiment, is a three-layer neural network with the ReLU activation function.

After concatenating the vectors obtained from averaging and standardization, this feature then enters a fully connected layer for accent classification to obtain the accent label $y_{accentID}$.

After obtaining the predicted $y_{accentID}$ from the classification task, given the actual accent label $y_{label}$ for this audio, the loss for the classification prediction $L_{accent}$ can be calculated by the cross-entropy loss function, where α is a hyperparameter.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/MTL_loss.png" width=75% height=75%>


## Pretraining and Finetuning
<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/Pretraining.png" width=75% height=75%>

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/pretrain_result.png" width=75% height=75%>

## Multi-task Learninn Result

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/MTL_result.png" width=75% height=75%>

## Classification weight

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/hyper_parameter.png" width=75% height=75%>

## Visualization of Accent Features

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer3.png" width=75% height=75%>

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer6.png" width=75% height=75%>

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer9.png" width=75% height=75%>

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer12.png" width=75% height=75%>
