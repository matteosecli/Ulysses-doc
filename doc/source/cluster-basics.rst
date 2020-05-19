Cluster Basics
==============

.. figure:: res/ulysses.jpg
   :width: 67%
   :alt: ulysses
   :align: center

   A picture of (part of) Ulysses.

What is a Cluster? When and Why Should I Use It?
------------------------------------------------

Using a cluster starts by having a rough knowledge of how it works.

A (computing) cluster is just a bunch of computers, called **nodes**, wired together via a high-speed connection; each of these computers is generally quite powerful itself. So, you can take advantage of a cluster in 2 scenarios.

* You have to do a super-demaning calculation, but this calculation can be split into smaller calculations that are performed at the same time by multiple nodes. All of these nodes communicate their results and/or status with each other, typically multiple times during the calculation, via the high-speed connection that links the nodes. A typical "language" that allows nodes to communicate with each other on this high-speed connection is MPI_ (Message Passing Interface).
  This scenario is referred to as a **parallel job** or parallel calculation.
* You have to do a calculation that could in principle run fine on a powerful enough desktop workstation, but you have to *repeat* this calculation *lots* of times (maybe by varying some parameters). In this case, having a cluster is like having *lots* of workstations on which you can run many of these calculations at the same time, but these calculations don't need to communicate with each other.
  This scenario is referred to as a **serial job** or serial calculation.

.. _MPI: https://en.wikipedia.org/wiki/Message_Passing_Interface

Long story short: if you have to run *a single or few* calculations that can fit your workstation's computing capabilities, just *don't use a cluster*. You'd be better off just using your own workstation.

.. note:: Jobs that perform multi-core calculations are definitely implementing a form of parallelism, but if no communication is performed among different nodes then these jobs still fall under the category "serial job". The "parallel" here refers to the fact of communicating among different nodes

What's a Resource Manager?
--------------------------

A cluster is a **shared resource**. It's an important aspect to keep in mind, that makes its usage fundamentally different from using your office workstation; while you typically are the only user of your workstation, which means that you can run calculations whenever you want and for how long you want, on a cluster there are tons of users that want to run calculations at the same time. It's clear that if all the users are allowed to run whatever number of calculations all at the same time, this big boy will crash badly, **very badly**, no matter how big and powerful it is.

The solution to this problem is to employ a **resource manager**, sometimes called **queue manager**. The term "queue manager" is actually not totally correct, as these systems manage far more than just the queues, but I'd say that for our purposes the term "queue manager" gives a sufficiently intuitive idea of what this thing does.

Imagine you want to have fun and you decide to have a trip to Gardaland (an Italian amusement park). As there are multiple attractions, you have to:

* Decide whether to go to e.g. "Kung Fu Panda Master" or "Jungle Rapids" or "Blue Tornado".
* Once you decide which attraction you want to go to, you have to **queue up** for that attraction.

.. figure:: res/park-queue.png
   :width: 67%
   :alt: park-queue
   :align: center

   Queues at an amusement park.

The park employee then makes sure that no one skips the queue, and assigns the visitors to the attraction's pods based on how people are grouped and on the available places. Let's say for example that a pod can host max. 6 people and that at the top of the queue there are a family of 5 people, another family of 3 people, and you (alone 😞). As an empty pod arrives, the queue manager lets the 5-people family on; an empty place is left, but the next in line is the 3-people family and it doesn't fit. A not-so-effective queue manager could wait for the next empty pod and let the 3-people family and you on it, but this is both a waste of time and resources for the park (filling all the empty spots means more \$ for the park!) and for you, since you have to wait for the next pod despite the fact that the 5-people family left an empty spot on their pod. Instead, an effective queue manager would realize that you could fit in that empty spot and, despite you're not the next-in-line, would let you in. Or maybe the queue manager would let in a person even far behind you, because maybe that person has bought a *priority* ticket.

That's how the resource manager in a cluster works, more or less. In order for your calculation to be executed, you have to:

* Choose the right queue, called **partition** in our resource manager. The right partition depends on what you have to do; different partitions typically differ on the maximum time your calculation has got to finish, on the quantity of computing resources you can ask (e.g. how many CPUs or how much RAM), or on the kind of specific resources you can ask (e.g. some partitions are equipped on GPUs, some are not).
* Queue up for that queue. The way you explain to the cluster that you want your calculation to be queued up in that partition and that your calculation needs to use a certain kind/amount of resources is through a **job script**.

The **job script** is where all the magic happens and it's all you need to perform calculations on a cluster. In the next sections we'll go into a more detailed explanation of the job script.

.. warning:: There's a way to ask for resources and queue for a partition even *without* a job script. This way of doing things is called an **interactive job**. There are some people that instruct new users to use the cluster only via these interactive jobs and don't explain **at all** what a job script is and why you should use it. This is **TOTAL BULLSHIT!** Unless you have a **really good and strong** reason to use an interactive job, **ALWAYS USE A JOB SCRIPT**. Not only because it forces you to be *programmatic*, but also because minimizes *a lot* the waste of computational resources.







