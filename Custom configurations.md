This setup below explores combining the features with some overrides. .
```
apiVersion: datadoghq.com/v2alpha1
kind: DatadogAgent
metadata:
  name: datadog
spec:
  global:
    credentials:
      apiSecret:
        secretName: datadog-secret
        keyName: api-key
    kubelet:
      tlsVerify: false
  
  # Enable various features
  features:
    apm:
      enabled: true
      hostPortConfig:
        enabled: true
    logCollection:
      enabled: true
      containerCollectAll: true
    clusterChecks:
      enabled: true
      useClusterChecksRunners: true
  
  override:
    # Add overrides to the (Node) Agent 
    nodeAgent:
      # Create a ConfigMap and mount in this "tcp_check.yaml" file
      extraConfd:
        configDataMap:
          tcp_check.yaml: |-
            init_config:
            instances:
              - name: trace-agent
                host: localhost
                port: 8126
      # Configurations specific to the containers "agent" and "trace-agent"
      containers: 
        agent: 
          resources:
            limits:
              cpu: 500m
              memory: 512Mi
        trace-agent:
          logLevel: debug

    # Add overrides to the Cluster Agent
    clusterAgent:
      replicas: 2
      # Create a ConfigMap and mount in this "http_check.yaml" file for a Cluster Check
      extraConfd:
        configDataMap:
          http_check.yaml: |-
            cluster_check: true
            init_config:
            instances:
              - name: example-cluster-check
                url: https://example.com

    # Add overrides to the Cluster Check Runner
    clusterChecksRunner:
      containers:
        agent:
          env:
            - name: HELLO
              value: WORLD
```

For more examples please follow: https://github.com/DataDog/datadog-operator/tree/main/examples/datadogagent/v2alpha1