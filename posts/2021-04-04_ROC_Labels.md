# The meaning of ROC and AUC for binary outputs

1. TOC
{:toc}

## AUC & ROC...what?
The Area under the [ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) (AUC) is a well-known method to estimate the goodness of a binary classifier.
True positive and False positive rates are measured for different thresholds from 0 to 1 and the curve is plotted.


![](/images/Roc-draft-xkcd-style.svg "By MartinThoma - Own work, CC0, https://commons.wikimedia.org/w/index.php?curid=70212136")


To plot the ROC curve, the classification output should be the probability that the input belongs to the positive class (`class 1`) and not the label itself (which would be `1` or `0`).

## So...what's the problem?

Some days ago, I was looking at a post in [Kaggle](https://www.kaggle.com/) where it was shown the difference in the results of the `roc_auc_score` function if labels were used as outputs instead of probabilities.
That was strange to me because if the output is the label, the True-positive and False-positive rates will always be the same independently from the chosen threshold.
This means that varying the threshold, we will always get the same point in the TP/FP plot, and plotting the curve would be impossible, let alone evaluate the area under the curve.

##  Meaning of the AUC for binary label outputs

I started looking into the implementation of the `roc_auc_score` function.
Long story short, the function always assume that for threshold=0 we have `TP=FP=0`, while for threshold=1 `TP=FP=1`. In this way there will always be at least 3 points.
It then linearly interpolate between each couple of points to draw the ROC curve. In this case the two segments would be `(0,0)—->(FP,TP)` and `(FP,TP)—>(1,1)`.
The area under the ROC curve would then be `TP*FP/2+TP*(1-FP)+(1-TP)*(1-FP)/2`. 
