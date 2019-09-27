# DevOps candidate Kubernetes task

Application can be deployed to Kubernetes Minikube in a convenient manner

## Getting Started

This repo contains two branches: 
* helmless
* helmful

First one (*helmless*) contains only deployment files. So you can deploy them on your kubernetes cluster manually with `kubectl` command or by running `minikube dashboard` and using Kubernetes GUI.

Second branch (*helmful*) is for Helm Chart (prefered way).

### Prerequisites

Kubectl, Minikube, Helm binaries in your $PATH.

Please note that there seems to be a bug with setting up tiller for k8s version 1.16. Because of that bug `helm init` command fails and returns error:
`Error: error installing: the server could not find the requested resource`. More info regarding this problem can be found here: https://github.com/helm/helm/issues/6374
So please use older Minikube versions.

Required application image is already built and hosted on *Docker Hub*.

### Installing

Rest of this Readme file is for *helmful* branch.

To start deploying application via Helm first you need to start Minikube by running:
```
minikube start
```

To enable the NGINX Ingress controller, run the following command: 
```
minikube addons enable ingress.
```

Next step is Helm, so run:
```
helm init
```

### Deployment

Now you can deploy this application by running:
```
helm install app/
```

Please note that application is dependent on Redis, so at the beginning "app" pod can fail if Redis pod is still not ready. So be patient for couple seconds.

Next is to make app accessible via browser. To do this first you can run:
```
curl $(minikube ip)/ -H "host: foo.bar.com"
```

If all goes well then in order to get required IP run:
```
minikube ip
```
Next add this line to your `/etc/hosts` file:
```
[IP you got from previous command] foo.bar.com
```

Now you should be able to access application in your browser via url: [foo.bar.com](http://foo.bar.com/)

### Usefull debugging stuff and issues I had

**This part is still in progress**

At the beginning I had issues with application pod failing because of incorrect or simply not set environment variables.
To debug this in application deployment yml under `spec: containers:` add couple lines for pod to sleep for some time before crashing:
```
command:
- sleep
- "3600"
```

Now you can log in to pod:
```
kubectl exec -it [your pod name] -- /bin/sh
```

And for example check environment variables by simply entering:
```
env
```

