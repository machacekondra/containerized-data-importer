# How to clone an image from one DV to another one
The purpose of this document is to show how to clone an image from an existing DV/PVC, to another DV.

## Prerequisites
- You have a Kubernetes cluster up and running with CDI installed, source DV/PVC, and at least one available PersistentVolume to store the cloned disk image.
- The target PV is equal or larger in size than the source DV/PVC.
- When cloning across namespaces, the user must have the ability to create pods or have 'datavolumes/source' permission in the source namespace. You can give a user the appropriate permissions to a namespace by specifying [RBAC](RBAC.md) rules.

## Clone an image with DataVolume manifest

Create the following DataVolume manifest [clone-datavolume.yaml](../manifests/example/clone-datavolume.yaml):

```yaml
apiVersion: cdi.kubevirt.io/v1alpha1
kind: DataVolume
metadata:
  name: cloned-datavolume
spec:
  source:
    pvc:
      namespace: source-ns
      name: source-datavolume
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 500Mi
```

Deploy the DataVolume manifest:

```bash
kubectl create -f clone-datavolume.yaml
```

Two cloning pods, source and target, will be spawned and the image existed on the source DV/PVC, will be copied to the target DV.
