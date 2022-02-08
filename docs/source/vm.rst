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

1. Create a swap space to prevent Out-of-memory (OOM) kills from the Linux kernel. A swap file is used to swap memory from RAM to disk in case the memory consumption comes close to the available RAM. This is useful to avoid the Linux kernel from killing your MongoDB instance if you (temporarily) come close to the memory threshold. However, note that writing on disk is significantly slower than RAM performance, such that if your database uses the swap for an extended period of time, it will likely crash. To create a swap file of 1Gb you can:
.. code-block:: console

   $ sudo dd if=/dev/zero of=/swapfile count=1024 bs=1MiB
   $ sudo chmod 600 /swapfile
   $ sudo mkswap /swapfile
   $ sudo swapon /swapfile

In order to keep the swapfile active on re-boot, add 
.. code-block:: console

   $  /swapfile none swap sw 0 0

in the ``/etc/fstab`` configuration file. 







..
   Links
..

.. _CERN OpenStack tutorial: https://clouddocs.web.cern.ch/tutorial_using_a_browser/create_a_virtual_machine.html
.. _storage section: https://docs.mongodb.com/manual/faq/storage/



