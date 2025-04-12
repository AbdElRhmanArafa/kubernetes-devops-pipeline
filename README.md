# kubernetes-devops-pipeline

## Steps to Run the Pipeline

1. Use the [k8s-cluster-automation repository](https://github.com/AbdElRhmanArafa/k8s-cluster-automation) to set up an easy Kubernetes cluster using Vagrant and Ansible, or use your own cluster.

2. inside your cluster, clone this repository:

   ```bash
   kubectl apply -f <jenkins yaml file>
   ```
