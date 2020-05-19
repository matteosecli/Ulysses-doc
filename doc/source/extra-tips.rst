Extra Tips
==========

.. warning:: **WORK IN PROGRESS!**

Run Interactive Jobs
--------------------

You can ask for a Bash terminal on one or more nodes and then execute all the commands you want. This kind of job is called an **interactive job**.

Interactive jobs are not resource efficient, so their usage should be **limited** to those cases in which it's not possible to use a job script; for example, you might need to interactively debug a code running on GPUs.

As an example, this command asks for 1 CPU on a node either in regular1 or in regular2, for 30 minutes:

.. code-block:: console
   
   $ srun --partition=regular1,regular2 --time=00:30:00 --ntasks=1 --cpus-per-task=1 --pty bash -i

Run Quick, High-Priority Jobs
-----------------------------

You can run a single, **really high-priority** job (e.g. for debugging) by using the ``fastlane`` QoS (Quality of Service), i.e.

.. code-block:: console

   $ sbatch --qos=fastlane send_job.sh

.. warning:: Using the `fastlane` QoS is **not free**, in the sense that you will be "punished" by lowering the proprity of your subsequent jobs. So, use it **only if you really need it!**

You can get a list of the available QoS's via:

.. code-block:: console
   
   $ sacctmgr show qos format=name,priority
         Name   Priority
   ---------- ----------
       normal          0
     fastlane   10000000
         wide          0
         long          0
    priority1     500000

Disable Hyper-Threading
-----------------------

If you want to **totally disable** Hyper-Threading, you can use

.. code-block:: console

   $ sbatch --hint=nomultithread --cpu-bind=cores send_job.sh

Explore Files in a User-Friendly Way
------------------------------------

.. warning:: **WORK IN PROGRESS!**
