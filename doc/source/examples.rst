Examples
========

This section contains examples of job scripts for typical situations. Refer to :ref:`Step 4: Prepare and Send the Job Script` for details on the job script.

I'll skip all the extra comments, as well the part that prints the job info in the output file and the part that loads the modules so to focus just on the relevant bits.

Serial Jobs
-----------

Serial Job -- Without OpenMP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Typical situation for a serial (non-MPI) job. On a cluster without Hyper-Threading 1 CPU is enough, but since on Ulysses we have Hyper-Threading we can ask for 2 (virtual) CPUs in such a way that they correspond to 1 physical core.

.. code-block:: bash
   :caption: Serial job asking for 2 threads (bounded to **1 physical core**), 32 GB of memory and 10 hours on ``regular1``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Serial_Job
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --ntasks=1
   #SBATCH --cpus-per-task=2
   #SBATCH --ntasks-per-core=1
   #
   #SBATCH --mem=32000mb
   #
   #SBATCH --partition=regular1
   #SBATCH --time=10:00:00
   #SBATCH --output=%x.o%j
   #SBATCH --error=%x.e%j
   #

   ## YOUR CODE GOES HERE (load the modules and do the calculations)

Serial Job -- With OpenMP
^^^^^^^^^^^^^^^^^^^^^^^^^

If you are confident that your software implements OpenMP parallelism, you can ask for more threads.

.. code-block:: bash
   :caption: Serial job asking for 20 (OpenMP) threads, 20 GB of memory and 10 hours on either ``regular1`` or ``regular2``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Serial_Job_OpenMP
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --ntasks=1
   #SBATCH --cpus-per-task=20
   #
   #SBATCH --mem=20000mb
   #
   #SBATCH --partition=regular1,regular2
   #SBATCH --time=10:00:00
   #SBATCH --output=%x.o%j
   #SBATCH --error=%x.e%j
   #

   ## YOUR CODE GOES HERE (load the modules and do the calculations)
   
.. note:: You can add the option ``#SBATCH --ntasks-per-core=1`` to make sure that 20 CPUs correspond to actual 10 physical cores.
   
Parallel Jobs
-------------

MPI Job -- Without OpenMP
^^^^^^^^^^^^^^^^^^^^^^^^^

This is an example of a pure MPI job. Again, we ask for 2 CPUs per task but we make sure they correspond to 1 physical core.

.. code-block:: bash
   :caption: Parallel job asking for 32 MPI tasks, each with 2 threads (bounded to **1 physical core**) and 1.98 GB of memory for a total of 12 hours on ``regular2``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Parallel_Job
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --ntasks=32
   #SBATCH --cpus-per-task=2
   #SBATCH --ntasks-per-core=1
   #
   #SBATCH --mem-per-cpu=990mb
   #
   #SBATCH --partition=regular2
   #SBATCH --time=12:00:00
   #SBATCH --output=%x.o%j
   #SBATCH --error=%x.e%j
   #

   ## YOUR CODE GOES HERE (load the modules and do the calculations)

MPI Job -- With OpenMP
^^^^^^^^^^^^^^^^^^^^^^

This is an example of a mixed MPI/OpenMP job, with each MPI process having 8 threads at its disposal.

.. code-block:: bash
   :caption: Parallel job asking for 16 MPI tasks, each with 8 (OpenMP) threads and 7.92 GB of memory for a total of 12 hours on ``regular2``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Parallel_Job_OpenMP
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --ntasks=16
   #SBATCH --cpus-per-task=8
   #
   #SBATCH --mem-per-cpu=990mb
   #
   #SBATCH --partition=regular2
   #SBATCH --time=12:00:00
   #SBATCH --output=%x.o%j
   #SBATCH --error=%x.e%j
   #
   
   ## YOUR CODE GOES HERE (load the modules and do the calculations)
   
.. note:: You can add the option ``#SBATCH --ntasks-per-core=1`` to make sure that 8 CPUs correspond to actual 4 physical cores.

.. note:: These parameters were specifically tailored for nodes in ``regular2``. Each of the nodes in this queue has 64 CPUs and 63.5 GB of memory, so asking for 8 CPUs/task and 990 MB per CPU means that 8 tasks are able to saturate a node. In fact, 990 MB x 8 CPUs x 8 tasks = 63360 MB (the max on these nodes is 63500 MB) and 8 CPUs x 8 tasks = 64 CPUs (equal to the max). So, SLURM would normally allocate 2 nodes to the job; however, if you prepare the script in this way, SLURM could also "fill" the holes left by other jobs and start your job sooner, instead of waiting to have 2 full nodes to employ for your calculation.

This is the same example, but with an explicit setup to ask for 2 full nodes.

.. code-block:: bash
   :caption: Parallel job asking for 16 MPI tasks distributed among 2 nodes, each task with 8 threads and 7.92 GB of memory for a total of 12 hours on ``regular2``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Parallel_Job_OpenMP
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --nodes=2
   #SBATCH --ntasks-per-node=8
   #SBATCH --cpus-per-task=8
   #SBATCH --exclusive
   #
   #SBATCH --mem=0
   #
   #SBATCH --partition=regular2
   #SBATCH --time=12:00:00
   #SBATCH --output=%x.o%j
   #SBATCH --error=%x.e%j
   #
   
   ## YOUR CODE GOES HERE (load the modules and do the calculations)

.. note:: The option ``--mem=0`` is a shorthand for asking all the available memory on each node. The ``--exclusive`` option makes sure that the nodes are not shared among other running jobs (even if these particular settings already saturate all the 2 nodes).

Job Arrays
----------

.. warning:: **WORK IN PROGRESS!**

Dependencies
------------

.. warning:: **WORK IN PROGRESS!**
