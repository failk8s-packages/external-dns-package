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
2. Update your [vendir.yml](./src/bundle/vendir.yml) with your upstreams
3. `vendir sync` in `src/bundle` to fetch your new upstream files
4. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
5. Update your [bundle.yml](./src/bundle/.imgpkg/bundle.yml) file
<<<<<<< HEAD
6. Test your bundle: `ytt -f bundle`
7. Lock images used: `kbld -f . --imgpkg-lock-output .imgpkg/images.yml`
=======
6. Test your bundle: `ytt -f config`
7. Lock images used: `ytt -f config ... | kbld -f - --imgpkg-lock-output .imgpkg/images.yml`
>>>>>>> develop
8. Publish your bundle: `imgpkg push --bundle quay.io/failk8s/<NAME>-package:<VERSION> --file .`. These steps can be done via [hack/build-package.sh](./hack/build-package.sh)
9. Package up your k8s manifests and test in k8s [hack/package-manifests.sh](./hack/package-manifests.sh). The files will be in `target` folder.