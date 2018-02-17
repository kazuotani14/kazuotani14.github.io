---
title:  "Time in robots"
---

This is a compilation of notes on lectures and emails from Chris Baker on the topic of time. I highly recommend going through his slides on Robotic Software Systems. Some of it will go over your head on the first few passes, but there's a lot of good stuff in there. In our project, we didn't realize that time was so important for our project until almost 6 months after Chris had first given us the lecture.

<img src="https://kazuotani14.github.io/media/mrsd_teamd_wiki/chris_baker_time.png" alt="time diagram" style="width: 500px;"/>

At high vehicle speeds, consider latency (sensor physics, data link from sensor to computer). Sensors were in past, actuation will be in future. Timestamp everything! Vehicle/obstacle models must be predictive through time. Also have to consider time constant of vehicle dynamics.

Motion planner (soft real-time constraints) -> Motor controller (hard real-time)

* “Plan to fail”. ex. at 10Hz, publish plan for next 5 seconds. 2.5s of normal operation, 2.5s of smooth stop
* Fault rejects further commands until cleared

Also consider synchronization of sensors

Beware of quantization issues with floating-point math in time -> always do math with relative times (ros time resets to zero every time system starts)

ROS nodelets for speed? allows zero-copy passing of messages 

<https://www.clearpathrobotics.com/assets/guides/ros/Nodelet%20Everything.html>

“Driving is an intensely social experience.” Chris Baker (trust with people, system)

Thresholds for smooth, fast, emergency braking: ~0.5g, 1g, 2g

* Initialization: for configurations/“parameter soup”, pick a single mechanism and stick with it. 
   * Should we have a more explicit “Initialize” routine?
   * Would it be better/more consistent to have all of our parameters, in here, i.e. dynamics model, PID gains, etc.?
* Runtime: “message/notification pump” that receives new data, passes to “handlers” that process data, notify status, send new updated commands, deal with periodic work (ex. ROS spin)
      *Must be able to respond to absence of valid data (periodic/timely data arrival is an implicit liveness indicator for your device)
* Cleanup: put device into a safe state, report “clean shutdown”

Recurrent theme in design patterns for robotics: Model-View-Controller architecture. Allows for layered designs, where one layer’s view is another layer’s model 
* View
   * Clients that use
   * User interfaces
   * “higher level” controllers
* Controllers
   * rules that govern
   * sense-plan-act
* Models
   * stuff that is
   * perception data
   * state of universe, or simulation
    
In a flow diagram showing software architecture
* boxes could be:
   * classes, programs, libraries
* lines could be:
   * method calls, global variables, messages
    
State management: want clarity and enforcement of rules for who owns, sees, alters the state
* const expressions to control changing
* Singleton (use static members) to control replication
    
Observer (publish/subscribe) pattern
* Pros
   * Anonymity/decoupling
   * No polling latency
   * Very low overhead
* Cons
   * Debugging complex notification patterns
   * Cycles = Notification storms
    
Algorithm vs program
* Instead of having ex. a ros node directly embody/implement an algorithm, let another node be the glue between algorithm and ros node
   * Direct embodiment “locks in” to infrastructure, and requires full system to test
   * With glue approach, can test algorithm separately
   * Glue will gather system data, inject into algorithm, run it, publish results, handle errors

When using a motion planner that takes time to find a plan, we must somehow account for the fact that the plan was generated using information from the past. The general approach for this is to predict where the robot will be when the planning is done, and tell the plan to assume it will start from there. Strategies for picking the replanning epoch vary in the details, but generally take one of three forms:

1.  Pick a hard number in the future, typically empirically derived from "how long the planning cycle usually takes", and hope for the best.  While simplistic, this satisfies 90+% of use cases and is easily adjustible.
    
2. Reframe your planning problem as an "anytime" or "optimization" problem, and process for an explicit amount of time.  That is, check the clock as part of your definition of "done" and return the best answer you were able to find within that fixed amount of time.  The problem is that not all planning problems submit themselves to an anytime approach, though for most driving situations, you could prioritize the nominal (keep driving straight) and extreme-exceptional (swerve maneuvers, hard stops, etc.) cases, then fill in the space between as time permits. This is more or less the approach we took for the Urban Challenge.
    
3. Split your problem into trajectory planning and trajectory blending/optimization: you usually still start from some epoch in the future, but instead of just using the resulting plan verbatim, perform a blending or local optimization step with very predictable runtime once the plan is created.  This will have the added benefit of allowing you to compensate for drift from the previous plan that occurred while you were busy thinking.  Something like the following:

~~~~
void replan()
{
   const TimeStamp replanStart(getSynchronizedTime());
   const TimeStamp replanEpoch(replanStart + 0.1);
   const Plan oldPlan(getCurrentPlan());
    
   const State predictedState(getPredictedStateAt(replanEpoch));
   const Plan newPlan(generatePlanFrom(replanEpoch,predictedState);
    
   // nontrivial time will have been consumed by generatePlanFrom
   // also, the real current state will have drifted away from the
   // prediction, so lets account for both sources of error here
   const TimeStamp replanEnd(getSynchronizedTime());
   const TimeStamp refineEpoch(replanEnd + 0.025);
    
   const State     afterPlanState(getCurrentState());
   const Plan refinedPlan(refine(newPlan,afterPlanState,refineEpoch);
   emit(refinedPlan);
}
~~~~

In this case, you could actually just replan from "now", or even from your most recent state estimate and count on the refinement/blending process to glue things together "just in time".  This in turn would allow you to emit your plan as soon as it's ready, in turn shortening your latency stackup in the case of novel sensor data that drastically affects your trajectory.  The tradeoff here is the intense juggling act, and the plethora of opportunities to get yourself thoroughly confused about "then", "now" and "when.

