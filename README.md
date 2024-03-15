# Multi-accent-English-Speech-Recognition

With the trend of globalization, the importance of English as a global lingua franca grows. However, the diversity of English accents, influenced by native languages, regions, and cultures, presents challenges for speech recognition systems. We aims to enhance speech recognition of accented English by improving accent discrimination. We introduce a method integrating accent classification into the speech recognition model to better identify different accents. Results show this approach reduces error rates and analyzes accent features across the model's layers, improving recognition of diverse accents.

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

Speech recognition systems trained on standard speech often perform poorly when recognizing accented speech. 
The most intuitive method to improve this is to fine-tune the model using accented speech data.
We attempt to compare the training results with and without the use of pre-trained model.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/Pretraining.png" width=75% height=75%>

The experiment reveals that pre-training the model with Librispeech and then fine-tuning it using AESRC2020 yields the best accuracy.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/pretrain_result.png" width=75% height=75%>

## Multi-task Learning Result

According to the experimental results in Table 2, incorporating an auxiliary accent classification task at just one layer of the encoder can reduce the Word Error Rate (WER) of speech recognition. This indicates that the auxiliary classification task can indeed help the model increase its discrimination for different accents. Additionally, we attempted to integrate the accent classification task at various layers of the encoder simultaneously.

It was found that adding the classification task to every layer of the encoder could further improve performance. However, the experimental results also showed that incorporating the classification task at the 3rd, 6th, and 9th layers yielded the best results, with the lowest overall WER and mostly reduced WERs for each accent. However, adding an additional auxiliary task at the 12th layer of the encoder significantly worsened the performance, even more so than not incorporating any auxiliary tasks at all, indicating that arbitrarily adding classification tasks does not necessarily benefit the model.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/MTL_result.png" width=75% height=75%>

## Classification weight

The weight of the loss for the accent classification task affects the learning of the model, and in this experiment, the setup with the best result involves adding the classifier to layers 3, 6, and 9 of the Conformer module. As can be seen from the result, when the weight changes from 0.3 to 0.5, there is no significant change in the model's recognition performance. However, as the weight increases to 0.7, there is a noticeable decline in recognition performance. This is because increasing the weight of the classification task in multi-task learning decreases the weight of the CTC loss, leading the model to emphasize accent classification at the expense of its speech-to-text capabilities. Therefore, in the experiments conducted for this study, the weight α is set to 0.3.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/hyper_parameter.png" width=75% height=75%>

## Visualization of Accent Features

The accent features in the experiment are visualized using t-SNE to analyze variations in accent speech characteristics.

To facilitate data visualization, we randomly selects 20 audio files from the test set for each speaker for visualization purposes.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer3.png" width=75% height=75%>

It is observed that the distribution of accent features in the third layer of the Conformer module closely resembles the characteristics of individual speakers. Each speaker forms their own cluster, and whether the classification task is incorporated, there is no significant impact on the feature distribution at the model's third layer.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer6.png" width=75% height=75%>

It is evident that the accent features in the sixth layer contain a wealth of accent information. Even without the addition of a classification task, the distribution of accent features is very close to that of different accents, and the incorporation of the accent classification task further enhances the clear separation of different accents. 

It was also observed that adding the classification task to just one layer of the model yields the best results when choosing the sixth layer, as it holds the richest accent information. Additionally, Figure 5 shows that American and Canadian accents are similar; Portuguese and Spanish accents are close; and Japanese and Korean accents resemble each other. This reveals a close relationship between accents and geographical distribution.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer9.png" width=75% height=75%>

In the ninth layer of the Conformer module, it is observed that the features of different accents become closer to each other, as shown in the figure. Although adding the classification task can slightly increase the spatial distance between different accents, the effect is not very pronounced. Consequently, we can assume that as the layer number increases, the accent features in the encoder gradually evolve into features without sound information, losing their ability to discriminate between accents.

<img src="https://github.com/Sky9487/Multi-accent-English-Speech-Recognition/blob/main/image/layer12.png" width=75% height=75%>

It is observed that the distributions of nearly all accent features are mixed together, with no discernible clusters visible. This is because the output of the twelfth layer, which does not contain audio information, is textual. Hence, there is almost no information about accents. This indicates that accent features are closely related to acoustic features. To improve the accent recognition rate of speech recognition models, acoustic features are significantly more important than textual features.
