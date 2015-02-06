---
title: Gem
subtitle: The General Ensemble Model
---

Gem, the General Ensemble Model, is a mathematical model for
ensembles.

Ensembles
---------

To quote the [ASCENS Blog](http://blog.ascens-ist.eu/2011/02/):

<cite>
Ensembles are software-intensive systems with massive numbers of nodes
or complex interactions between nodes, operating in open and
non-deterministic environments in which they have to interact with
humans or other software-intensive systems in elaborate
ways. Ensembles have to dynamically adapt to new requirements,
technologies or environmental conditions without redeployment and
without interruption of the system’s functionality, thereby blurring
the distinction between design-time and run-time.
</cite>

An example of an ensemble are swarms of robots that are used for
rescue operations: To support rescue operations after a natural
disaster or an accident in an industrial plant we want to deploy not
only human rescue parties but also a swarm of robots that support them
by, e.g., exploring and mapping the terrain, securing dangerous
structures, locating victims and transporting them to safety, or by
sealing radioactive or toxic substances.  Since the disaster area is
dangerous and unpredictable we expect the robots to become damaged;
therefore it might be better to deploy many simple and inexpensive
robots rather than very few powerful expensive ones.  That way, if a
single robot falls off a ledge or is damaged by radiation it has little
impact on the swarm of robots.  The disadvantage of having many simple
robots is, of course, that they have to collaborate to achieve the
tasks which a more sophisticated robot might achieve on its own.  And
they have to do so autonomously in an environment with many
imponderables.

Traditional software-engineering techniques are not sufficient for
these kinds of systems.  For example:

* Requirements can often not be specified precisely but only as
  optimization of some value function ("Rescue as many victims as
  possible").

* The environment might be impossible to specify in detail: The rescue
  robots might have to work in diverse environments such as power
  plants or petroleum refineries, and even if the disaster site were
  known in advance (e.g., in a swarm developed for a particular
  factory) the designers cannot know in advance what kind of damage
  will have occurred when the swarm will be deployed.

* In addition the environment may change significantly while the
  ensemble is running.  This is obvious in the case of the rescue
  robots where buildings may collapse or machinery break during the
  rescue mission, but it is also true for many systems that rely on
  the Internet for services.

Gem is a mathematical model for describing the behavior of ensembles.

Gem
---

First of all, Gem is a *mathematical* model, i.e., it is not a
modeling notation or language, it is a way of writing down system
models.  Gem models are essentially relations that are connected to a
logic for specifying properties, and they can be specified in
whichever way is convenient for later analysis.  For example,
[PVS](http://pvs.csl.sri.com/) is an excellent language to specify Gem
models if the goal is to prove properties of the resulting ensemble.
If however we want to analyze the dynamics of the ensemble, then it
might be more useful to express the Gem model using differential
equations and perform their analysis a computer algebra system like
[Maxima](http://maxima.sourceforge.net/).

Having said what Gem is not, how does a Gem model look like?

The core of Gem is based on the general system mode of
[Mihajlo D. Mesarovic](https://en.wikipedia.org/wiki/Mihajlo_D._Mesarovic)
as described in his book
[General Systems Theory: Mathematical Foundations](http://www.ebooks.com/583339/general-systems-theory/mesarovic-m-d-takahara-y/).
The details are contained in the paper
[Towards a System Model for Ensembles](http://www.pst.ifi.lmu.de/~hoelzl/papers/talcott.pdf).
There are some mathematical subtleties involved in getting the details
correct, but the main idea is straightforward to understand.  To
simplify the discussion the following explanation deviates slightly
from the precise definitions presented in the paper.

### State and Trajectory Space

Mesarovic's theory mostly deals with the *behavior* of a system.  At
each point in time there are certain parameters of the system that
describe its current state (or configuration).  The state is typically
restricted to those parts of the system that are relevant for the
problem under investigation, otherwise models would quickly become
unmanageably large.  For example, if we are tracking a robot moving on
a two-dimensional surface, the *x* and *y* coordinates of its center
of gravity might suffice as the state, or we might in addition need
the direction in which the robot is headed, or the configuration of a
gripper it uses to pick up objects.

The set of all possible values of these parameters is the system's
state space.  Each point of the state space therefore describes a
possible system configuration at a certain point of time.  If we track
the evolution of the system over time we obtain a curve through the
state space: At time 0 the robot may be at position *(1,5)*, over the
next 10 seconds it may move to *(2,2)*, and so on.  All possible
trajectories of the systems through its state space form the
*trajectory space*; each member of the trajectory space is a curve
describing the movement of the system through its state space over
time.  Most systems exhibit not a single behavior but rather many
possible movements through their state space; either because of
different control inputs or because they or their environment are
non-deterministic.  To capture these multiple possible behaviors,
a system is represented in Gem as a *relation* over the trajectory
space, i.e., a set of trajectories through the state space.

### Combination Operators

Writing down all possible system behaviors in one fell swoop is only
possible for the simplest of systems.  Therefore it is possible to
compose systems from simpler components: A *combination operator* is a
function that takes multiple relations and uses them to build a new
relation whose behavior depends (1) on the individual components and
(2) on the combination operator itself.  For example, suppose we have
two robots moving in an arena; each robot has its individual $x$ and
$y$ coordinates.  However by putting them together into the same arena
we have built a new system that has the additional constraint that the
robots may not overlap.  In Gem a combination operator, say
*put-in-arena* is responsible for building the relation describing the
two-robot system from the two simpler systems describing a single
robot each, and for enforcing the relevant constraints such as
non-overlapping physical extents of the robots.

If we place the two robots into an arena but connect them using a
piece of rope, we can express this using another combination operator,
say *tie-together*.  This operator is similar to *put-in-arena* but
constrains the resulting relation further by excluding all
trajectories in which the two robots are further apart than the length
of the rope.

### Network and Environment

So far we have only considered the system itself.  But typically
systems are not closed, they interact with their environment.
Therefore each Gem model includes an *environment*.  To model the
effects of the sensors and actuators on system and environment we
often also include a *network* that mediates these interactions.  Like
the system itself, environment and network are modeled as relations
over the trajectory space.  Therefore the interaction between system
and environment, possibly mediated by the network can again be
expressed using a combination operator.  This leads to an elegant
structure of the model: the ensemble operating in an environment is
represented by a relation that may be decomposed into three simpler
relations and a combination operator; each of the simpler system may
in turn be decomposed into a combination operator and even simpler
systems, and we can recurse in this manner until we arrive at the
elementary objects in our model.


Acknowledgments
---------------

The development of Gem was funded by the European project IP 257414
ASCENS ("Software Engineering for **A**utonomic
**S**ervice-**C**omponent **Ens**embles").
