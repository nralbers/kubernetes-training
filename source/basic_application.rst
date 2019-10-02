.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

Creating a simple application
=============================

To create a simple application, we will need 2 things:

- A deployment definition
- A service definition

Normally, you would create the service definition FIRST.

.. _basic_example:

Example Deployment
------------------

*nginx-deployment.yaml*

.. code-block:: yaml



    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80

This can be deployed to a cluster by running :code:`kubectl apply -f nginx-deployment.yaml`

Example service
---------------

*nginx-service.yaml*

.. code-block:: yaml

    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
    spec:
      selector:
        app: nginx
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80

This can be deployed to a cluster by running :code:`kubectl apply -f nginx-service.yaml`

Viewing the service outside the cluster
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The general method for exposing a service to the outside world is to install an
`Ingress Controller <https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/>`_ and define an
`ingress rule <https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource>`_ for the service.

Alternatively, you can add a :code:`type: <foo>` property to the :code:`spec:` where :code:`<foo>=` :

- `LoadBalancer <https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer>`_
- `NodePort <https://kubernetes.io/docs/concepts/services-networking/service/#nodeport>`_

For testing purposes, you can use: :code:`kubectl port-forward`

**Examples**

.. code-block:: bash

   # Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in the pod
  kubectl port-forward pod/mypod 5000 6000

   # Listen on port 8888 forwarding to 5000 in the pod
   kubectl port-forward pod/mypod 8888:5000

Order of creation
^^^^^^^^^^^^^^^^^

You should create service definitions before you create the deployment it references and before any resources
that use it.
This is because kubernetes will expose the service properties to the environment of any containers created after the
service is created. For more information, see
`Discovering services <https://kubernetes.io/docs/concepts/services-networking/service/#discovering-services>`_ in the
Kubernetes documentation.



