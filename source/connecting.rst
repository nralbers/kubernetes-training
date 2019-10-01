.. image:: ./_static/logo.png
   :height: 100px
   :width: 100px
   :alt: Kubernetes Logo
   :align: right

Connecting
==========

- Managing resources in kubernetes is performed using the :code:`kubectl` tool.
- This is installed locally, and connects to remote clusters using information in the :code:`kubeconfig` file

Local Installation using minikube
---------------------------------

- You can only install minikube direct on a host OS. Installing minikube in a guest OS in a VM is not supported.
- Follow the instructions at kubernetes.io `to install kubectl <https://kubernetes.io/docs/tasks/tools/install-kubectl/>`_
  Kubectl is the kubernetes command-line tool which is used to run commands against a kubernetes cluster.
- Follow the instructions at kubernetes.io `to install minikube <https://kubernetes.io/docs/tasks/tools/install-minikube/>`_ ,
  a single-machine kubernetes installation that runs on a virtual machine on your own computer.
- `Instructions on starting and connecting to your minikube <https://kubernetes.io/docs/setup/learning-environment/minikube/#managing-your-cluster>`_
- To switch kubectl to the minikube context in your kubeconfig file use: :code:`kubectl config use-context minikube`


Kubernetes in the cloud
-----------------------

AWS
^^^

To connect to an existing AWS Kubernetes cluster, you will either need to use the AWS CLI, or install the AWS IAM
authenticator.

- Ensure you have an IAM user in the AWS subscription, and that you have an API key for that user.
- To connect using the AWS CLI.


  - `Install the AWS CLI <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html>`_
  - `Configure your kubeconfig file <https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html#eks-configure-kubectl>`_

- To connect using the AWS IAM authenticator:

  - `Install the AWS IAM authenticator <https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html>`_
  - Use the instructions in the section 'To use the AWS IAM Authenticator' in
    `this document <https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html>`_

Azure
^^^^^

To connect to an existing Azure managed kubernetes cluster, you will need to install the azure CLI

- Ensure you have a user account in Azure that can connect to your cluster.
- `Install the Azure CLI <https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest>`_
- Configure kubectl to connect to your cluster: :code:`az aks get-credentials --resource-group myResourceGroup --name myAKSCluster`
- When working with multiple clusters, you can switch to your AKS cluster using :code:`kubectl config use-context myAKSCluster`
