# Artifact Metadata Repository CloudEvent Handler

## Switching Context

If Artifact Metadata Repository CloudEvent Handler is installed on a separate cluster, such as with a view profile cluster, it is important that the correct cluster is targeted when updating the installation.

```bash
# Switch context to view profile cluster
kubectl config use-context VIEW-CLUSTER-NAME

# get current install values for tap package
tanzu package installed get tap --values-file tap-values.yaml -n tap-install

# update the tap-values in an editor according to the desired configuration

# update the installeds tap package on the cluster
tanzu package installed update tap --values-file tap-values.yaml -n tap-install
```

## Install

On the view profile cluster or full profile cluster, the Metadata Store installation needs to be updated to have Artifact Metadata Repository deployed. 
When the Artifact Metadata Repository is deployed, Artifact Metadata Repository CloudEvent Handler will also be deployed alongside it. 

To do so, additional TAP Values are required:
```yaml
metadata_store:
    amr:
        deploy: true
```

## Uninstall

Artifact Metadata Repository CloudEvent Handler is deployed alongside Artifact Metadata Repository. Therefore, to undeploy Artifact Metadata Repository CloudEvent Handler, the TAP values can be updated with:

```yaml
metadata_store:
    amr:
        deploy: false
```

**Note:** When Artifact Metadata Repository Observer is deployed on the same cluster with the full TAP profile, Artifact Metadata Repository Observer will also be undeployed when Artifact Metadata Repository is undeployed.