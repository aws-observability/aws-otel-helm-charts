# ADOT Helm chart for EKS on EC2 metrics and logs to Amazon CloudWatch Container Insights
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

This [Helm](https://helm.sh/) chart provides easy to use [AWS Elastic Kubernetes Service](https://aws.amazon.com/eks/) (EKS) on [AWS Elastic Compute Cloud](https://aws.amazon.com/ec2/) (EC2) monitoring with [AWS Distro for OpenTelemetry(ADOT) Collector](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html) for metrics and [Fluent Bit](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html) for logs. Therefore, the Helm chart is useful for customers who use EKS on EC2 and want to collect metrics and logs from clusters. The metrics will be sent to Amazon CloudWatch Container Insights and [Amazon Managed Service for Prometheus](https://aws.amazon.com/prometheus/?trk=f6e79447-9b4c-4310-8415-1a76de2de47f&sc_channel=ps&sc_campaign=acquisition&sc_medium=ACQ-P|PS-GO|Non-Brand|Desktop|SU|Management%20Tools|Solution|US|EN|DSA&ef_id=CjwKCAiAg6yRBhBNEiwAeVyL0KLIKHm3fznhVURTI6T-WBvANCmqo3r0-pYp_U82lIDDMmXRXDk0DBoCohQQAvD_BwE:G:s&s_kwcid=AL!4422!3!579408286031!!!g!!) (AMP) and the logs will be sent to Amazon Cloudwatch.

The Helm chart configured in this repository deploys the ADOT Collector and Fluent Bit as [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) and is ready to collect metrics and logs and send them to Amazon CloudWatch Container Insights and AMP.

## Helm Chart Structure
```console
adot-exporter-for-eks-on-ec2/
|-- scripts/ 
|   |-- install-tools.sh
|   |-- lint-charts.sh
|   |-- validate-charts.sh
|-- templates/
|   |-- NOTES.txt
|   |-- aws-for-fluent-bit/
|   |   |-- _helpers.tpl
|   |   |-- clusterrole.yaml
|   |   |-- clusterrolebinding.yaml
|   |   |-- configmap.yaml
|   |   |-- daemonset.yaml
|   |   |-- namespace.yaml
|   |   |-- serviceaccount.yaml
|   |-- aws-fargate-logging/
|   |   |-- configmap.yaml
|   |   |-- namespace.yaml
|   |-- adot-collector/
|   |   |-- _helpers.tpl
|   |   |-- clusterrole.yaml
|   |   |-- clusterrolebinding.yaml
|   |   |-- configmap.yaml
|   |   |-- daemonset.yaml
|   |   |-- namespace.yaml
|   |   |-- serviceaccount.yaml
|   |   |-- sidecar.yaml
|   |   |-- sidecarnamespace.yaml
|-- documentation
|   |-- metrics_to_CloudWatch_AMP.md
|   |-- deploy_collector_as_sidcar.md
|   |-- aws_logging_on_Fargate.md 
|-- Chart.yaml
|-- values.schema.json
|-- values.yaml
|-- Makefile
|-- README.md
```

`templates` folder contains two subfolders, `aws-for-fluent-bit` and `aws-otel-collector`, and each subfolder contains template files that will be evaluated with the default values configured in `values.yaml.`

`script` folder contains shell script files to run chart validation and lint tests with [Helm Lint](https://helm.sh/docs/helm/helm_lint/) and [Kubeval](https://kubeval.instrumenta.dev/).

`values.yaml` file stores parameterized template defaults in the Helm chart. Using this file, we can provide more flexibility to our users to expose configuration that can be overriden at installation and upgrade time.

`values.schema.json` file contains schemas of each values in values.yaml. It defines each values’ type, required keys, and constraints.

`_helpers.tpl` files are used to define GO template helpers to create name variables.

## Prerequisites

The following prerequisites need to be set up in order to install this Helm chart.

- Your EKS Cluster on EC2 
- [Amazon CloudWatch Container Insights prerequisites](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-prerequisites.html)
- Using the above instructions, please attach the ‘prometheusremotewriteacess’ policy to the nodes.
- Your working [Amazon Managed Service for Prometheus(AMP) workspace](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-onboard-create-workspace.html).
- [Amazon Managed Service for Grafana](https://aws.amazon.com/grafana/) is the go-to platform for visualizing AMP data.
- [Helm v3+](https://helm.sh/docs/helm/helm_install/)

## Get Repository Information

[Helm](https://helm.sh/) must be installed to use the chart. Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add this repo as follows:
```console
$ helm repo add [REPO_NAME] https://aws-observability.github.io/aws-otel-helm-charts
$ helm search repo [REPO_NAME] # Run this command in order to see the charts.
```

## Configuration
To see all configurable options with detailed comments:

```console
$ helm show values [REPO_NAME]/adot-exporter-for-eks-on-ec2
```

By changing values in `values.yaml`, you are able to customize the chart to use your preferred configuration.

Following options are some useful configurations that can be applied to this Helm chart.

### Documentation

Please follow the links to get more details for specific use cases and deployment of the collector.

* To send metrics to CloudWatch and Amazon Managed Service for Prometheus(AMP) and logs to CloudWatch. Click [here](documentation/metrics_to_cloudwatch_amp.md).
* To deploy the Adot Collector as a Sidecar. Click [here](documentation/deploy_collector_as_sidecar.md).
* To deploy EKS on AWS Fargate. Click [here](documentation/aws_logging_on_fargate.md).
* The various scenarios under which the Helm chart was tested has been documented [here](documentation/helm_chart_test_doc.pdf).


### Deploy ADOT Collector as Deployment and StatefulSet

Deploying ADOT Collector as Deployment and StatefulSet mode requires installing ADOT Operator. See [OpenTelemetry Operator Helm Chart](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-operator) for detailed explanation.

## Uninstall Chart

The following command uninstalls the chart. 
This will remove all the Kubernetes components associated with the chart and deletes the release.

```console
$ helm uninstall [RELEASE_NAME]
```

## Upgrade Chart

```console
$ helm upgrade [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2
```

## Contributing

See [CONTRIBUTING.md](../../CONTRIBUTING.md).

## Contributors

[Hyunuk Lim](https://github.com/hyunuk)

[James Park](https://github.com/JamesJHPark)

[Ruthvik Ravindra](https://github.com/ruthvik17)

## Other useful links

[Set up Fluent Bit as a DaemonSet to send logs to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html)

[Using AWS Distro for OpenTelemetry](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html)

[Learn more about OpenTelemetry](https://opentelemetry.io/docs/)

## Questions

To learn more about how to use AWS Distro for OpenTelemetry to collect data for your observability solution, check out the hands-on [AWS Observability workshop](https://observability.workshop.aws/en/adot.html). For any questions and issues, please feel free to reach out via [AWS OTeL Community](https://github.com/aws-observability/aws-otel-community/issues).


## License

Apache 2.0 License.

## Support Plan

Our team plans to fully support the code we plan to release in this repo.
