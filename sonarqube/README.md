# Install Sonarqube 

## Steps

1. Create a new namespace in your cluster
```bash
kubectl create namespace sonarqube
```

2. create a label on node
```bash
kubectl label nodes <node-name> sonarqube=true
```
![Sonarqube Node Labeled](https://raw.githubusercontent.com/AbdElRhmanArafa/kubernetes-devops-pipeline/main/images/sonarqube_labeled.png)

3. run the following command to deploy Sonarqube
```bash
helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
helm repo update
helm upgrade --install -n sonarqube --version '~8' sonarqube sonarqube/sonarqube -f values.yaml
```
