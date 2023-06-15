# external-dns-package

This package provides integration with Cloud Provider's DNS functionality using [external-dns](https://github.com/kubernetes-sigs/external-dns).

## Components

* external-dns

## Configuration

The following configuration values can be set to customize the external-dns installation.

### Global

| Value | Required/Optional | Default     | Description |
|-------|-------------------|-------------|-------------|
| `namespace` | Required | **external-dns** | The namespace in which to deploy external-dns |
| `create_namespace` | Required | **True** | Needs the namespace where external-dns will be installed to be created? |
| `domain` | Optional | <EMPTY> | If set, external-dns will only act on these domains |
| `target.service` | Required | True | Target services for external-dns integration. When True every service with external-dns annotation will be published by external-dns |
| `target.ingress` | Required | False | Target ingresses for external-dns integration. When True every Ingress will be published by external-dns |
| `target.contour` | Required | False | Target contour httpproxy for external-dns integration. When True every HTTPProxy will be published by external-dns |
| `provider` | Required | <EMPTY> | DNS provider to use: Supported values are `aws`, `bind`, `azure`|
| `aws.auth.irsa.role` | Required (or aws.auth.iam)| AWS IRSA IAM Role's to integrate with Route53 |
| `aws.auth.iam.access_key_id` | Required (or aws.auth.irsa) | AWS IAM Role's Key Id to integrate with Route53 |
| `aws.auth.iam.secret_access_key` | Required (or aws.auth.irsa) | AWS IAM Role's Secret Access Key to integrate with Route53 |
| `...`| | |

## Usage Example

This walkthrough guides you through using external-dns...

By default, only services with this annotation will be published `external-dns.alpha.kubernetes.io/hostname=MY-DOMAIN.`

## Test

Test Route53 config:
```
ytt -f src/bundle/config -v domain=apps.example.org -v provider=aws -v aws.auth.iam.access_key_id=EXAMPLEKEY -v aws.secret_access_key=SECRETKEY
```

## Test in minikube

Start minikube:
```
minikube start
```

Install kapp-controller 0.20+
```
kubectl apply -f https://github.com/vmware-tanzu/carvel-kapp-controller/releases/latest/download/release.yml
```

Install the Package Metadata:
```
kubectl apply -f target/k8s
```

Install the Required RBAC for the package install (create the control NS):
```
kubectl apply -f target/test/packageinstall-ns-rbac.yaml
```

Create the configuration file for your cluster:
```
kubectl create secret generic external-dns -n external-dns-package --from-file=values.yaml=src/examples-values/minikube.yaml -o yaml --dry-run=client | kubectl apply -f -
```

Create the package:
```
kubectl apply -f target/test/packageinstall.yaml
```

Verify the installation:
```
watch kubectl get packageinstall -A
```

If there's an issue, you can verify the problem with:

```
kubectl get packageinstall external-dns -n external-dns-package -o yaml
```

## Develop checklist


1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f src/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)