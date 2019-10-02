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


ConfigMaps
----------

`ConfigMaps <https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/>`_ are a method of
storing application configuration information.

- ConfigMaps can be mounted as Volumes
- ConfigMaps can be exposed as environment variables
- ConfigMap volume mounts are updated if the ConfigMap is updated
- ConfigMaps are similar to secrets, but are not designed for storing sensitive data.

Usage
^^^^^

- `Using a configmap to create environment variables
  <https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#define-a-container-environment-variable-with-data-from-a-single-configmap>`_
- `Populate a volume with ConfigMap data
  <https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#populate-a-volume-with-data-stored-in-a-configmap>`_

Example
^^^^^^^

*configmap-multikeys.yaml*

.. code-block:: yaml

    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: special-config
      namespace: default
    data:
      SPECIAL_LEVEL: very
      SPECIAL_TYPE: charm

Create the ConfigMap:

.. code-block:: bash

    kubectl create -f configmap-multikeys.yaml

Create a Pod that uses the ConfigMap

*pod-configmap-volume.yaml*

.. code-block:: yaml

    apiVersion: v1
    kind: Pod
    metadata:
      name: dapi-test-pod
    spec:
      containers:
        - name: test-container
          image: k8s.gcr.io/busybox
          command: [ "/bin/sh", "-c", "ls /etc/config/" ]
          volumeMounts:
          - name: config-volume
            mountPath: /etc/config
      volumes:
        - name: config-volume
          configMap:
            # Provide the name of the ConfigMap containing the files you want
            # to add to the container
            name: special-config
      restartPolicy: Never

.. code-block:: bash

  kubectl apply -f pod-configmap-volume.yaml

Output

.. code-block:: bash

    SPECIAL_LEVEL
    SPECIAL_TYPE

Secrets
-------

`Secrets <https://kubernetes.io/docs/concepts/configuration/secret/>`_ are a method for passing sensitive information
to Pods.

- Secrets can be mounted as data volumes
- Secrets can be exposed as environment variables
- Secret volume mounts are updated automatically if the secret is changed
- ImagePullSecrets are used to pass image repository credentials to Pods

Example
^^^^^^^

Creating a secret from a file:

.. code-block:: bash

    # Create files needed for rest of example.
    echo -n 'admin' > ./username
    echo -n '1f2d1e2e67df' > ./password

    kubectl create secret generic mysecret --from-file=./username --from-file=./password

Using a secret in an environment variable:

.. code-block:: yaml

    apiVersion: v1
    kind: Pod
    metadata:
      name: secret-env-pod
    spec:
      containers:
      - name: mycontainer
        image: redis
        env:
          - name: SECRET_USERNAME
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: username
          - name: SECRET_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: password
      restartPolicy: Never



