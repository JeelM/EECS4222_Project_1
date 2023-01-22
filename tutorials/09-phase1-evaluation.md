# Submission and Evaluation 

By the end of this tutorial you should have a kubernetes cluster up and
running, our microservice application deployed, a functional monitoring stack,
kubernetes HPA enabled, and have performed experiments on the cluster.

We use your deployment to verify the correct installation and execution of the following:

## Kubernetes (25%)

Your kubernetes cluster should be up and running, your nodes should all be in the
state `Ready`, all pods should be in the state `Running`, and your installation should
generally be healthy.

## Application Deployment (25%)

All pods that make up different microservices for the application should be successfully
deployed, in the `Running` state, and we should be able to navigate the application
running on your cluster using the IP of your `master` node.

## Monitoring Stack (25%)

Your monitoring stack should be up and running, all pods should be in a `Healthy`
status and all pods should be `Running`. We should be able to open your prometheus
installation by opening port `9090` on your `master` node and be able to query
the monitored metrics like CPU or memory utilizations.

## Experiment Report (25%)

You need to write a report discussing the results you got from running the provided
Jupyter notebook on your cluster. Extract the generated plots from the Jupyter notebook
and explain the process discussing how and why the autoscaling events have occurred.
