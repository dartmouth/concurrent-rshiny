# Concurrent R-Shiny

This repository provides examples on how to deploy R-Shiny applications in a Kubernetes environment with concurrency/sticky sessions in mind.  These examples were created to support a paper presented at [PEARC24](https://pearc.acm.org/pearc24/).

## Prerequisites

* Experience working with Containers and Kubernetes
* A Kubernetes Cluster
* [Traefik Ingress Controller](https://doc.traefik.io/traefik/getting-started/install-traefik/#use-the-helm-chart) installed

## R-Shiny Application

We built our R-Shiny container images based upon the [`rocker/shiny:4.3.1`](https://hub.docker.com/r/rocker/shiny) image.  Details about this image and other available images can be found here:

[https://rocker-project.org/images/versioned/shiny.html](https://rocker-project.org/images/versioned/shiny.html)

For the examples in this repository, we'll be using the unaltered `rocker/shiny:4.3.1` image to represent our R-Shiny application.

> NOTE: `rocker/shiny:4.3.1` is only available in the `x86_64` architecture.

## Installation

First, we apply the `rshiny-demo-app.yml` manifest to kubernetes which creates a namespace called `rshiny-demo-app`, a service called `rshiny-demo-app`, and a deployment called `rshiny-demo-app`.  This deployment will be configured with two replicas to show sticky sessions functioning as expected.

```shell
kubectl apply -f rshiny-demo-app.yml
```

If you want to deploy the application to the root of your domain (ex. http://example.com/), then you can use the `ingress-root.yml` manifest.  This will create an ingress resource that will route traffic from the root of your domain to the R-Shiny application.

```shell
kubectl apply -f ingress-root.yml
```

If you want to deploy the application to a subpath of your domain (ex. `http://example.com/rshiny-demo-app`), then you can use the `ingress-subpath.yml` manifest.  This will create an ingress resource that will route traffic from the `/rshiny-demo-app` subpath to the R-Shiny application.  

```shell
kubectl apply -f ingress-subpath.yml
```

When you access the domain, you should see a cookie called `RShinyDemoAppStickyCookie` created in your browser.  This cookie is used to enable sticky sessions to a specific pod.  If you delete this cookie, you'll see that the session is no longer sticky and you're routed to a different pod. You can determine which pod you are connected to by looking at the logs for each pod.