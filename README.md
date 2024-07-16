# openshift-operator-pr

GitHub action to submit PRs to OpenShift operator repositories.

### Operator repositories

- [k8s-operatorhub/community-operators](https://github.com/k8s-operatorhub/community-operators)
- [redhat-openshift-ecosystem/community-operators-prod](https://github.com/redhat-openshift-ecosystem/community-operators-prod)
- [redhat-openshift-ecosystem/certified-operators](https://github.com/redhat-openshift-ecosystem/certified-operators)

### Before using this action

You must fork the [operator repositories](#operator-repositories) you are willing to submit your operator to.

### Usage

```yaml
name: Release

on:
  push:
    tags:
      - "*"

jobs:
  operator-pr:
    name: Version
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Fetch tags
        run: git fetch --force --tags

      - name: Get Version
        id: version
        run: |
          VERSION=sha-${GITHUB_SHA::8}
          if [[ $GITHUB_REF == refs/tags/* ]]; then
            VERSION=${GITHUB_REF/refs\/tags\//}
          fi
          echo ::set-output name=version::${VERSION}

      # TODO:
      # - Generate bundle
      # - Push bundle image to a registry

      - name: Operator PR
        uses: mariadb-operator/openshift-operator-pr@v0.0.1
        with:
          name: "mariadb-operator"
          version: "${{ steps.version.outputs.version }}"
          fork-repo-name: "mariadb-operator/community-operators"
          upstream-repo-url: "https://github.com/k8s-operatorhub/community-operators"
          bundle-path-dir: "bundle"
          ci-path-file: "ci.yaml"
          user-name: "Martin Montes"
          user-email: "martin11lrx@gmail.com"
``` 
