.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

External Endpoints
==================

- Kubernetes exposes endpoints to the outside world via `Ingress rules <https://kubernetes.io/docs/concepts/services-networking/ingress/>`_.
- These use an `Ingress Controller <https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/>`_ to
  route traffic from outside your kubernetes cluster to the services inside.
- A cluster may have multiple ingress controllers. Which one is used is determined by the annotations on the Ingress rule.

Ingress rules
-------------

Ingress rules define how an external endpoint (usually a DNS name) should be routed to a service by an ingress controller

- Can apply to all traffic on the ingress controller endpoint or to a specific host
- Allow path-based routing
- Can define TLS offloading for the endpoint
- Can allow for authentication on the endpoint

Example
^^^^^^^

This is an example from a production system using letsencrypt and using an external oAuth proxy to validate access
to the resource. It is using:

- `ingress-nginx controller <https://kubernetes.github.io/ingress-nginx/>`_
- `cert-manager <https://docs.cert-manager.io/en/latest/index.html>`_
- `oauth2_proxy <https://hub.docker.com/r/machinedata/oauth2_proxy/>`_
- An azure AD application resource for authorization (configured in the oauth2_proxy)

.. code-block::

    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      annotations:
        certmanager.k8s.io/acme-challenge-type: http01
        certmanager.k8s.io/issuer: letsencrypt-prod
        kubernetes.io/ingress.class: nginx
        kubernetes.io/tls-acme: "true"
        nginx.ingress.kubernetes.io/auth-signin: https://$host/oauth2/start?rd=$escaped_request_uri
        nginx.ingress.kubernetes.io/auth-url: https://$host/oauth2/auth
        nginx.ingress.kubernetes.io/backend-protocol: HTTP
      labels:
        app: grafana
        chart: grafana-3.8.11
        heritage: Tiller
        release: metrics
      name: metrics-grafana
      namespace: monitoring
    spec:
      rules:
      - host: monitoring.example.com
        http:
          paths:
          - backend:
              serviceName: metrics-grafana
              servicePort: 80
            path: /
      tls:
      - hosts:
        - monitoring.example.com
        secretName: grafana-general-tls



