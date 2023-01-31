# monitoring-with-prometheus-and-grafana
A demo of how I provisioned kubernetes cluster with eksctl, manage microservices with kubernetes, helm chart and monitoring with prometheus and grafana.

**Contents** <a name="Contents"></a>
* [Tools](#Tools)
* [Demo](#Demo)
  * [Provision of Infrastruture](#Provision-of-Infrastruture)
  * [Deployment of Microservices](#Deployment-of-Microservices)
  * [Monitoring the System](#Monitoring-the-System)
  * [Monitoring the application](#Monitor-the-application)
* [Resources](#Resources)

[Back to Contents](#Contents)

## Tools
- [aws](https://aws.amazon.com/) - cloud platform, offers reliable, scalable, and inexpensive cloud computing services.
- [aws cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)  - a unified tool to manage your AWS services.
- [VSCode](https://code.visualstudio.com/) - a source-code editor made by Microsoft with the Electron Framework, for Windows, Linux and macOS.
- [helm](https://helm.sh/) - Helm helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.
- [eksctl](https://eksctl.io/) - a simple CLI tool for creating and managing clusters on EKS - Amazon's managed Kubernetes service for EC2.
- [prometheus](https://prometheus.io/docs/introduction/overview/) - An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
- [grafana](https://grafana.com/docs/grafana/latest/) - Operational dashboards for your data

## Demo
The architecture flow this pattern;
Git --> GitHub --> eskctl --> helmfile --> kubernetes cluster --> prometheus --> Grafana

### Provision of Infrastruture
- Step 1: Programmatic access; 
![aws-configure](docs/aws-configure.png)
- Step 2: Install and configure kubectl, AWS CLI and eksctl.
[Link to How](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html). Be careful to set and utilise profile name when working with multiple accounts in your machine.
- Step 3: Creating cluster with (1.) a name (2.) version 1.22 (3.) nodegroup name, type and number in a specify region. Run the command; 
```
eksctl create cluster --name database --version 1.22 --nodegroup-name clusternode --node-type t3.micro --nodes 1 --managed
```
![aws-cluster](docs/aws-cluster-ready.png)
- Step 4; Check Cloudformation and eks from aws management console
![aws-cluster](docs/aws-cluster.png)
With this command, we have successfuly create a eks cluster with a node.

#### Configure kubectl to communicate with cluster
- Step 5; Configure your computer to communicate with your cluster;
```
aws eks update-kubeconfig --region us-east-1 --name database
```
- Step 6; Confirm your context and test your configuration. Run this command to list your contexts;
```
kubectl config get-contexts
```
- or the this command to get the current context;
```
kubectl config current-context
```
- or the this command to switch context;
```
kubectl config use-context <name>
```
- then the following commands to check your configuration;
```
kubectl get nodes
```
```
kubectl get svc
```
![aws-get](docs/aws-get.png)

[Back to Contents](#Contents)

### Deployment of Microservices

#### Deploy own Microservice application
For simplicity I will use and deploy MySQL instance using persistent volume with kubernetes declaratively;
- create YAML files for the application: secret, deployment, persistent volume and Volume Claim;
Locate all the appropriate YAML files inside docs directory.
- apply all the YAML files for the service; 
```
kubectl apply -f ....yaml
```
- confirm if pods were deployed;
```
kubectl get pods
```
![aws-get](docs/aws-get.png)

#### Deploy the Prometheus Stack
- There are basically 3 options to deploy prometheus different parts in the kubernetes cluster; 
1.) Creating and executing all configuration YAML file in the right order
2.) Using operator
3.) Using helm chart to deploy operator
- I use helm chart to deploy a prometheus operator that manages all the prometheus components. This is maintained by the Prometheus community.
Helm initializes setup and operator manages the running setup. For update, check the prometheus documentation and repository.
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
```
helm repo update
```
- Install Prometheus into own namespace - named monitoring;
```
kubectl create namespace monitoring
```
```
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
```

- Check its status;
```
kubectl --namespace monitoring get pods -1 "release=monitoring"
```
- Or printout everything that was deployed in the monitoring namespace;
```
kubectl get all -n monitoring
```
- Note that prometheus components are interconnected in a way, so care must be given while querying or troubleshooting a part.
- Also, it is important to know and understand;
1.) How to add/adjust alert rules
2.) How to adjust prometheus configuration

[Back to Contents](#Contents)

## Resources
Google
Tools documentation

[Back to Contents](#Contents)