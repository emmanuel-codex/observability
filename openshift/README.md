The NVIDIA GPU Operator requires access to insecure nvcr.io registry. You need to edit the image.config.openshift.io/cluster as described [here](https://docs.openshift.com/container-platform/4.16/openshift_images/image-configuration.html#images-configuration-insecure_image-configuration)

The spec contents should be:


```
spec:
  registrySources:
    allowedRegistries:
    - quay.io
    - registry.redhat.io
    - nvcr.io
    - image-registry.openshift-image-registry.svc:5000
    insecureRegistries:
    - nvcr.io
```
The above change will bring the samples operator to a Degraded state:

```
$ oc get co openshift-samples
NAME                VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
openshift-samples   4.16.32   True        True          True       3d10h   Samples installation in error at 4.16.32: &errors.errorString{s:"global openshift image configuration prevents the creation of imagestreams using the registry "}
```

You will need to do an extra step to resolve this: 

```
oc delete configs.samples cluster
```

Then check the managementState using the following command:

```
oc edit configs.samples.operator.openshift.io -n openshift-cluster-samples-operator
```
managementState: Removed

Check the link [here](https://docs.openshift.com/container-platform/4.14/openshift_images/configuring-samples-operator.html#samples-operator-bootstrapped) for more details on Removed management State.



## Samples Operator Troubleshooting 
### Retrieve the operator pod logs

If you need to do further troubleshooting with the 


```
$ oc project openshift-cluster-samples-operator
$ export PODS=`oc get pods -o=jsonpath="{.items[*].metadata.name}"`
$ for pod in $PODS;do echo Saving logs from pod $pod to $pod.log;oc logs $pod &> $pod.log;done;
```


```	
oc edit image.config.openshift.io/cluster
oc get co openshift-samples
oc delete configs.samples cluster
oc get pods -n openshift-cluster-samples-operator
oc edit configs.samples.operator.openshift.io -n openshift-cluster-samples-operator
```


