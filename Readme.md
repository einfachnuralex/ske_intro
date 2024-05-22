# SKE Demo

## Install CLIs

Link: https://github.com/stackitcloud/stackit-cli/blob/main/INSTALLATION.md

To install CLI via Brew

```bash
brew install stackitcloud/tap/stackit
```

Install k9s as it's the best TUI for K8s

```bash
brew install derailed/k9s/k9s
```

## Set Project ID

Set project id for STACKIT CLI

```bash
stackit config set --project-id <project_id>
```

## Activate SKE

Activate SKE for that prohect

```bash
stackit ske enable
```

## Create SKE Cluster

Create SKE cluster with default settings

```bash
stackit ske cluster create wdcdemo
```

For more customizations just add a json to the request

```json
{
    "kubernetes": {
        "version": "1.28.9"
    },
    "nodepools": [
        {
            "availabilityZones": [
                "eu01-1"
            ],
            "machine": {
                "image": {
                    "name": "flatcar",
                    "version": "3815.2.1"
                },
                "type": "c1.3"
            },
            "maximum": 3,
            "minimum": 2,
            "name": "pool",
            "volume": {
                "size": 20,
                "type": "storage_premium_perf2"
            }
        }
    ]
}
```

Then run this command 

```bash
stackit ske cluster create wdcdemo2 --payload @./work/api.json 
```

## Get Kubeconfig

Get kubeconfig for named cluster. This command will overwrite `~/.kube/config`

```bash
stackit ske kubeconfig create wdcdemo
```

Now you can access the cluster

```bash
kubectl get nodes
```

Output

```bash
NAME                                                   STATUS   ROLES    AGE   VERSION
shoot--a588a96419--test1-pool-default-z1-76956-q5ktk   Ready    <none>   12m   v1.28.9
shoot--a588a96419--test1-pool-default-z1-76956-w9kng   Ready    <none>   10m   v1.28.9
```

## Create Kubernetes Manifests

Kubernetes Documentation is a good source for YAML manifests. 

Link: https://kubernetes.io/docs/concepts/workloads/controllers/

Also reference for all fields in manifests are there

Link: https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/

Kubectl can also help in describing fields as you need it

```bash
kubectl explain deployment.spec.template.spec.containers
```

Output

```bash
KIND:     Deployment
VERSION:  apps/v1

RESOURCE: containers <[]Object>

DESCRIPTION:
     List of containers belonging to the pod. Containers cannot currently be
     added or removed. There must be at least one container in a Pod. Cannot be
     updated.

     A single application container that you want to run within a pod.

FIELDS:
   args	<[]string>
     Arguments to the entrypoint. The container image's CMD is used if this is
     not provided. Variable references $(VAR_NAME) are expanded using the
     container's environment. If a variable cannot be resolved, the reference in
     the input string will be unchanged. Double $$ are reduced to a single $,
     which allows for escaping the $(VAR_NAME) syntax: i.e. "$$(VAR_NAME)" will
     produce the string literal "$(VAR_NAME)". Escaped references will never be
     expanded, regardless of whether the variable exists or not. Cannot be
     updated. More info:
     https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#running-a-command-in-a-shell

   command	<[]string>
     Entrypoint array. Not executed within a shell. The container image's
     ENTRYPOINT is used if this is not provided. Variable references $(VAR_NAME)
     are expanded using the container's environment. If a variable cannot be
     resolved, the reference in the input string will be unchanged. Double $$
     are reduced to a single $, which allows for escaping the $(VAR_NAME)
     syntax: i.e. "$$(VAR_NAME)" will produce the string literal "$(VAR_NAME)".
     Escaped references will never be expanded, regardless of whether the
     variable exists or not. Cannot be updated. More info:
     https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#running-a-command-in-a-shell

   env	<[]Object>
     List of environment variables to set in the container. Cannot be updated.
...
```

Use `kubectl` to create resources, e.g. Deployments or Services

```bash
# Create Deployment for nginx container
kubectl create deployment nginx-deployment --image=nginx --replicas=3
```

Now we want to expose this Deployment as Service of type LoadBalancer

```bash
kubectl expose deployment nginx-deployment --port=80 --type=LoadBalancer
```

## Install Ingress Stack

```bash
kubectl kustomize ./stack | EMAIL="email_address" envsubst | kubectl apply -f -
```
