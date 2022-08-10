# Volumes

|volume|Description|
|---|-  |
|spec.volumes|All volumes that may be accessed by containers.|
|volumeMounts|Volumes that are mounted to a specific container and path where each volumes.

```yaml
apiVersion: v1
kind: Pod
...
spec:
  ...
  volumes:
    - name: "mount1"
      hostPath:
        path: ""
  container:
    - image:
      ...
    volumeMounts:
      - mountPath: ""
        name: "mount1"
```

## Persistent Volume

- Removing an in use PVC by a Pod will be postponed until the PVC not in use anymore. Same for PV, if an admin deletes a PV which is bounded to a PVC, the PV deletion will be postponed until the PV is not bounded to a PVC.

## Dynamic Volume Provisioning

In *Dynamic Volume Provisioning*, the cluster operator creates one or more StorageClass objects.

In Automatic provisioning the persistent volumes, the lifespan of PVs are based on reclamation policy of PVC and default is to **bind that lifespan to lifespan of the Pod which creates the volume**.
> To enable DVP:
>
> - Making a `StorageClass` object as *default* by adding `storageclass.kubernetes.io/is-default-class` annotation to it;
> - `DefaultStorageClass` plugin needs to be added to **admission controller** on the API server.
