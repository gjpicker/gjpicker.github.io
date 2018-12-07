
# Conclusion the Drift in Object Tracking 

## Table of Contents

* Reason to Tracking Failure 
	* Train Data 
	* Model 
	* Loss Function 
 
## Train Data 

### Imbalance 
	
sampling ambiguity is one of the big problems in discriminative tracking methods which results in drifting  

* Hard negetive  
	A popular solution in object detection for this issue is hard negative mining [1]
	
	The hard negative examples are identified by testing M<sub>-</sub> (>> M<sub>+</sub> ) negative samples and selecting the ones with top M<sub>h</sub><top>+</top> positive scores. 
	  <img src="https://raw.githubusercontent.com/gjpicker/gjpicker.github.io/master/images/20181207_images/22F9F8B8-F145-4507-9C4F-A6966C844DFF.png" width="50%" class="img-responsive" alt=""> 

* Augment positive 
	
	The elements of the score map are considered to belong to a positive example if they are within radius R of the centre [3]
		 <img src="https://raw.githubusercontent.com/gjpicker/gjpicker.github.io/master/images/20181207_images/2A050CFF-DB40-4719-8309-BFCF37F41A6A.png" width="50%" class="img-responsive" alt=""> 
		 
	- ANS : not uniform or random within a circle ,but try guassian 
	

* Fast motion 
	
	Because these training examples are sampled from a Laplace distribution, small motions will be sampled more than large motions, and thus our network will learn to prefer small motions to large motions 
	
	Objects change their position such that ∆x and ∆y can each be modeled with a Laplace distribution with a mean of 0 

	<img src="https://raw.githubusercontent.com/gjpicker/gjpicker.github.io/master/images/20181207_images/3A08C2BA-39F1-4803-8C87-1C732F73A522.png" width="50%" class="img-responsive" alt=""> 
		
	<img src="https://raw.githubusercontent.com/gjpicker/gjpicker.github.io/master/images/20181207_images/7B9A7B55-A6BD-4E9B-BDC9-D7F41DA81E83.png" width="50%" class="img-responsive" alt=""> 
		

* Part object 
	
	 It also appears that the tracker would have a hard time tracking a part of an object compared to the entire object.
	 
	 eg . when I tried to use it to track the tip of my finger, it ended up tracking the hand. This is probably because it is not trained on parts of objects, but entire objects
	 
## CNN Model 
### data ...

* the large training data required to train correlation filters prevented them from ap- plication to online visual tracking though correlation filters are effective for localization tasks
	
	- ans_1
		-  ILSVRC15 dataset ,50 times larger than VOT 2012 ~2016  
		
	- ans_2  (imagenet + youtube_bb etc )
		- Consists of a collection of videos in which a subset of frames in each video are labeled with the location of some object 
		- This training set of images teaches our network to track a more diverse set of objects and prevents overfitting to the objects in our training videos


### CNN Architecture
* pretrained imagenet 

	General visual feature extractor was obtain from pretrained VGG or Alexnet such as ImageNet classification with 1000 classes .
	
	Tracking the palm of my hand and as I moved it over my face, the tracker latched on to the face and never recovered.[2] 


* Lack of Motion information
	
	Since motion information is not incorporated in the two frame model, if we are tracking an object ( say a face ) moving in one direction, and it gets partially occluded by a similar object ( say another face ) moving in the other direction, there is a chance the tracker will latch onto the wrong face. This problem can be fixed by using the first frame as the previous frame. [2] 
	
* forget prevision frame 
	 
 	online learning with CNNs is not straight- forward because neural networks tend to forget previously learned information quickly when they learn new infor- mation [27]. 
	 	
 	This property often incurs drift problem especially when background information contaminates target appearance models, targets are completely occluded by other objects, or tracking fails temporarily.  [4]
	 	
* UnRobust feature extractor [6]
	
	In comparison, the baseline without the reciprocative learning scheme drifts at the presence of occlusion as the classifier does not attend to temporal robust features. 
	
	Moreover, slight inaccuracy of feature weights will exacerbate the misclassification problem. This requires an in-depth investigation on how to best exploit the visual attention of deep classifiers so that they can attend to target objects over time.
	 - ans   [attention ] [ forecast next frame by GAN ]
	  
		  		  
		  
## Loss function 

* messy 
	- un-distiguish
	
		However, sampling ambiguity is one of the big problems in discriminative tracking methods which results in drifting . Recently, correlation filters [27]–[29] have been introduced for online target tracking that can alleviate this sampling ambiguity.
		
		- ans : correlation filters
	
* long term 
	
	- tracking confidence too low  to re-detect new propose [5]
	
		we include a re- detection module for overcoming tracking failures on the most confident frames using hand-engineered features  .This re- detection module is activated only when the correlation response of the object is below some pre-defined threshold. 
	 	- ans :re-detect module  while confident blow a threshold  

	- reinite 
	
		We then measure the average pixel distance error in every frame as Ed. Whenever the tracking for one landmark fails in one image, i.e., Ed > t, where t is a threshold, we re-initialize this landmark from the ground truth at the failed frame. [7]
		

## ref 

[1] 1510.07945__MDNET.pdf
 
[2] https://www.learnopencv.com/goturn-deep-learning-based-object-tracking/

[3] siamefc

[4] Modeling and Propagating CNNs in a Tree Structure for Visual Tracking

[5] [http://cn.arxiv.org/pdf/1705.11175]

[6] 1810.03851 

[7] [1811.11325]