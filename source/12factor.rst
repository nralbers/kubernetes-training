12 factor applications
======================

Applications that run in container orchestration platforms should meet certain requirements. One discussion of how
applications should be written and configured is the `12 factor application <https://12factor.net/>`_

I. **Codebase**: One codebase in version control, many deploys
II. **Dependencies**: Explicitly declare and isolate dependencies
III. **Config**: Store config in the environment
IV. **Backing services**: Treat backing services as attached resources
V. **Build, release, run**: Strictly separate build and run stages
VI. **Processes**: Execute the app as one or more stateless processes
VII. **Port binding**: Export services via port binding
VIII. **Concurrency**: Scale out via the process model
IX. **Disposability**: Maximize robustness with fast startup and graceful shutdown
X. **Dev/prod parity**: Keep development, staging, and production as similar as possible
XI. **Logs**: Treat logs as event streams
XII. **Admin processes**: Run admin/management tasks as one-off processes

Almost all of these points have direct relevance when deploying container images to Kubernetes. Kubernetes is easiest
to use and deploy to when your app explicitly addresses these points.

Docker images
-------------

II. Build your image with specific version tags.

V. Pipeline should build image, test image, push to docker repository, trigger deployment of new version to kubernetes.

VI. One process per image if possible. You can side-load secondary containers into Pods, but only do this if necessary.

VII. Expose your app via a known port on the image.

IX. Application MUST be running on PID 1 in the container.

X. By externalising all configuration, running in a container automatically supplies this.

Application design
------------------

III. Have your application read configuration from environment variables. If a config file is necessary, have this read
     from a fixed volume endpoint on the image so you can use a ConfigMap (:ref:`See Container Configuration <container_configuration>`)

IV. Read service connection parameters from environment. These can be mapped from the env variables that Kubernetes
    creates with each service resource.

VIII. Do not use or assume session affinity exists in your application. Session state must be explicitly managed and
      stored in a backing service.

IX. Application should shutdown gracefully on receiving a SIGTERM signal.

XI. Application MUST output logs to stdout only. Using some kind of structured log library, and/or exposing the logs
    as json will allow easy parsing and splitting of log data into categories.

XII. Write separate docker images or container commands for performing admin tasks. Run these using a
     `Job controller <https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/>`_

