1. For Instaling KubeCtl-

curl.exe -LO "https://dl.k8s.io/release/v1.27.0/bin/windows/amd64/kubectl.exe"

2. For Installing MiniKube-

New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing

Then-

$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}

- minikube start

deployment.yaml
----------------
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: simple-app
  template:
    metadata:
      labels:
        app: simple-app
    spec:
      containers:
      - name: simple-app
        image: pitambarbhadra/bittu
        resources:
          limits:
            memory: "256Mi"
            cpu: "500m"

        ports:
        - containerPort: 8000
```
-----------------------------------------------------------------------------------------------------

service.yaml
--------------
```


apiVersion: v1

kind: Service

metadata:

  name: simple-app-service

spec:

  type: NodePort
  selector:
    app: simple-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8000
    nodePort: 31257
```
-------------------------------------------------------------------------------------------------

3. echo $oldPath
4. kubectl apply -f deployment.yaml
5. kubectl apply -f service.yaml

6. minikube service simple-app-service

-------------------------------------------------------------------------------------------------
## With INGRESS

ingress.yaml
------------

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  # namespace: default
  namespace: ingress-nginx

spec:
  ingressClassName: nginx
  rules:
  - host: pitambar.bhadra.co.in
    http: 
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: simple-app-service
              port:
                number: 80

```
--------------------------------------------------
deployment.yaml
---------------

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bhadra-deployment
  namespace: ingress-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: simple-app
  template:
    metadata:
      labels:
        app: simple-app
    spec:
      containers:
      - name: djangodeployment
        image: pitambarbhadra/bittu
        ports:
        - containerPort: 8000

```
-----------------------------------------------------
service.yaml
------------

```
apiVersion: v1

kind: Service

metadata:

  name: simple-app-service
  namespace: ingress-nginx

spec:

  type: NodePort
  selector:
    app: simple-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8000
    nodePort: 31258
    ```
--------------------------------------------------------
command
--------
# go to powershell
1. cd drivers
2. cd etc
3. notepad.exe hosts
-------
```
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
# Added by Docker Desktop
172.16.1.164 host.docker.internal
172.16.1.164 gateway.docker.internal
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
127.0.0.1 pitambar.bhadra.co.in
# End of section
```
---------------------------------------------------------------------------------------------
4. kubectl apply -f deployment.yaml
5. kubectl apply -f service.yaml
6. kubectl apply -f ingress.yaml

7. kubectl get ing
8. kubectl get ingress
9. kubectl get svc
10. kubectl describe ingress

11. kubectl get deployment -n default
12. kubectl get all
13. kubectl get pods
14. kubectl get ns

15. kubectl apply -f https://k8s.io/examples/service/networking/example-ingress.yaml
16. kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/cloud/deploy.yaml
17. kubectl config use-context docker-desktop

18. kubectl get ing -n ingress-nginx

19. now browse- pitambar.bhadra.co.in

## Kubernetes Ingress is an API object that provides routing rules to manage external users' access to the services in a Kubernetes cluster. It convert IP address to Domain name for the current machine only







