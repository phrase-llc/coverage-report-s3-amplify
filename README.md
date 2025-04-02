# coverage-report-s3-amplify

This GitHub Action automates the process of uploading coverage report artifacts to an S3 bucket and initiating an AWS Amplify deployment.

## Overview

The action performs the following tasks:

- **S3 Upload:** Uploads coverage artifacts from a specified directory to an S3 bucket at the path `artifacts/<BRANCH_NAME>/`.
- **Amplify Deployment:** Initiates a deployment on AWS Amplify using the uploaded artifacts as the source for the `main` branch.
- **Report Link Addition:** Appends the generated coverage report URL to the GitHub Actions step summary for easy reference.

## Features

- **S3 Upload:** Automatically uploads coverage artifacts to the specified S3 bucket.
- **Amplify Deployment:** Triggers a deployment on AWS Amplify using the uploaded artifacts.
- **Report Link Addition:** Adds a link to the coverage report in the GitHub Actions step summary.

## Input Parameters

The action requires the following inputs:

| Input Name              | Description                                             | Required |
| ----------------------- | ------------------------------------------------------- | -------- |
| `AWS_ACCESS_KEY_ID`     | Your AWS Access Key ID                                  | Yes      |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Access Key                              | Yes      |
| `BUCKET_NAME`           | Name of the S3 bucket                                   | Yes      |
| `AWS_DEFAULT_REGION`    | AWS region                                              | Yes      |
| `BRANCH_NAME`           | Branch name (e.g., `main`, `develop`)                   | Yes      |
| `AMPLIFY_APP_ID`        | AWS Amplify Application ID                              | Yes      |
| `AMPLIFY_HOST_URL`      | Amplify Host URL (used to create the report link)       | Yes      |

## Usage

To use this action in your workflow, add a step similar to the following:

```yaml
name: CI Workflow
on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # Add a step to set up AWS CLI if needed.
      - name: Set up AWS CLI
        run: |
          sudo apt-get update && sudo apt-get install -y awscli

      # The following step has been added to run the coverage-report-s3-amplify action.
      - name: Run coverage-report-s3-amplify action
        uses: phrase-llc/coverage-report-s3-amplify@main
        continue-on-error: true # Even if this action fails, the job will continue to the next step.
        if: success() # This step runs only if all previous steps have succeeded.
        with:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          BUCKET_NAME: ${{ secrets.BUCKET_NAME }}
          AWS_DEFAULT_REGION: ${{ secrets.AWS_DEFAULT_REGION }}
          BRANCH_NAME: ${{ github.ref_name }}
          AMPLIFY_APP_ID: ${{ vars.AMPLIFY_APP_ID }}
          AMPLIFY_HOST_URL: ${{ vars.AMPLIFY_HOST_URL }}
```

## Prerequisites

- **AWS CLI:** Ensure that the AWS CLI is installed and configured correctly. If necessary, include a setup step in your workflow as shown above.
- **AWS Credentials and Resources:** Make sure your AWS credentials, S3 bucket, and Amplify application details are correctly configured.

## License

This action is licensed under the [Apache License 2.0](LICENSE).