# Intuitive physics (learning physics models)

* Look at [Intuitive Physics Workshop at NIPS 2016](http://phys.csail.mit.edu/program.html)

### Papers

Some of the links are to blog.acolyer

* [Visual Interaction Networks - Deepmind](https://arxiv.org/pdf/1706.01433.pdf)
    * Builds on Interaction Networks by learning a network that encodes an image as a graph representation of objects and relationships
* [Interaction Networks - Deepmind](https://blog.acolyer.org/2017/01/02/interaction-networks-for-learning-about-objects-relations-and-physics/)
    * Learn two main functions: effect of relationships between objects, and how those effects influence next state of objects
    * "If each object has a state vector of length $D_s$, and there are $N_o$ objects, then $O$ is represented as a $D_s$ \times N_o matrix.
Relationships are represented by three matrices: $R_r$ is a binary vector whose $j^{th}$ column is a one-hot vector indicating the receiver object of the $j^{th}$ relation; $R_s$ similarly identifies sender objects. The final matrix, $R_a$ encodes the attributes of each relationship."
* [Relation networks - Deepmind](https://arxiv.org/abs/1706.01427)
* [Deep Visual Foresight for Planning Robot Motion - Finn and Abbeel](http://phys.csail.mit.edu/papers/6.pdf)
* [Learning to Poke by Poking - Abbeel and Levine](https://papers.nips.cc/paper/6113-learning-to-poke-by-poking-experiential-learning-of-intuitive-physics.pdf)
* [PhysNet - Facebook](https://arxiv.org/abs/1603.01312)
* [Building machines that learn and think like people](https://blog.acolyer.org/2016/11/25/building-machines-that-learn-and-think-like-people/)
    * Read paper! 50 pages but looks like it's worth it

* Learning fluid dynamics from Google
    * <http://cims.nyu.edu/~schlacht/CNNFluids.htm>
    * <https://github.com/google/FluidNet>
