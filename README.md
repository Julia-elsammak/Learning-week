# Learning-week
https://docs.google.com/presentation/d/153XcxQ9k7_41NRiUPAdQFrnnwbAke7tcVrrSsJYb2aY/edit#slide=id.g2e6f4f09bba_0_35
Datadog Operator 

### Installation
This page provides instructions on installing the Datadog Agent in a Kubernetes environment through Datadog Operator

### Minimum Agent and Cluster Agent versions
Some features related to later Kubernetes versions require a minimum Datadog Agent version.

- KUBERNETES VERSION 1.16.0+ | 1.22.0+	
- AGENT VERSION	7.19.0+ | 7.37.0+	
- CLUSTER AGENT VERSION	1.9.0+ | 1.37.0+
- REASONS Kubelet metrics deprecation | Kubernetes resource deprecation | Support dynamic service account token

The Datadog Operator is a way to deploy the Datadog Agent on Kubernetes. It reports deployment status, health, and errors in its Custom Resource status, and it limits the risk of misconfiguration thanks to higher-level configuration options.

### Using the Datadog Operator requires the following prerequisites:
Kubernetes Cluster version >= v1.20.X: Tests were done on versions >= 1.20.0. Still, it should work on versions >= v1.11.0. For earlier versions, because of limited CRD support, the Operator may not work as expected.
Helm https://helm.sh/ for deploying the datadog-operator.
Kubectl CLI: https://kubernetes.io/docs/tasks/tools/#kubectl for installing the datadog-agent.

### Deploy an Agent with the Operator
To deploy the Datadog Agent with the operator in the minimum number of steps, see the datadog-operator Helm chart: https://github.com/DataDog/helm-charts/blob/main/charts/datadog-operator/values.yaml

### Install the Datadog Operator: Here are the steps: https://github.com/DataDog/datadog-operator/blob/main/docs/configuration.v2alpha1.md
To install the Datadog Operator in your current namespace, run:

```helm repo add datadog https://helm.datadoghq.com
helm install datadog-operator datadog/datadog-operator
kubectl create secret generic datadog-secret --from-literal api-key=<DATADOG_API_KEY>
```
Replace <DATADOG_API_KEY> and <DATADOG_APP_KEY> with your Datadog API and application keys

### Configure datadog-agent.yaml. Create a file with the spec of your Datadog Agent deployment configuration. The simplest configuration is as follows:

``` 
apiVersion: datadoghq.com/v2alpha1
kind: DatadogAgent
metadata:
  name: datadog
spec:
  global:
    clusterName: <CLUSTER_NAME>
    site: <DATADOG_SITE>
    credentials:
      apiSecret:
        secretName: datadog-secret
        keyName: api-key
```
**Notes:** 
Replace <CLUSTER_NAME> with a name for your cluster.
Replace <DATADOG_SITE> with your Datadog site. Your site is datadoghq.com. (Ensure the correct SITE is selected on the right).
Existing secret `kubectl get secrets`

**Deploy the Datadog Agent with the above configuration file:**

`kubectl apply -f /path/to/your/datadog-agent.yaml` or `kubectl apply -f datadog-agent.yaml`

**Confirm Agent installation**
Verify that Agent pods (tagged with app.kubernetes.io/component:agent) appear on the Containers page in Datadog. Agent pods are detected within a few minutes of deployment.

### Cleanup
The following command deletes all the Kubernetes resources created by the above instructions:
`kubectl delete datadogagent datadog`
`helm delete datadog-operator`

For further details on setting up Operator, including information about using tolerations, refer to the Datadog Operator advanced setup guide:https://docs.datadoghq.com/agent/guide/operator-advanced/

**Troubleshooting**
`kubectl get pods`
`kubectl describe pods my pod`
`kubectl exec -it <agent-pod-name> agent status`
