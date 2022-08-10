# Health Checks

To check if a container is running and also serving request properly, there is two probe mode in Pod manifest:

|Probe|Description|On Failed|
|---|-  |-  |
|Liveness|Check if container is running properly|Restart the container|
|Readinees|Check if container is ready to server requests|Remove the container from service load-balancers|

```yaml
apiVersion: v1
kind: Pod
...
spec:
  ...
  livenessProbe:
    httpGet:
      path: /healthy
      port: 8080
      initialDelaySeconds: 5
      timeoutSeconds: 1
      periodSeconds: 20
      failureThreshold: 3
  ...
```
