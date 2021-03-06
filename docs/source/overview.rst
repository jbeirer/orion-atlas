Orion in ATLAS
==============================================

These pages document the recommended usage of Orion within the ATLAS/CERN 
infrastructure and is intended to help you get started. If this is the first time
you are using Orion, please first take a look at the `Orion documentation`_.


What is Orion?
----------------------------

Oríon is an asynchronous framework for black-box function optimization.

Its purpose is to serve as a meta-optimizer for machine learning models
and training, as well as a flexible experimentation
platform for large scale asynchronous optimization procedures.

Core design value is the minimum disruption of a researcher's workflow.
It allows fast and efficient tuning, providing minimum simple non-intrusive
(not even necessary!) helper *client* interface for a user's script.


How will Orion simplify your life?
------------------------------------------

The optimization of hyperparameters in ML models is a classical example of 
a black-box optimization problem, which in general is a cumbersome and time-consuming
activity and in some cases requires significant computing resources. Orion will allow
you to provide a simple user script which is expected to return some kind of metric,
which Orion will aim to minimize. This means that you will not be required to significantly
modify your current workflow and simply continue using the scripts you are already using 
to train your ML models. 

While you can use Orion on your local machine or on lxplus, the real power of 
Orion unfolds when using its distributed capabilities. Orion cannot only be run 
on multiple cores, but it can distribute its optimization procedure across multiple 
machines. As many Orion workers as required can be launched to simultanously work on
an optimization problem. The individual workers make decisions based on a common shared 
database. Operations in the database are non-blocking, ensuring horizontal scalability 
for large search spaces. 

What is the recommended usage of Orion in ATLAS?
------------------------------------------------

For small-scale optimization problems or tests, you should use the standard instructions
provided in the `Orion documentation`_. However, 
for large-scale optimizations, and if you want to profit from the distributed capabilities
of Orion a few things will need to be set up first. This documentation will guide you through
the necessary steps. 

The general idea is to use HTCondor jobs submitted through lxplus as Orion workers. Orion
supports various `executor backends`_. In order to use HTCondor jobs as Orion workers, we will employ Dask, an open source library for parallel 
computing written in Python, as a backend in order to spawn the workers and keep them alive. For more information on Dask, 
please take a look at the `Dask documentation`_.

As mentioned, the workers make a decision based on a shared common database (DB). If we want to use HTCondor jobs
as Orion workers, the jobs need to have continuous access to a shared database. Keep in mind that HTCondor jobs
are isolated from the outside world (and this for good reason!), such that we are not able to use cloud-hosted
DB services such as Amazon Web Services (AWS). Instead, we can host our own database on a virtual machine (VM)
using `CERN OpenStack resources`_, which can be reached within the network by the HTCondor jobs. While Orion supports a variety of different database, MongoDB, a document-based
NoSQL database is the recommended backend for large-scale parallel optimizations, where the number of workers gets higher than 50. 
You can find out more about the database in the `MongoDB documentation`_. You can follow these (easy) steps to get a working set-up:

1. :ref:`Set-up a VM using CERN OpenStack resources<vm>`
2. :ref:`Set-up a MongoDB instance on the VM<db>`
3. Configure Orion

..
   Links
..
.. _Orion documentation: https://orion.readthedocs.io
.. _executor backends: https://orion.readthedocs.io/en/stable/user/parallel.html
.. _Dask documentation: https://docs.dask.org/en/stable/
.. _MongoDB documentation: https://docs.mongodb.com/
.. _CERN OpenStack resources: https://clouddocs.web.cern.ch/index.html



