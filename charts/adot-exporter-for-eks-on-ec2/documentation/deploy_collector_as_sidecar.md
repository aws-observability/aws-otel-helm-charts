

# Deploy ADOT Collector as a Sidecar

A sidecar is a microservice design pattern where a companion service runs next to your primary microservice, augmenting its abilities or intercepting resources it is utilizing. The sidecar pattern would be the best fit for a single application monitoring.

In order to deploy the ADOT Collector in Sidecar mode using the Helm chart, 1) update sidecar.yaml and values.yaml files in the Helm chart with the application configurations and 2) include the use of --set flag in the helm install command from [Install Chart](https://github.com/aws-observability/aws-otel-helm-charts/tree/main/charts/adot-exporter-for-eks-on-ec2#install-chart).
Please refer to the installation documentation to ensure your helm chart is configured to send metrics and logs to the platform of your choice.


```console
$ helm install \  
[RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \  
--set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \  
--set adotCollector.daemonSet.enabled=false \
--set adotCollector.sidecar.enabled=true
```
The use of `--set` flag with `enabled=true` or `enabled=false` can switch on/off the specified deployment mode. The command set enabled=false for ADOT Collector as DaemonSet and enabled=true to deploy ADOT Collector as Sidecar. You can also check whether your applications are successfully deployed by executing the following command.

```console

$ kubectl get pods --all-namespaces

NAMESPACE                   NAME                            READY   STATUS    RESTARTS   AGE
AGEadot-sidecar-namespace   adot-sidecar-658dc9ffbb-w9zv2   2/2     Running   0          18s
amazon-cloudwatch           fluent-bit-9dcql                1/1     Running   0          18s
amazon-cloudwatch           fluent-bit-wqhmd                1/1     Running   0          18s
```
