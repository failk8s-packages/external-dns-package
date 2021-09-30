# external-dns-package

This package provides integration with Cloud Provider's DNS functionality using [external-dns](https://github.com/kubernetes-sigs/external-dns).

## Components

* external-dns

## Configuration

The following configuration values can be set to customize the external-dns installation.

### Global

| Value | Required/Optional | Description |
|-------|-------------------|-------------|
| `namespace` | Optional | The namespace in which to deploy external-dns. |
| `domain` | Required | The domain to use for external-dns. |
| `provider` | Required | DNS provider to use: Supported values are `aws`, `bind`, `azure`|
| `aws.access_key_id` | Required | AWS IAM Role's Key Id to integrate with Route53 |
| `aws.secret_access_key` | Required | AWS IAM Role's Secret Access Key to integrate with Route53 |
| `...`| | |

## Usage Example

This walkthrough guides you through using external-dns...

## Test

Test Route53 config:
```
ytt -f src/bundle/config -v domain=apps.example.org -v provider=aws -v aws.access_key_id=EXAMPLEKEY -v aws.secret_access_key=SECRETKEY
```

## Develop checklist

1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f target/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)

**NOTE**: `develop` versions will not be image locked and will have a version of 0.0.0+develop to comply with semver.
