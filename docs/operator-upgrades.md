# Upgrading the Mondoo Operator
The Mondoo Operator version numbers are expressed as `x.y.z`, where `x` is the major version, `y` is the minor version, and `z` is the patch version, following [Semantic Versioning](https://semver.org/) standards. Our release approach ensures there are no breaking changes between two adjacent minor versions. For example, you can upgrade the Mondoo Operator from `v0.2.15` to `v0.3.0` without any manual actions. The Mondoo Operator automatically executes any required migration and/or cleanup steps.

You can skip Mondoo Operator patch versions, however we don't recommend skipping minor versions. For example, if you upgrade from `v0.2.0` directly to `v0.4.0`, the Mondoo Operator may not behave as expected, and you may leave behind unused resources in the cluster. Skipping a minor version may require manual actions to ensure the Mondoo Operator is fully functional.

**WARNING: Never try to upgrade the Mondoo Operator by simply changing the tag for the Mondoo Operator container image.**

## Recommended Mondoo Operator upgrade process
Follow these steps for a smooth Mondoo Operator upgrade:
1. Verify the Mondoo Operator version currently running in the cluster:
    ```bash
    kubectl get deployments -n mondoo-operator -o jsonpath='{.items[*].spec.template.spec.containers[0].image}'
    ```
2. Check the latest version of the Mondoo Operator on our [Releases](https://github.com/mondoohq/mondoo-operator/releases/latest) GitHub page.

Based on the version difference between your Mondo Operator and the latest release, follow the steps below.

### If your current Mondoo Operator is no more than one minor version behind  
If there is **not** more than one minor version difference between the installed Mondoo Operator and the latest release, apply the latest manifest to the cluster:
```bash
kubectl apply -f https://github.com/mondoohq/mondoo-operator/releases/latest/download/mondoo-operator-manifests.yaml
```

### If your current Mondoo Operator is more than one minor version behind
If there **is** more than one minor version difference between the installed Mondoo Operator and the latest release, you must apply the manifest files for each minor version between the two versions. For example, if the version installed is `v0.2.0` and the latest version is `v0.4.3`, you must install `v0.3.0`. Follow these steps:

1. Apply the manifest for `v0.3.0` (the version you skipped):
    ```bash
    kubectl apply -f https://github.com/mondoohq/mondoo-operator/releases/v0.3.0/download/mondoo-operator-manifests.yaml
    ```
2. Wait until the new version of the Mondoo Operator is running and verify there are no errors in the operator log:
    ```bash
    kubectl logs -n mondoo-operator deployment/mondoo-operator-controller-manager
    ```
    It's essential to check the logs and wait for the new version of the operator to run; directly upgrading to the next version can result in skipped internal upgrade procedures and unexpected behavior.
3. Apply the manifest for `v0.4.3` (the latest version):
    ```bash
    kubectl apply -f https://github.com/mondoohq/mondoo-operator/releases/latest/download/mondoo-operator-manifests.yaml
    ```
Adjust the steps above to fit your current situation. There may be multiple minor release versions between your installed version and the latest release. You must install each minor version independently, wait between each update to verify that the version installed properly and the log is error-free.      
