### Architectures

##### LSTM

LSTM explanation: https://colah.github.io/posts/2015-08-Understanding-LSTMs/
> Just like CNNs are like feedforward networks that share weights through kernels, RNNs share weights through loops LSTMs keep a “state”, in addition to the output of the network at each step. This passes through “forget” and “add” networks and gates to update the state at each step. 

https://distill.pub/2016/augmented-rnns/#attentional-interfaces
> “This creates a major challenge: how do we learn which actions to take? That sounds like a reinforcement learning problem and we could certainly take that approach. But the reinforcement learning literature is really attacking the hardest version of this problem, and its solutions are hard to use. The wonderful thing about attention is that it gives us an easier way out of this problem by partially taking all actions to varying extents. This works because we can design media—like the NTM memory—to allow fractional actions and to be differentiable. Reinforcement learning has us take a single path, and try to learn from that. Attention takes every direction at a fork, and then merges the paths back together.”

### CNNs

_Resnet_

* http://teleported.in/posts/decoding-resnet-architecture/
* Residual is what you should have added to your prediction to match the actual. Zero is way easier to learn than identity mapping

### Tensorflow 

* Using Tensorboard
    * `tensorboard --logdir=PATH`, Replace PATH with the actual path passed as `model_dir`when making `tf.estimator.Estimator`
    * Add evaluation metrics (loss, accuracy) during model construction



### Tricks 

##### Learning rate 

* Reference: https://techburst.io/improving-the-way-we-work-with-learning-rate-5e99554f163b
* Find default learning rate by training for a while with linearly increasing learning rate, until loss stops decreasing. Choose rate that is slightly before the loss stopping. 
* Cyclical learning rates, Stochastic gradient descent with restarts : increase learning rate once in a while to get out of shallow local minima/saddle points

* Differential learning rates: fine-tuning full pre-trained models (instead of just last layers) by using higher learning rates towards the end, and smaller towards the beginning
    * Ref: https://towardsdatascience.com/transfer-learning-using-differential-learning-rates-638455797f00
    * Note that it's still better to freeze all except last layers and fine-tuning fc layers first
    * Assumption is that features towards the end of the network are more data-specific, while features in the beginning are more generic
