##### ROS

* Communication
  * Topics, actions, services
  - Usually uses TCP for inter-node communication because it's reliable
  - Use UDP (unreliable datagram protocol) when reducing latency is more important than reliability
  - ROS master acts as a database of running ROS nodes and their sub/pub topics. When a node wants to subscribe to a topic, it asks the ROS master for the publisher's info, and then the nodes/processes communicate amongst each other.
