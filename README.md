# AWS Distro for OpenTelemetry Helm Charts
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## Introduction
This AWS Distro for OpenTelemetry (ADOT) Helm Charts repository contains [Helm](https://helm.sh/) charts to provide easy mechanisms to setup the ADOT Collector and other collection agents to collect telemetry data such as metrics, logs and traces to send to AWS monitoring services.

This repository contains a [Helm](https://helm.sh/) chart to provide easy to operate, end-to-end  [AWS Elastic Kubernetes Service](https://aws.amazon.com/eks/) (EKS) on [AWS Elastic Compute Cloud](https://aws.amazon.com/ec2/) (EC2) monitoring with [AWS Distro for OpenTelemetry(ADOT) Collector](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html) for metrics. This Helm chart is useful for customers who use EKS on EC2 and want to collect metrics to send to Amazon CloudWatch Container Insights and Amazon Managed Service for Prometheus(AMP).

## Getting Started

[Helm](https://helm.sh/) must be installed to use the chart. Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add this repo as follows:
```console
$ helm repo add aws-observability https://aws-observability.github.io/aws-otel-helm-charts
$ helm search repo aws-observability # Run this command in order to see the charts.
```

### :warning: Warning: Fluent Bit and Fargate logging templates are deprecated and were removed from the Helm chart on December 30th, 2022 (>= 0.11.0)

There is planned work in the upstream OpenTelemetry repositories to stabilize logs, and so that means that our Helm Chart will have to be updated to reflect these eventual changes.  The changes required for our Helm Chart include:

* Removing the [Fluent Bit logging templates](https://github.com/aws-observability/aws-otel-helm-charts/tree/main/charts/adot-exporter-for-eks-on-ec2/templates/aws-for-fluent-bit)
* Removing the [Fargate logging templates](https://github.com/aws-observability/aws-otel-helm-charts/tree/main/charts/adot-exporter-for-eks-on-ec2/templates/aws-fargate-logging)

Therefore, we recommend revising any code that utilizes these templates by December 30th, 2022 or continue to use the version of the Helm Chart that pre-dates December 30th (<= 0.10.0), 2022 to avoid any issues. As an alternative, you can also make use of the [`aws-for-fluent-bit`](https://github.com/aws/eks-charts/tree/master/stable/aws-for-fluent-bit) Helm Chart ([more details](https://github.com/aws-observability/aws-otel-helm-charts/issues/88)).

Once logs are stabilized upstream and implemented, a new version of the Helm Chart will be released and users may upgrade to this instance instead.  Thank you for your cooperation.


## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for more information.

> NOTE: we're currently not accepting new PRs as we're evaluating our priorities concerning the ADOT Helm Charts. We plan to update this repo with a concrete timeline with the new policy by end of March 2023.

## License

This project is licensed under the Apache 2.0 License.

## ADOT Roadmap

You can track upcoming features and enhancements for ADOT on its [roadmap](https://github.com/orgs/aws-observability/projects/4).

## Support

Please note that as per policy, we're providing support via GitHub on a best effort basis. However, if you have AWS Enterprise Support you can create a ticket and we will provide direct support within the respective SLAs.

## Security issue notifications
If you discover a potential security issue in this project we ask that you notify AWS/Amazon Security via our [vulnerability reporting page](http://aws.amazon.com/security/vulnerability-reporting/). Please do **not** create a public github issue.

## Maintainers

- [Anthony Mirabella](https://github.com/Aneurysm9)
