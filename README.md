# AWS Distro for OpenTelemetry Helm Charts
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## Introduction
This AWS Distro for OpenTelemetry (ADOT) Helm Charts repository contains [Helm](https://helm.sh/) charts to provide easy mechanisms to setup the ADOT Collector and other collection agents such as Fluent Bit to collect telemetry data such as metrics, logs and traces to send to AWS monitoring services.

This repository contains a [Helm](https://helm.sh/) chart to provide easy to operate, end-to-end  [AWS Elastic Kubernetes Service](https://aws.amazon.com/eks/) (EKS) on [AWS Elastic Compute Cloud](https://aws.amazon.com/ec2/) (EC2) monitoring with [AWS Distro for OpenTelemetry(ADOT) Collector](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html) for metrics and [Fluent Bit](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html) for logs. This Helm chart is useful for customers who use EKS on EC2 and want to collect metrics and logs to send to Amazon CloudWatch Container Insights and Amazon Managed Service for Prometheus(AMP).

## Getting Started

[Helm](https://helm.sh/) must be installed to use the chart. Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add this repo as follows:
```console
$ helm repo add aws-observability https://aws-observability.github.io/aws-otel-helm-charts
$ helm search repo aws-observability # Run this command in order to see the charts.
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for more information.

## License

This project is licensed under the Apache 2.0 License.

# ADOT Roadmap 

You can track upcoming features and enhancements for ADOT on its [roadmap](https://github.com/orgs/aws-observability/projects/4).

# Support

Please note that as per policy, we're providing support via GitHub on a best effort basis. However, if you have AWS Enterprise Support you can create a ticket and we will provide direct support within the respective SLAs.

# Maintainers

- [Anthony Mirabella](https://github.com/Aneurysm9)
- [Ruthvik Ravindra](https://github.com/ruthvik17)
