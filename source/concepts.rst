.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

Concepts
========

Pods
----

- The smallest deployment unit in kubernetes is a `Pod <https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/>`_.
- A Pod can contain multiple containers.
- A pod runs a single instance of an application.
- To scale horizontally, use multiple pods.
- A pod has a single IP.
- Containers inside a pod communicate using :code:`localhost`
- Containers inside a pod share storage using Volumes.
- Pods are disposable. To manage scheduling and self-healing, you should create Pods via a Controller.

Controllers
-----------

- Controllers create and manage multiple pods.
- Controllers handle replication, rollout, and self-healing within a cluster
- Types of controller:

  - `Deployment <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/>`_
  - `StatefulSet <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`_
  - `DaemonSet <https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/>`_

- Controllers use Pod Templates to specify how to create the pods

Services
--------

- `Services <https://kubernetes.io/docs/concepts/services-networking/service/>`_ expose pod network endpoints to the cluster
- A service uses a specification to define which pods to route traffic to.
- A service can be used to expose a service externally using the following service types:

  - LoadBalancer
  - NodePort

Ingress
-------

- `Ingress rules <https://kubernetes.io/docs/concepts/services-networking/ingress/>`_ defines cluster external routes
  to connect services inside the cluster to the outside world.
- To create an ingress rule, the cluster must have an
  `Ingress Controller <https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/>`_ running.
- Ingress can be used to arrange:

  - Routing
  - SSL/TLS termination
  - Authentication