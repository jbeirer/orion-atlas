.. highlight:: sh
.. _db:

Setting up you MongoDB
==============================================
In order to set up your MongoDB instance on your Virtual Machine (VM), please see the MongoDB `installation documentation`_, specifically for `CentOS`_.

Most importantly, make sure that you either deactivate the SELinux policy by setting ``SELINUX`` to ``disabled`` in ``/etc/selinux/config`` and re-boot your VM or make the suggested adjustments to the SELinux policy in order to allow MongoDB access to ``/sys/fs/cgroup`` and ``/proc/net/netstat``, which is required for MongoDB to function properly (recommended). **Ignoring this step will inevitably lead to permission issues.**

Once you installed your MongoDB instance, you can check the proper functioning by starting a `MongoDB Shell`_ using ``mongosh``. In order for the MongoDB instance to be reachable through ``lxplus`` and ``HTCondor``, you need to deactivate the firewall using

      .. code-block:: bash

         $ firewall-cmd --zone=public --add-service=mongodb
         $ firewall-cmd --zone=public --permanent --add-service=mongodb

.. _installation documentation: https://docs.mongodb.com/manual/installation/
.. _CentOS: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/
.. _MongoDB Shell: https://docs.mongodb.com/mongodb-shell/#mongodb-binary-bin.mongosh

Moreover, you should set ``net.bindIpAll: true`` and ``net.maxIncomingConnections: 1048576`` in your MongoDB config file ``/etc/mongod.conf``. While the first option makes sure that you will have access to the DB through the HTCondor jobs, the second option increases the number of maximum allowed incomming connections. 