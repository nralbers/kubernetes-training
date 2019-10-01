.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

Container configuration
=========================

Almost every application deployed into a cluster will need at least one or more of the following configuration items:

- Ports
- Environment
- Volumes
- ConfigMaps
- Secrets

Ports
-----

Which ports a container exposes can be listed in the :code:`spec.containers[].ports` array.

Example
^^^^^^^

.. code-block:: yaml

    ...  # Pod or Controller definition comes above this
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - name: http  # port name. May be referenced by services
          containerPort: 80 # Container port to expose
          protocol: "TCP" # May be TCP, UDP or SCTP. Defaults to TCP
          hostIP: "0.0.0.0" # OPTIONAL: What host IP to bind the external port to.


Environment
-----------

The :code:`spec.containers[].env` array contains environment variables that will be passed to the container.
For the value field, variable references $(VAR_NAME) are expanded using the previous defined environment variables
in the container and any service environment variables.
If a variable cannot be resolved, the reference in the input string will be unchanged.

Besides these environment variables, any existing services in the cluster will also cause service environment variables
to be inserted into newly created containers.


Example
^^^^^^^

.. code-block:: yaml

    ...  # Pod or Controller definition comes above this
    spec:
      containers:
        - name: worker
          image: "worker:latest"
          env:
            - name: REDIS_URL   # Example of combining service environment variables into another expected variable
              value: 'redis://$(REDIS_MASTER_SERVICE_HOST):$(REDIS_MASTER_SERVICE_PORT)'
            - name: CLIENT_ID  # Pull an env variable from a kubernetes secret
              valueFrom:
                secretKeyRef:
                  name: client-credentials
                  key: CLIENT_ID


Volumes
-------

If an application container stores state to disk, and this state needs to be accessible if the container is restarted,
it will need a `Volume <https://kubernetes.io/docs/concepts/storage/volumes/>`_ .
Volumes:

- Store state for the lifetime of the Pod
- Map to directories in the containers of the Pod according to the :code:`spec.containers.volumeMounts` field
- Cannot be mounted inside other volumes.
- Can share data between containers.
- Has many specific types of volume `see <https://kubernetes.io/docs/concepts/storage/volumes/#types-of-volumes>`_

Example
^^^^^^^

.. code-block:: yaml

    apiVersion: v1
    kind: Pod
    metadata:
      name: test-pd
    spec:
      containers:
      - image: k8s.gcr.io/test-webserver
        name: test-container
        volumeMounts:
        - mountPath: /cache
          name: cache-volume
      volumes:
      - name: cache-volume
        emptyDir: {}



