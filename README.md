# Upload Coverage Action

A GitHub Action to upload code coverage reports to Google Cloud Storage (GCS).

## Description

This action helps you automate the process of uploading code coverage reports to a Google Cloud Storage bucket. It's particularly useful for maintaining a history of coverage reports across different branches and commits.

The coverage report is uploaded to a bucket set up on `gcp_coverage_bucket` and then the folder structure will be `{branch_name}/{timestamp}` and the idea is to upload the coverage.xml.

This action is used as a previous step to the action (missing link here).

Right now it only works with python as it expects a python version to be passed as an argument to run on that python version only and upload just one python coverage report (in case a matrix strategy is used).

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version` | Python version to use for the action | No | `3.10` |
| `gcp-project-id` | Google Cloud Project ID | Yes | - |
| `gcp-credentials` | GCP Service Account credentials in JSON format (delete line breaks before uploading to secrets) | Yes | - |
| `gcp-coverage-bucket` | GCS bucket name where coverage reports will be uploaded | Yes | - |
| `coverage-path` | Path to the coverage report file | No | `coverage.xml` |

## Usage

Here's an example of how to use this action in your workflow:

``` yaml

name: Coverage Upload
on: [push, pull_request]

jobs:
  upload-coverage:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12']

    steps:
      - uses: actions/checkout@v3
      
      - name: Generate coverage report
        run: |
          pip install coverage
          coverage run -m unittest discover
          coverage report -m
          coverage xml
          
      - name: Upload coverage
        uses: baobabsoluciones/upload-coverage@v1
        with:
          python-version: ${{ matrix.python-version }}
          gcp-project-id: ${{ secrets.GCP_PROJECT_ID }}
          gcp-credentials: ${{ secrets.GCP_CREDENTIALS }}
          gcp-coverage-bucket: ${{ secrets.GCP_COVERAGE_BUCKET }}

```
