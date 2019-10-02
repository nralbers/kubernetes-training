.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

Advanced Topics
===============


Statefulsets
-------------

`StatefulSets <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`_ are used to manage the
deployment of stateful applications.

- Similar to a deployment **BUT**
- Guarantees the ordering and uniqueness of Pods
- Use when you need one or more of:

  - Stable, unique network identifiers
  - Stable, persistent storage
  - Ordered, graceful deployment and scaling
  - Ordered, automated rolling updates

Use case
^^^^^^^^

- Clustered applications such as Consul, Cassandra, Prometheus Alertmanager.
- Databases (to ensure persistent storage remains available if the database pod is rescheduled.

Daemonsets
----------

`DaemonSets <https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/>`_ deploy Pods that are available
on all nodes matching a specification.

Use case
^^^^^^^^

- running a kubernetes cluster storage daemon on each node
- running a log collection daemon on each node
- running a node monitoring daemon on each node


Jobs and CronJobs
-----------------

A `Job <https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/>`_ creates one or more Pods
and ensures that a specified number of them run successfully to completion.

CronJob
^^^^^^^

A `CronJob <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/>`_ creates
`Jobs <https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/>`_ according to a schedule.

See `Running Automated Tasks with a CronJob <https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/>`_
for more information