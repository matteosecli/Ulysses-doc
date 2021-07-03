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
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Run calculation
   ./my_program.x

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
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Optionally set the number of OpenMP threads as you want
   # Example:     export OMP_NUM_THREADS=$((${SLURM_CPUS_PER_TASK}/2))
   
   # Print info on current number of OpenMP threads
   "Using "$(($OMP_NUM_THREADS))" OpenMP threads."
   
   # Run calculation
   ./my_program.x
   
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
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Run MPI calculation
   mpirun -np $SLURM_NTASKS ./my_program.x

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
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Optionally set the number of OpenMP threads as you want
   # Example:     export OMP_NUM_THREADS=$((${SLURM_CPUS_PER_TASK}/2))
   
   # Print info on current number of OpenMP threads
   "Using "$(($OMP_NUM_THREADS))" OpenMP threads."
   
   # Run MPI calculation
   mpirun -np $SLURM_NTASKS ./my_program.x
   
.. note:: You can add the option ``#SBATCH --ntasks-per-core=1`` to make sure that 8 CPUs correspond to actual 4 physical cores.

.. note:: These parameters were specifically tailored for nodes in ``regular2``. Each of the nodes in this queue has 64 CPUs and 63.5 GB of memory, so asking for 8 CPUs/task and 990 MB per CPU means that 8 tasks are able to saturate a node. In fact, 990 MB x 8 CPUs x 8 tasks = 63360 MB (the max on these nodes is 63500 MB) and 8 CPUs x 8 tasks = 64 CPUs (equal to the max). So, SLURM would normally allocate 2 nodes to the job; however, if you prepare the script in this way, SLURM could also "fill" the holes left by other jobs and start your job sooner, instead of waiting to have 2 full nodes to employ for your calculation.

This is the same example, but with an explicit setup to ask for 2 full nodes.

.. code-block:: bash
   :caption: Parallel job asking for 16 MPI tasks distributed among 2 nodes, each task with 8 (OpenMP) threads and 7.92 GB of memory for a total of 12 hours on ``regular2``. The output and error filenames are in TORQUE style.
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
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Optionally set the number of OpenMP threads as you want
   # Example:     export OMP_NUM_THREADS=$((${SLURM_CPUS_PER_TASK}/2))
   
   # Print info on current number of OpenMP threads
   "Using "$(($OMP_NUM_THREADS))" OpenMP threads."
   
   # Run MPI calculation
   mpirun -np $SLURM_NTASKS ./my_program.x

.. note:: The option ``--mem=0`` is a shorthand for asking all the available memory on each node. The ``--exclusive`` option makes sure that the nodes are not shared among other running jobs (even if these particular settings already saturate all the 2 nodes).

Job Arrays
----------

Job arrays are a handy way to send multiple jobs that vary e.g. just by some parameters of the calculation. SLURM's documentation has a very well-written page on job arrays, I suggest you to take a look for more details and examples: https://slurm.schedmd.com/job_array.html. Here I'll just show a couple of examples.

A job array is specified via the :data:`--array=<value>` option (see :ref:`Partition, Walltime and Output`), that takes a range of integers as ``<value>``. This range can be specified as an interval, e.g. ``1-10`` (numbers from 1 to 10), or as a sequence, e.g. ``3,5,23``, or both, e.g. ``1-5,13`` (numbers from 1 to 5, then 13).

For example, if one uses the option :data:`--array=1-5,13`, then SLURM will generate 6 different jobs, each of one containing the following environment variables:

.. table::
   :align: center
   :widths: auto
   
   +-----------------------------+-----------------------------------------+
   | Environment Variable        | Value                                   |
   +=============================+=========================================+
   | ``$SLURM_ARRAY_TASK_ID``    | One of the following: ``1,2,3,4,5,13``. |
   +-----------------------------+-----------------------------------------+
   | ``$SLURM_ARRAY_TASK_COUNT`` | ``6`` (number of jobs in the array)     |
   +-----------------------------+-----------------------------------------+
   | ``$SLURM_ARRAY_TASK_MAX``   | ``13`` (max of given range)             |
   +-----------------------------+-----------------------------------------+
   | ``$SLURM_ARRAY_TASK_MIN``   | ``1``  (min of given range)             |
   +-----------------------------+-----------------------------------------+

In other words, each of these 6 different jobs will have a variable ``$SLURM_ARRAY_TASK_ID`` containing one (and only one) of the numbers given to :data:`--array`. This variable can then be used to generate one or more parameters of the simulation, in a way that's completely up to you.

.. note:: Array ranges can additionally be specified with a step. For example, to generate multiples of 3 up to 21, you can use :data:`--array=0-21:3`.

.. note:: You can also specify a maximum number of jobs in that array that are allowed to run at the same time. For example, :data:`--array=1-20%4` generates 20 jobs but only 4 of them are allowed to run at the same time.

Serial Job Array
^^^^^^^^^^^^^^^^

.. code-block:: bash
   :caption: Serial job array asking for 2 threads (bounded to 1 physical core), 990 MB of memory and 6 hours for each of the 32 jobs, on ``regular2``. The output and error filenames are in TORQUE style.
   :linenos:

   #!/usr/bin/env bash
   #
   #SBATCH --job-name=Array_Job
   #SBATCH --mail-type=ALL
   #SBATCH --mail-user=jdoe@sissa.it
   #
   #SBATCH --ntasks=1
   #SBATCH --cpus-per-task=2
   #SBATCH --ntasks-per-core=1
   #
   #SBATCH --mem-per-cpu=990mb
   #
   #SBATCH --array=1-32
   #SBATCH --partition=regular2
   #SBATCH --time=06:00:00
   #SBATCH --output=%x.o%A-%a
   #SBATCH --error=%x.e%A-%a
   #

   ## YOUR CODE GOES HERE (load the modules and do the calculations)
   ## Sample code:
   
   # Make sure it's the same module used at compile time
   module load intel
   
   # Calculate the parameter of the calculation based on the array index,
   # e.g. in this case as 5 times the array index
   PARAM=$((${SLURM_ARRAY_TASK_ID}*5))
   
   # Run calculation
   ./my_program.x $PARAM
   
.. note:: This workload is based on the specifics of the regular2 nodes. With these numbers you should be able to occupy even just a single node, if it's available; but hey, nonetheless you are running 32 calculations at the same time! 😄

Dependencies
------------

SLURM allows the jobs dependency adding a simple flag to the `sbatch` command 
.. code-block:: console
   sbatch --dependency=<type:jobid[:jobid][,type:jobid[:jobid]]> ...

.. table::
   :align: center
   :widths: 1 3

   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | Command                                      | Meaning                                                                                                                     |
   +==============================================+=============================================================================================================================+
   | ``after:jobid``                              | job can begin after the specified jobs have started                                                                         |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | ``afterany:jobid``                           | job can begin after the specified jobs have terminated                                                                      |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | ``afternotok:jobid``                         | job can begin after the specified jobs have failed                                                                          |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | ``afterok:jobid``                            | job can begin after the specified jobs have run to completion with an exit code of zero (see the user guide for caveats).   |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | ``singleton``                                | jobs can begin execution after all previously launched jobs with the same name and user have ended.                         |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+

A command that I found usefull suppose you have ``n`` jobs that are dependent and for each ``i-th`` job you have a script called job_i.sh, then you can use the following command:

.. code-block:: console
   
   ID=($(sbatch job_1.sh )) 
   for i in $(seq 2 n ); do 
      ID=($(sbatch --dependency=type:${ID[3]} job_$i.sh ))
   done
