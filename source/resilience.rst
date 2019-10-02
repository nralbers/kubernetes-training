Application Resilience
======================

Kubernetes has **many** facilities for ensuring application reliability. These are (not a complete list):

- Deployment strategies
- Replicating Pods and Scaling Pods
- Detection of unhealthy containers and Pods
- Setting resource requirements and limits on Pods
- Setting node affinity or taints (disallowing certain workloads on nodes)


Deployment strategies
---------------------

Setting a deployment strategy will determine **how** Kubernetes replaces the running Pods when you change
the deployment image version.

The strategies are:

- `Recreate <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#recreate-deployment>`_ .
  This will remove all existing Pods before new ones are created
- `Rolling Update <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment>`_.
  This will replace Pods sequentially. The update can be controlled using :code:`maxUnavailable` and :code:`maxSurge`

Replication and Scaling
-----------------------

The :ref:`basic application example <basic_example>` is already using replication. It will create 3 Pods.
Since there are no other requirements set in the deployment, these will be evenly spread over the available nodes.

It is possible to scale a deployment down or up using the :code:`kubectl scale` command.

.. code-block:: bash

    kubectl scale deployment/nginx-deployment --replicas=2


Full documentation on scaling deployments can be found
`here <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#scaling-a-deployment>`_

Detection of unhealthy containers
---------------------------------

To allow Kubernetes to determine if a container is running properly, it allows developers to specify
`Container probes <https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes>`_ .


These consist of a probe and a handler.

Handlers:

- ExecAction: Runs a command inside the container. If the command returns 0, the diagnostic is successful
- TCPSocketAction: Performs a TCP check against a specific port. Successful if the port is open.
- HTTPGetAction: Performs a HTTP GET request against the specified port and path. Successful if the status code
  is in the range  200 <= status < 400

Probes:

- :code:`livenessProbe` : Indicates if the container is running. On failure, Container will be killed,
  and the restart policy is applied. If a process running in a container will crash and exit if it becomes unhealthy,
  you do not need a liveness probe.
- :code:`readinessProbe` : Container is ready to service requests. On failure, Pod IP will be removed from any matching
  Services.
- :code:`startupProbe`: Indicates that the container is started. Disables all other probes until the container is started.

Example
^^^^^^^

Liveness probe

.. code-block:: yaml

    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        test: liveness
      name: liveness-http
    spec:
      containers:
      - args:
        - /server
        image: k8s.gcr.io/liveness
        livenessProbe:
          httpGet:
            # when "host" is not defined, "PodIP" will be used
            # host: my-host
            # when "scheme" is not defined, "HTTP" scheme will be used. Only "HTTP" and "HTTPS" are allowed
            # scheme: HTTPS
            path: /healthz
            port: 8080
            httpHeaders:
            - name: X-Custom-Header
              value: Awesome
          initialDelaySeconds: 15
          timeoutSeconds: 1
        name: liveness

More information
^^^^^^^^^^^^^^^^

`Configure Liveness, Readiness and Startup Probes <https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/>`_


Setting resource requirements and limits
----------------------------------------

Each container in a Pod can `request and specify limits on cpu and memory resources
<https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#resource-requests-and-limits-of-pod-and-container>`_.

Unit definitions
^^^^^^^^^^^^^^^^

One Kubernetes CPU unit is equivalent to:

.. sidebar:: CPU Units

    CPU is an absolute property. 1 cpu is the same amount on a single core as it is on a 48-core machine.
    Fractional units are allowed. Minimum precision is 1m (one milli-cpu).

- 1 AWS cCPU
- 1 GCP Core
- 1 Azure vCore
- 1 IBM vCPU
- 1 hyperthread on an Intel processor

Memory is measured in bytes, and can either be supplied as a plain integer, or a fixed point integer using the following
suffixes: E, P, T, G, M, K. You can also use the power-of-two equivalents: Ei, Pi, Ti, Gi, Mi, Ki

Example
^^^^^^^

.. code-block:: yaml

    apiVersion: v1
    kind: Pod
    metadata:
      name: frontend
    spec:
      containers:
      - name: db
        image: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "password"
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
      - name: wp
        image: wordpress
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"

Node scheduling
^^^^^^^^^^^^^^^

When scheduling a Pod, it will check if the available resources on the node have sufficient capacity to meet both
the resource request of the Pod, and the limits. A container that exceeds it's limits might be terminated and restarted.


Node affinity and taints
------------------------

For information on this feature, see the
`kubernetes documentation <https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#node-affinity-beta-feature>`_






