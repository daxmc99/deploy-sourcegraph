# Installing Sourcegraph

> **Note:** Sourcegraph sends performance and usage data to Sourcegraph to help us make our product
> better for you. The data sent does NOT include any source code or file data (including URLs that
> might implicitly contain this information). You can view traces and disable telemetry in the site
> admin area on the server.

## Requirements

- [Kubernetes](https://kubernetes.io/) v1.9 or later
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) v1.9.7 or later
- Access to server infrastructure on which you can create a Kubernetes cluster (see
  [resource allocation guidelines](scale.md)).
- [Sourcegraph Enterprise license](./configure.md#add-license-key). You can run through these instructions without one, but you must obtain a license for instances of more than 10 users.


## Steps

1. [Provision a Kubernetes cluster](k8s.md) on the infrastructure of your choice.
1. Make sure you have configured `kubectl` to [access your cluster](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/).

   - If you are using GCP, you'll need to give your user the ability to create roles in Kubernetes [(see GCP's documentation)](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control#prerequisites_for_using_role-based_access_control):

     ```bash
     kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user $(gcloud config get-value account)
     ```

1. Clone this repository and check out the version tag you wish to deploy.

   ```bash
   # Go to https://github.com/sourcegraph/deploy-sourcegraph/tags and select the latest version tag
   git clone https://github.com/sourcegraph/deploy-sourcegraph && cd deploy-sourcegraph && git checkout ${VERSION}
   ```

1. Configure the `sourcegraph` storage class for the cluster by reading through ["Configure a storage class"](./configure.md#configure-a-storage-class).

1. If you want to add a large number of repositories to your instance, you should [configure the number of gitserver replicas](configure.md#configure-gitserver-replica-count) and [the number of indexed-search replicas](configure.md#configure-indexed-search-replica-count) _before_ you continue with the next step. (See ["Tuning replica counts for horizontal scalability"](scale.md#tuning-replica-counts-for-horizontal-scalability) for guidelines.)

1. Deploy the desired version of Sourcegraph to your cluster:

   ```bash
   ./kubectl-apply-all.sh
   ```

1. Monitor the status of the deployment.

   ```bash
   watch kubectl get pods -o wide
   ```

1. Once the deployment completes, verify Sourcegraph is running by temporarily making the frontend port accessible:

   kubectl 1.9.x:

   ```bash
   kubectl port-forward $(kubectl get pod -l app=sourcegraph-frontend -o template --template="{{(index .items 0).metadata.name}}") 3080
   ```

   kubectl 1.10.0 or later:

   ```
   kubectl port-forward svc/sourcegraph-frontend 3080:30080
   ```

   Open http://localhost:3080 in your browser and you will see a setup page. Congrats, you have Sourcegraph up and running!

1. Now [configure your deployment](configure.md).

### Troubleshooting

See the [Troubleshooting docs](troubleshoot.md).
