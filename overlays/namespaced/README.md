This is a convenient kustomization that adds the specified namespace to all objects.

To use it, execute the following command from the root directory of this repository:

```shell script
./overlay-generate-cluster.sh namespaced generated-cluster
```

You need to create the namespace if it doesn't exist yet (replace `ns-sourcegraph` with your namespace):

```shell script
kubectl create namespace ns-sourcegraph
```

After executing the script you can apply the generated manifests from the `generated-cluster` directory:

```shell script
kubectl apply --prune -l deploy=sourcegraph -f generated-cluster --recursive
```
