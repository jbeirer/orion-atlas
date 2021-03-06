.. highlight:: sh
.. _vm:

Setting up your Virtual Machine
==============================================
In order to set up you personal virtual machine (VM), please follow 
the `CERN OpenStack tutorial`_ on how to create a personal linux VM. As MongoDB likes consuming memory (see the `storage section`_ in the MongoDB docs), 
make sure to choose the m.large flavour to avoid memory issues later on. Also see the chapter on creating a sharded cluster to further alleviate memory pressure, if required.

Configuring the VM for MongoDB usage
------------------------------------------
In order to avoid potential problems with using MongoDB on your VM, the following
configuration is strongly recommended:

**1. Create a swap space to prevent Out-of-memory (OOM) kills from the Linux kernel.** 
A swap file is used to swap memory from RAM to disk in case the memory consumption comes close to the available RAM. This is useful to avoid the Linux kernel from killing your MongoDB instance if you (temporarily) come close to the memory threshold. However, note that writing on disk is significantly slower than RAM performance, such that if your database uses the swap for an extended period of time, it will likely crash. To create a swap file of 1Gb you can:
   
      .. code-block:: bash

         $ sudo dd if=/dev/zero of=/swapfile count=1024 bs=1MiB
         $ sudo chmod 600 /swapfile
         $ sudo mkswap /swapfile
         $ sudo swapon /swapfile

In order to keep the swapfile active on re-boot, add 

      .. code-block:: bash

         $  /swapfile none swap sw 0 0

in the ``/etc/fstab`` configuration file. 

**Further recommendations**

* Set the swappiness to one by adding ``vm.swappiness=1`` to the ``/etc/sysctl.conf`` configuration file. This will tell the system to avoid swapping whenever possible, which is the most performant option for MongoDB.
* Tell the system not to commit more virtual address space than swap space plus a faction of the overcommit ratio with

      .. code-block:: bash

         $  echo "2" > /proc/sys/vm/overcommit_memory
         $  echo "80" > /proc/sys/vm/overcommit_ratio

The overcommit setting controls what happens when processes request too much memory from the operating system. Depending on the setting, the kernal may give memory to processes even it is actually not available. The standard settings do not work well with MongoDB.

**2. Increase the maximum number of allowed open file descriptors and other Unix related limits.** Linux imposes certain limits on the amount of various system resources available to a user process. These limitations include how many files a process can have open, how large of a file the user can create, and how much memory can be used by the different components of the process such as the stack, data and text segments. You can check some of those limits using

      .. code-block:: bash

         $  ulimit -a

Some of these limits will negatively impact the performance or might even lead to crashes of your MongoDB instance. For instance, if the number of open file descriptors ``open files`` exceeds the limit imposed by the kernel, your DB will crash with a ``Too many open files`` error. In order to pevent this and other issues, the following settings are recommended:

1. Increase the maximum number of file handles that the Linux kernel will allocate to the maximum by adding ``fs.file-max=2147483584`` to the ``/etc/sysctl.conf`` config file (the default setting allows a total of 715 428 file handles). In order for the changes to take effect, run ``sysctl -p``. You should see the new configuration appear in ``/proc/sys/fs/file-max``.
2. Increase the maximum number of memory map areas a process may have by setting ``vm.max_map_count=268431360`` in the ``/etc/sysctl.conf`` config file. In order for the changes to take effect, run ``sysctl -p``.
3. Increase the hard and soft limits of open file descriptors to 1048576 (default 1024) in ``/etc/security/limits.conf``.
4. Increase the maximum number of threads in ``/proc/sys/kernel/threads-max`` to 1048576 (default 56800).
5. Increase the maximum number of a PID in ``/proc/sys/kernel/pid_max`` to 4194304 (default 32768).

You can find more information on the recommended MongoDB Unix limit settings in the `ulimit section`_ of the MongoDB documentation. Please note that there are also further limit settings which need to be sonfigured on the MongoDB side. Please see the section on how to :ref:`set-up a MongoDB instance on the VM<db>` for more information.


..
   Links
..

.. _CERN OpenStack tutorial: https://clouddocs.web.cern.ch/tutorial_using_a_browser/create_a_virtual_machine.html
.. _storage section: https://docs.mongodb.com/manual/faq/storage/
.. _ulimit section: https://docs.mongodb.com/manual/reference/ulimit/



