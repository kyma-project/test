## Release log uploader 
For auditing purposes, we need to provide the successful test results for each release, as a quick workaround we can retain test logs for the last 12 months. 
Currently, we can only store logs for the last 3 months in public Github and only 1 Month in SAP Github, which is insufficient.

The goal is to upload logs from pre-release tests to a GCP bucket for auditing purposes.

### `Upload release report` reusable workflow

[Reusable workflow](https://github.com/kyma-project/compliancy/blob/main/.github/workflows/notify-release-report.yml) should be executed only when your pre-release tests are successfully completed.
It will capture:
- workflow status and a status of each job
- logs from the test run
and send it to the GCP bucket.

### How it can be used in your workflow:

It requires specific permissions:
```
permissions:
  contents: read
  actions: read
```
It requires also:
- `release_version`
- `needs` and `if` statement adjusted so that it is triggered only after the tests are successfully completed.

Sample job configuration:
```
  notify:
    needs: test
    if: ${{ needs.test.result == 'success' }}
    uses: kyma-project/compliancy/.github/workflows/notify-release-report.yml@main
    secrets: inherit
    with:
      release_version: ${{ inputs.version }}
```
