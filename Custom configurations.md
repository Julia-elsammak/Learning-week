This setup below explores combining the features with some overrides:
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
   nodeAgent:
    extraConfd:
      configDataMap:
        http_check.yaml: |-
          init_config:
          instances:
            - url: "http://%%host%%"
              name: "My service"
```

For more examples please follow: https://github.com/DataDog/datadog-operator/tree/main/examples/datadogagent/v2alpha1
