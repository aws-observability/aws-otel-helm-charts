## AWS Observability Build Repo

This repo is for all release builds that will also generate Docker images and other artifacts that will be published to Amazon ECR and other container or package registries.

## Compliance and Security

* Before publishing any images, open source and security approvals are mandatory.

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## Workflow Usage Instructions

### public-ecr-image-build-and-push

This workflow can be used to deploy any aws-observability ECR image. It uses a `workflow_dispatch` event and can hence be manually triggered.
The following variables must be specified when triggering:

* Image version (eg. `v0.5.0`)
* Name of the ECR Repo to push image to (eg. `aws-otel-collector`)
* Name of the github organization and repository where the code is hosted (eg. `aws-observability/aws-otel-collector`)
* Path within repository containing Dockerfile and optional ECR_REPO file

For any ECR image to push successfully, the following conditions must be met:

1. ECR repository specification:

* Either the name of the ECR Repo must match the name of the github repository (eg. `aws-observability/aws-otel-collector` pushes to ECR repository `aws-otel-collector`)

OR 

* The github repository contains a file called ECR_REPO in the same directory containing the Dockerfile, and this file contains only the name of the ECR repository to push to. (eg. `aws-observability/aws-otel-community` contains a sample app in directory `sample-apps/prometheus`. Within this directory exists the code for the sample app, a Dockerfile, and an ECR_REPO file which contains the string `prometheus-sample-app`. This pushes the code into the `prometheus-sample-app` ECR repository)

2. Release AWS Credentials must be set up as secrets within this repository. The naming convention is as follows:

* AWS Access Key must be stored in secret RELEASE_{{ECR_REPO_NAME_IN_CAPS_AND_UNDERSCORES}}_KEY_ID
* AWS Secret Access Key myst be stored in secret RELEASE_{{ECR_REPO_NAME_IN_CAPS_AND_UNDERSCORES}}_SECRET

These AWS credentials must be set up and be restricted to only be able to push images into one ECR repository.

Examples:

* `aws-otel-collector` has the following secrets configured: RELEASE_AWS_OTEL_COLLECTOR_KEY_ID, RELEASE_AWS_OTEL_COLLECTOR_SECRET
* `aws-sigv4-proxy` has the following secrets configured: RELEASE_AWS_SIGV4_PROXY_KEY_ID, RELEASE_AWS_SIGV4_PROXY_SECRET

## License

This project is licensed under the Apache-2.0 License.

