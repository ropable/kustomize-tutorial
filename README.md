# Kustomize usage tutorial

Resources for learning and experimenting with Kustomize.

Useful Kustomize references:

- Basic guide: <https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/>
- Kustomize reference: <https://kubectl.docs.kubernetes.io/references/kustomize/>
- The Kustomization file: <https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/>

## Prerequisites

- Shell environment with a newish version of kubectl installed (1.29+), which will have Kustomize included.
- Configure kubectl to have a context per cluster you need to work with.
- Test that kubectl is communicating with the cluster, e.g. `kubectl cluster-info`

## Step 0 - Intro

1. Clone the repository and cd into it.
1. Run `git checkout 00-intro`
1. Run `kubectl kustomize overlays/uat`, noting the YAML output.
1. Run `kubectl apply -k overlays/uat` and watch the **default** namespace on the cluster, noting the created Deployment workload (nginx-deployment).
1. Run `kubectl delete -k overlays/uat` to clean up the workload.
1. Run `kubectl apply -k overlays/prod` to review the Deployment workload and note differences from the UAT one (tagged image version, resource request).
1. Run `kubectl delete -k overlays/prod` to clean up.

Learning outcomes:

- Resource deployment / removal.
- Differences between overlays based on patches.

## Step 1 - Refinements

1. Run `git checkout 01-refinements`
1. Run `kubectl apply -k overlays/uat` and watch the deployment resource start, having the -uat name suffix.
1. Run `kubectl apply -k overlays/prod`, and note the difference in resource name.
1. Take a look at the configuration of each resource and note differences.
1. Clean up the resources.

Learning outcomes:

- Extended variables in deployment resource (environment variable, restartPolicy, imagePullPolicy, etc.)
- **nameSuffix** in kustomization.yaml (<https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/namesuffix/>).

## Step 2 - Service & label selectors

1. Run `git checkout 02-service`
1. Run `kubectl apply -k overlays/uat` and then `kubectl apply -k overlays/prod`.
1. Examine the created deployments and services, noting the different label selectors that have been added to each resource (app, variant).
1. Clean up the resources.

Learning outcomes:

- Setting `labels` in kustomizations.yaml adds labels to all resources (<https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/labels/>).
- Setting `includeSelectors: true` also adds selectors on those labels.

## Step 3 - Secret values

1. Run `git checkout 03-secrets`
1. Inside the overlays/uat directory, create a `.env` file containing the line: `SECRET_VALUE=uat_secret-value`
1. Run `kubectl apply -k overlays/uat` and note the opaque secret that has been created along with the deployment and service.
1. Start a shell session in the Nginx workload and run `echo $SECRET_VALUE` to confirm that the value is present as an environment variable.
1. Repeat the steps above for the prod overlay, if desired.
1. Clean up the resources.

Learning outcomes:

- Use a **secretGenerator** to generate a resource from values in a file: <https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/secretgenerator/>
- Observe how to set environment variables inside deployments using key values in opaque secrets.
