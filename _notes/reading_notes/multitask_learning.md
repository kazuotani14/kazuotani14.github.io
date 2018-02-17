# Multi-task learning for robot control

Project notes for Deep RL and Control course

http://www.cs.cmu.edu/~rsalakhu/10703/

### Project idea 

Can we use embedding layers instead of one-hots to: 1. train multiple behaviors in one network thru RL, just like other approaches. 2. have "features" for these behaviors that we trained for, so we can do interpolation between behaviors? 

### Notes

##### "Multitask learning for continuous control"

* They train a locomotion task on models of HalfCheetah with 25% enlarged body parts, and find that they can achieve better performance for all models by training a joint policy instead of independent models. 
* Can this be interpreted as data augmentation for RL? Also seems similar to ensemble control (see Mordatch's Ensemble-CIO)
    * ensemble methods can probably be used for production robots as well

##### Maximum entropy methods

* In practice, we prefer maximum-entropy models as they assume the least about the unknowns while matching the observed information. e.g. If the distribution is on the Euclidean space and the observed statistics are the mean and the covariance, then the maximum entropy distribution is a Gaussian with the corresponding mean and covariance.

##### References

_To read_

* Embedding networks for RL: https://openreview.net/forum?id=rk07ZXZRb
    * Figure out what "entropy-regularized RL" and "variational inference" are
* http://www.cs.cmu.edu/~rsalakhu/10703/Lectures/Lecture_DQL.pdf
    * https://arxiv.org/abs/1511.06342
* Maximum entropy reinforcement learning
    * Explanation of max entropy RL, solid list of references: http://bair.berkeley.edu/blog/2017/10/06/soft-q-learning/
    * https://arxiv.org/pdf/1507.04888.pdf
    * [Maximum Entropy Inverse Reinforcement Learning](https://www.ri.cmu.edu/publications/maximum-entropy-inverse-reinforcement-learning)
        * http://178.79.149.207/posts/maxent.html
            * Code: https://github.com/stormmax/irl-imitation

* One model to learn them all 
    * https://blog.acolyer.org/2018/01/12/one-model-to-learn-them-all/
    * https://arxiv.org/abs/1706.05137
* https://www.kdnuggets.com/2016/07/multi-task-learning-tensorflow-part-1.html

_Read_ 

* [Good explanation of embeddings in structured learning](https://towardsdatascience.com/structured-deep-learning-b8ca4138b848)
* [Explanation of embeddings](http://colah.github.io/posts/2014-07-NLP-RNNs-Representations/)
* Multi-task learning for continuous control https://arxiv.org/pdf/1802.01034v1.pdf
* http://ruder.io/multi-task