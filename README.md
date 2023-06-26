# Shipwrigth

### First you need to install the Tekton dependency
```bash
$ kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/previous/v0.44.0/release.yaml
```

### Then you install Shipwright
```bash
$ kubectl apply --filename https://github.com/shipwright-io/build/releases/download/v0.11.0/release.yaml
```

### Install Shipwright build strategies
```bash
$ kubectl apply --filename https://github.com/shipwright-io/build/releases/download/v0.11.0/sample-strategies.yaml
```

### Check all available ClusterBuildStrategy (CBS) objects
```bash
$ kubectl get cbs
```

### Create a new Kubernetes Secret using some environment variables

```bash
$ REGISTRY_SERVER=https://index.docker.io/v1/
$ REGISTRY_USER=<your_registry_user>\
$ REGISTRY_PASSWORD=<your_registry_password>
$ REGISTRY_ORG=<your_registry_org>
$ EMAIL=<your_email>
$ kubectl create secret docker-registry push-secret --docker-server=$REGISTRY_SERVER --docker-username=$REGISTRY_USER --docker-password=$REGISTRY_PASSWORD --docker-email=$EMAIL
```

### Using Kaniko

```bash
$ kubectl create -f build-kaniko.yaml

$ kubectl get builds

$ kubectl create -f buildrun-kaniko.yaml

$ kubectl logs -f <kaniko-nodejs-buildrun-pod-name> -c step-build-and-push
```

### Using Buildpack

```bash
$ cat <<EOF | kubectl apply -f -
apiVersion: shipwright.io/v1alpha1
kind: Build
metadata:
  name: buildpack-nodejs-build
spec:
  source:
    url: https://github.com/shipwright-io/sample-nodejs
    contextDir: source-build
  strategy:
    name: buildpacks-v3
    kind: ClusterBuildStrategy
  output:
    image: docker.io/${REGISTRY_ORG}/sample-nodejs:latest
    credentials:
      name: push-secret
EOF

$ kubectl get builds

$ cat <<EOF | kubectl create -f -
apiVersion: shipwright.io/v1alpha1
kind: BuildRun
metadata:
  generateName: buildpack-nodejs-buildrun-
spec:
  buildRef:
    name: buildpack-nodejs-build
EOF
```



## Documentation:
https://github.com/shipwright-io/























