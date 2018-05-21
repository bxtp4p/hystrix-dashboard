# About

This project is for deploying the hystrix dashboard to minikube for local developer testing and to Docker UCP.

## Setup

Clone this repository and navigate to its directory.

```
$ git clone git@github.com:bxtp4p/hystrix-dashboard.git
$ cd hystrix-dashboard
```

## Deployment

### Minikube

To deploy to Minikube:

```
$ kubectl apply -f minikube/deployment.yaml
```

This will create:

* `hystrix-dashboard` Namespace
* `hystrix-dashboard-sa` ServiceAccount
* `hystrix-sa-clusterrole-view` ClusterRoleBinding for the service account to `view` role across the cluster
* `hystrix-dashboard` Deployment
* `hystrix-dashboard-svc` Service/NodePort

You can then bring up the interface with:

```
$ minikube service -n hystrix-dashboard hystrix-dashboard-svc
```

### Docker UCP

To deploy to Docker UCP:

```
$ kubectl apply -f ucp/deployment.yaml
```

Since Docker UCP doesn't currently support Kubernetes RBAC, to ensure the `hystrix-dashboard-sa` ServiceAccount has proper access, do the following steps in UCP:

1. Navigate to `User Management` > `Grants`
2. Click on the `Create Grant` button
    * Under `Resource Sets`
      * Select Type: `Namespaces`
      * Click on the `Apply grant to all existing and new namespaces` switch
    * Under `Roles`
      * Select `View Only` role
    * Under `Subjects`
      * Select `Service Account` Subject Type
      * Select `hystrix-dashboard` Namespace
      * Select `hystrix-dashboard` Service Account
3. Click the `Create` button on the bottom right of the page


To access the interface, check the url and node port that was assigned to the `hystrix-dashboard-svc` in UCP. Alternatively, if you've deployed the [Nginx Ingress Controller](https://docs.docker.com/ee/ucp/kubernetes/layer-7-routing/), you can modify the `ucp/ingress.yaml` file to add your desired hostname (e.g., `hystrix-dashboard.apps.example.com`) and apply it to your cluster:

```
$ kubectl apply -f ucp/ingress.yaml
```

## Testing

On the front page, just enter the url to any Hystrix or Turbine stream and click the Monitor Stream url.

For testing purposes, feel free to use the following [Hystrix Example App](https://github.com/bxtp4p/hystrix-example).
