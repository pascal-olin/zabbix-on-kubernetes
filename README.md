## Introduction

(PO)
**
This project is forked from  bezarsnba/zabbix-on-kubernetes (https://github.com/bezarsnba/zabbix-on-kubernetes) . all credits to him for the hard work. 
Running his project 2 years later, I noticed a few issues that I have tried to fix using this fork. 
**
**
I am leaving the rest of this README as written by @bezarsnba , except when necessary within (PO) (/PO) Tags. 
**
(/PO)
- This repository files contains all files and information to provisioning Zabbix in Kubernetes
(PO)
included in repo is the option to build the necessary images from scratch and use a local (microK8s) registry to store them, then use them in our deployment. 
the build is achieved with Kaniko, to demonstrate we can do everything without Docker. 

(/PO)


# Pre requirements

~~- Kubernetes (Used version: v1.18.0)~~ 
- (PO) microK8s (used Version : installed:v1.22.5(2855) 193MB classic) (/PO)
- Kaniko (debug image)

# File structure

| File			| Content | Resources |
| ------------- | ------- | --------- |
| [cadvisor.yaml](./cadvisor.yaml) | Configuration to get and export monitoring metrics of [cAdvisor](https://prometheus.io/docs/guides/cadvisor/) ||
| [clusterRole-monitoring.yaml](./clusterRole-monitoring.yaml) | Prometheus roles ||
| [confimaps.yaml](./confimaps.yaml) | Non confidential variables with data used in many files | [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
| [namespace.yaml](./namespace.yaml) | Namespace configuration |[Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)|
| [zabbix-agent.yaml](zabbix-agent.yaml) | Configuration of Zabbix Agent | [Statefulsets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/), [PVC and PV](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) |
| [database-mysql.yaml](./database-mysql.yaml) |Database configuration | [Statefulsets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/), [PVC and PV](https://kubernetes.io/docs/concepts/storage/persistent-volumes/), [Service](https://kubernetes.io/docs/concepts/services-networking/service/), [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/) |
| [zabbix-server.yaml](./zabbix-server.yaml) | Zabbix server configuration | [Statefulsets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) and [Service](https://kubernetes.io/docs/concepts/services-networking/service/) |
| [zabbix-frontend.yaml](./zabbix-frontend.yaml) | Frontend configuration | [Statefulsets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) and [Service](https://kubernetes.io/docs/concepts/services-networking/service/) |


# Step by Step


1- Execute the apply to create namespace.

```
kubectl apply -f namespace.yaml
```

2- Execute the apply to create configmaps
```
kubectl apply -f configmaps.yaml
```

3 - Execute the apply to create secrtetes
```
kubectl apply -f secretes.yaml
```

4 - Execute the apply to create database
```
kubectl apply -f database-mysql.yaml 
```

5 - Execute the apply to create zabbix-agent
```
kubectl apply -f zabbix-agent.yaml
```

6- Execute the apply to create zabbix-server

```
 kubectl apply -f zabbix-server.yaml
```
7 - Execute the apply to create frontend

```
 kubectl apply -f zabbix-frontend.yaml 
```
*
(PO)
Adding an Ingress to the zabbix-frontend POD (zabbix-web-nginx-mysql-0)	
```
kubectl apply -f zabbix-frontend-ingress.yaml 
```
(/PO)
*


Execute the command to get informations about your enviromennt:

```
kubectl get deployment,svc,pods,pvc,ingress  -n monitoring

```


![Alt text](screenshot/kubernetes-zabbix.png?raw=true "Kubernetes-Zabbix")

## CADVISOR

The Cadvisor export the metrics of the Kubernetes if you preferer monitoring this environment with the Zabbix.

```
kubectl apply -f cadvisor.yaml
```

```
kubectl get deployment,svc,pods,pvc,ingress  -n cadvisor`
```
![Alt text](screenshot/cadvisor.png?raw=true "Cadvisor")


## Access

To  you access  Zabbix through the Minikube, execute this command:

```
$ minikube tunnel
Status:	
	machine: minikube
	pid: 4042
	route: 10.96.0.0/12 -> 172.17.0.2
	minikube: Running
	services: []
    errors: 

```

After that execute this command to get IP address of the Zabbix Frontend:

```
$ kubectl get svc  -n monitoring
...
zabbix-web-nginx-mysql   ClusterIP   10.103.89.223   <none>        8081/TCP,8443/TCP   18h

```

## Metrics

I created one host at the Zabbix to get metrics Cadvisor

![Alt text](screenshot/metrics-cadvisor-zabbix.png?raw=true "Cadvisor-Zabbix")


## Reference

https://www.zabbix.com/documentation/current/manual/config/items/itemtypes/prometheus

https://hub.docker.com/u/zabbix/

https://kubernetes.io/docs/concepts/


Thanks, @QuintilianoB for collaborating with the best practices Kubernetes  :)
