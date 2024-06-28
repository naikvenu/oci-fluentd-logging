Disclaimer:
I work for Oracle and the views expressed on this documentation are my own and do not necessarily reflect the views of Oracle. 
These instructions provided are for experimental purposes only and should not be used in production. You should always run any new procedure and scripts on a test environment and validate and modify the same as per your requirements before using on your application environment.


# Deploying Fluentd as a Dameonset with OCI (Oracle Cloud Infrastructure) logging as backend for log management.

# Build your docker image and push it to your tenancy OCIR registry

$ cd docker-image

Get your Namespace (Run this from cloud shell if OCI CLI is not installed on your workstation):

$ oci os ns get 

Follow these instructions if required: 
https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryprerequisites.htm

```
$ docker login <region>.ocir.io
$ docker build -f Dockerfile -t <region>.ocir.io/<namespace>/<repo>/custom-logging-fluentd:latest
$ docker push <region>.ocir.io/<namespace>/<repo>/custom-logging-fluentd:latest
```

Open fluentd-daemonset.yaml and replace the image with your image 
```
<region>.ocir.io/<namespace>/<repo>/custom-logging-fluentd:latest
```

# Deploy fluentd daemonset on kubernetes cluster:

First add your config as secrets as shown below, please refer OCI docs on creating the API keys
```
$ kubectl create secret generic oci-creds --from-file=<path>/.oci/config
$ kubectl create secret generic oci-keys --from-file=<path>/oci_api_key.pem
```
This is required to pull image from OCI container Registry:
```
$ kubectl create secret docker-registry ocirsecret --docker-server=<region>.ocir.io --docker-username=<namespace>/oracleidentitycloudservice/<username> --docker-password=<auth-token>
```
# The secrets would look like this

```
$ kubectl get secrets

NAME         TYPE                             DATA   AGE
oci-creds    Opaque                           1      3h31m
oci-keys     Opaque                           1      10s
ocirsecret   kubernetes.io/dockerconfigjson   1      42
```

# Create Config map:

Before applying this, you first need to create a log group ID from the OCI console -> Observability and Management -> OCI logging -> Log group and Log
Edit the OCID of the OCI log into the config file before applying.

```
$ kubectl apply -f config.yaml

$ kubectl get configmap
NAME                              DATA   AGE
fluentd-config                    1      3h33m

$ kubectl apply -f security.yaml

# Edit the OCIR image name before creating fluentd dameonset

$ kubectl apply -f fluentd-daemonset.yaml
```

# Log on to OCI console and verify the logs are coming through.
