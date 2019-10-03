Useful kubectl commands
=======================

The following commands are useful for checking the status of your cluster or of the applications running in it

:code:`kubectl get <resource> [instance] [-o json]` : Get a list or single instance of a kubernetes resource.

Resources:

- deployment
- service
- configmap
- secret
- statefulset
- pod
- <any other kubernetes resource>

:code:`kubectl describe <resource> <instance>` : Shows details of the resource instance status. Useful for troubleshooting

:code:`kubectl logs <pod|controller> <instance> <container> [--all-containers=true] [-f]` : Print the logs of the specified
pod or resource. The :code:`-f` flag can be used to follow logs.

:code:`kubectl exec <podname> [-c containername] -- <command>` : Execute the command in a container. To interactively run
the command, add the :code:`-it` flag. This works more or less identically to the :code:`docker exec` command.

:code:`kubectl delete <resource> <instance>` : Delete the specified resource instance.

:code:`kubectl port-forward <podname> localport:podport` : Start a local listener that forwards all traffic on
:code:`localhost:localport` to the :code:`podport` in the pod on the cluster. Useful for testing applications where there
is no external network access.

For more useful commands, see the `kubectl Cheat Sheet <https://kubernetes.io/docs/reference/kubectl/cheatsheet/>`_
