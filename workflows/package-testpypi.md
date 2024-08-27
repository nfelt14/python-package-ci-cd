# package-testpypi.yml

This workflow will build the package using the
[`hynek/build-and-inspect-python-package`](https://github.com/hynek/build-and-inspect-python-package)
action, upload the package to [TestPyPI](https://test.pypi.org), and then verify that the package
can be installed from [TestPyPI](https://test.pypi.org).

In order to ensure each version uploaded to [TestPyPI](https://test.pypi.org) is unique, the
workflow will first create a unique `.postN` version number for the package on top of the
officially released version of the package, incrementing `N` each time the workflow runs.

> [!IMPORTANT]
> When calling this reusable workflow, the permissions must be set as follows:
>
> ```yaml
> permissions:
>   contents: read
>   id-token: write
>   attestations: write
> ```

> [!NOTE]
> This workflow uses concurrency to limit the number of builds that can run at the same time
> to a single build. This concurrency is shared across the `'pypi (Reusable Workflows)'` concurrency
> group within the repo that calls this workflow.

> [!NOTE]
> This workflow uses the following GitHub Actions:
>
> - [actions/checkout](https://github.com/actions/checkout)
> - [tektronix/python-package-ci-cd/actions/create_unique_testpypi_version](https://github.com/tektronix/python-package-ci-cd)
> - [hynek/build-and-inspect-python-package](https://github.com/hynek/build-and-inspect-python-package)
> - [actions/download-artifact](https://github.com/actions/download-artifact)
> - [pypa/gh-action-pypi-publish](https://github.com/pypa/gh-action-pypi-publish)
> - [actions/setup-python](https://github.com/actions/setup-python)
> - [nick-fields/retry](https://github.com/nick-fields/retry)
>
> See the [Workflow file][workflow-file] for the currently used versions of each GitHub Action.

> [!TIP]
> See the [Workflow file][workflow-file] for implementation details.

## Inputs

| Input variable | Necessity | Description                                                                         | Default |
| -------------- | --------- | ----------------------------------------------------------------------------------- | ------- |
| `package-name` | required  | The name of the package to build, upload, and install.                              |         |
| `repo-name`    | required  | The full name of the repository to use to gate uploads, in the format `owner/repo`. |         |

## Example

```yaml
name: Publish to TestPyPI
on:
  push:
    branches: [main]
concurrency:  # This concurrency is not required, but can be added if extra control of concurrent builds is required
  group: pypi
jobs:
  package-testpypi:
    uses: tektronix/python-package-ci-cd/.github/workflows/_reusable-package-testpypi.yml@v1.0.0
    with:
      package-name: my-package  # required
      repo-name: owner/my-package  # required
    permissions:
      contents: read
      id-token: write
      attestations: write
```

[workflow-file]: ../.github/workflows/_reusable-package-testpypi.yml