# ADOT Helm chart for EKS on EC2 metrics to Datadog
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

This [Helm](https://helm.sh/) chart provides easy to use [AWS Elastic Kubernetes Service](https://aws.amazon.com/eks/) (EKS) on [AWS Elastic Compute Cloud](https://aws.amazon.com/ec2/) (EC2) monitoring with [AWS Distro for OpenTelemetry(ADOT) Collector](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html) for metrics and send them to [Datadog](https://www.datadoghq.com). Therefore, the Helm chart is useful for customers who use EKS on EC2 and want to collect metrics to send to Datadog.

The Helm chart configured in this repository deploys the ADOT Collector as a [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) and is ready to collect metrics and send them to Datadog.

## Helm Chart Structure
```console
datadog-exporter-for-eks-on-ec2/
|-- scripts/ 
|   |-- install-tools.sh
|   |-- lint-charts.sh
|   |-- validate-charts.sh
|-- templates/
|   |-- NOTES.txt
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
|-- Chart.yaml
|-- values.schema.json
|-- values.yaml
|-- Makefile
|-- README.md
```

`templates` folder contains one subfolder, `adot-collector`, and each subfolder contains template files that will be evaluated with the default values configured in `values.yaml.`

`script` folder contains shell script files to run chart validation and lint tests with [Helm Lint](https://helm.sh/docs/helm/helm_lint/) and [Kubeval](https://kubeval.instrumenta.dev/).

`values.yaml` file stores parameterized template defaults in the Helm chart. Using this file, we can provide more flexibility to our users to expose configuration that can be overriden at installation and upgrade time.

`values.schema.json` file contains schemas of each values in values.yaml. It defines each values’ type, required keys, and constraints.

`_helpers.tpl` files are used to define GO template helpers to create name variables.

## Prerequisites

The following prerequisites need to be set up in order to install this Helm chart.

- Your EKS Cluster on EC2 
- A valid [Datadog API key](https://docs.datadoghq.com/account_management/api-app-keys/)
- [Helm v3+](https://helm.sh/docs/helm/helm_install/)

## Get Repository Information

[Helm](https://helm.sh/) must be installed to use the chart. Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add this repo as follows:
```console
$ helm repo add [REPO_NAME] https://aws-observability.github.io/aws-otel-helm-charts
$ helm search repo [REPO_NAME] # Run this command in order to see the charts.
```

## Install Chart

```console
$ helm install \
  [RELEASE_NAME] [REPO_NAME]/datadog-exporter-for-eks-on-ec2 \
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] --set adotCollector.daemonSet.exporters.datadog.api.key=[API_KEY]
```

`CLUSTER_NAME` and `AWS_REGION` must be specified with your own EKS cluster and the region.
You can find these values by executing following command.

```console
$ kubectl config current-context

[IAM_User_Name]@[CLUSTER_NAME].[AWS_REGION].eksctl.io
```

`API_KEY` is a valid Datadog API key. It can be found on your [organization page](https://app.datadoghq.com/organization-settings/api-keys). Your API key will be stored in a Secret.

To verify the installation is successful, you can execute the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE                NAME                             READY   STATUS    RESTARTS   AGE
datadog-adot-metrics  adot-collector-daemonset-7nrst   1/1     Running   0          4s
datadog-adot-metrics  adot-collector-daemonset-x7n8x   1/1     Running   0          4s
```

If you see these two running pods for ADOT Collector as [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) within the specified namespace, they are successfully deployed.

### Verify the Helm chart works as expected
- Run chart validation test and lint from`MakeFile`.
```console
$ cd datadog-exporter-for-eks-on-ec2
$ make install-tools # required initially
$ make all           # to run chart validation test and lint 
```

### Verify the metrics are sent to Datadog

- Open the Datadog webapp
- Navigate to the [Metrics > Summary page](https://app.datadoghq.com/metric/summary)
- Check that the [AWS Container Insights metrics](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/awscontainerinsightreceiver#available-metrics-and-resource-attributes) are reported. They may take a few minutes to come up.

## Configuration
To see all configurable options with detailed comments:

```console
$ helm show values [REPO_NAME]/datadog-exporter-for-eks-on-ec2
```

By changing values in `values.yaml`, you are able to customize the chart to use your preferred configuration.

Following options are some useful configurations that can be applied to this Helm chart.

### Deploy ADOT Collector as a Sidecar

A sidecar is a microservice design pattern where a companion service runs next to your primary microservice, augmenting its abilities or intercepting resources it is utilizing. The sidecar pattern would be the best fit for a single application monitoring.

In order to deploy the ADOT Collector in Sidecar mode using the Helm chart, 1) update `sidecar.yaml` and `values.yaml` files in the Helm chart with the application configurations and 2) include the use of `--set` flag in the `helm install` command from [Install Chart](#install-chart).

```console
$ helm install \
  [RELEASE_NAME] [REPO_NAME]/datadog-exporter-for-eks-on-ec2 \
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \
  --set adotCollector.daemonSet.enabled=false --set adotCollector.sidecar.enabled=true
```
The use of `--set` flag with `enabled=true` or `enabled=false` can switch on/off the specified deployment mode. The command set `enabled=false` for ADOT Collector as DaemonSet and `enabled=true` to deploy ADOT Collector as Sidecar.
You can also check whether your applications are successfully deployed by executing the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE                NAME                            READY   STATUS    RESTARTS   AGE
adot-sidecar-namespace   adot-sidecar-658dc9ffbb-w9zv2   2/2     Running   0          5m18s
```

### Deploy ADOT Collector as Deployment and StatefulSet

Deploying ADOT Collector as Deployment and StatefulSet mode requires installing ADOT Operator. See [OpenTelemetry Operator Helm Chart](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-operator) for detailed explanation.


### Deploy ADOT Collector with Prometheus Receiver for AWS Container Insights on EKS

Please refer to [deployment template](https://github.com/aws-observability/aws-otel-collector/blob/main/deployment-template/eks/otel-container-insights-prometheus.yaml) to deploy ADOT Collector with [Prometheus Receiver](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/d46048ac4dd01062c803867cb6a13377ea287a23/receiver/prometheusreceiver/README.md#prometheus-receiver) and [Datadog Exporter](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/datadogexporter) for AWS Container Insights on EKS 
via [configurations](https://github.com/aws-observability/aws-otel-collector/blob/main/config/eks/prometheus/config-all.yaml) in the Helm chart.

## Uninstall Chart

The following command uninstalls the chart. 
This will remove all the Kubernetes components associated with the chart and deletes the release.

```console
$ helm uninstall [RELEASE_NAME]
```

## Upgrade Chart

```console
$ helm upgrade [RELEASE_NAME] [REPO_NAME]/datadog-exporter-for-eks-on-ec2
```

## Contributing

See [CONTRIBUTING.md](../../CONTRIBUTING.md).

## Other useful links

[Using AWS Distro for OpenTelemetry](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html)

[AWS OpenTelemetry Collector Datadog exporter](https://aws-otel.github.io/docs/partners/datadog)

## License

Apache 2.0 License.

## Support

This is a supported Datadog product. Please [reach out](https://www.datadoghq.com/support/) if you need help.
