

# Sending EKS cluster on EC2 metrics to CloudWatch and Amazon Managed Prometheus(AMP)

## Install Chart

The helm chart can be installed in 3 configurations. This allows the helm chart to be flexible based on your needs. The options are sending metrics AMP only, metrics and logs to CloudWatch only or sending the data to both the platforms. This can be configured based on the user's needs. You can also turn off the fluentbit deployment if you do not want to collect logs. All the deployment modes are explained below. 

### Default Mode

 By default, the helm chart is configured to send metrics data to the AMP workspace only. You need update the `values.yaml` file to add the Remote Write Endpoint URL and the region for your AMP workspace. By default, the region is set to `us-west-2`.

```console
helm install \  
  [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \   
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION]
```
`CLUSTER_NAME` and `AWS_REGION` must be specified with your own EKS cluster and the region. You can find these values by executing following command.

```console 
$ kubectl config current-context

[IAM_User_Name]@[CLUSTER_NAME].[AWS_REGION].eksctl.io
```

To verify the installation is successful, you can execute the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE         NAME                              READY      STATUS         RESTARTS        AGE
amazon-metrics    adot-collector-daemonset-jlhvs    1/1        Running        0               5s
amazon-metrics    adot-collector-daemonset-ph7xs    1/1        Running        0               5s 
```
If you see these two running pods, two for ADOT Collector as [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) within the specified
namespaces, they are successfully deployed.


### Sending metrics and logs to CloudWatch Container Insights only

To send metrics and logs to CloudWatch, you need to disable the AMP pipeline and enable the CloudWatch pipeline. You can either update the `values.yaml` to change the values of the following variable and then run the `helm install` command as above. Or you can use `--set` flag while installing the helm chart as shown below.

```console
helm install \  
  [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \   
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \
  --set adotCollector.daemonSet.ampEnabled=false \
  --set adotCollector.daemonSet.cwEnabled=true \
  --set fluentbit.enabled=true
```

To verify the installation is successful, you can execute the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE           NAME                              READY      STATUS         RESTARTS        AGE
amazon-metrics      adot-collector-daemonset-jlhvs    1/1        Running        0               5s
amazon-metrics      adot-collector-daemonset-ph7xs    1/1        Running        0               5s 
amazon-cloudwatch   fluent-bit-16rrn                  1/1        Running        0               5s
amazon-cloudwatch   fluent-bit-ksj6f                  1/1        Running        0               5s
```
If you see these four running pods, two for ADOT Collector and two for Fluent Bit as [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) within the specified
namespaces, they are successfully deployed.


### Sending metrics to both CloudWatch and AMP

To send metrics and logs to CloudWatch, you need to enable the CloudWatch pipeline. You can either update the `values.yaml` to change the values of the following variable and then run the `helm install` command as above. Or you can use `--set` flag while installing the helm chart as shown below.

```console
helm install \  
  [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \   
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \
  --set adotCollector.daemonSet.cwEnabled=true \
  --set fluentbit.enabled=true
```

To verify the installation is successful, you can execute the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE           NAME                              READY      STATUS         RESTARTS        AGE
amazon-metrics      adot-collector-daemonset-jlhvs    1/1        Running        0               5s
amazon-metrics      adot-collector-daemonset-ph7xs    1/1        Running        0               5s 
amazon-cloudwatch   fluent-bit-16rrn                  1/1        Running        0               5s
amazon-cloudwatch   fluent-bit-ksj6f                  1/1        Running        0               5s
```
If you see these four running pods, two for ADOT Collector and two for Fluent Bit as [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) within the specified
namespaces, they are successfully deployed.


## Verify the Helm chart works as expected

Run chart validation test and lint from MakeFile.

```console
$ cd adot-exporter-for-eks-on-ec2
$ make install-tools # required initially
$ make all           # to run chart validation test and lint 
```

## Verify the metrics and logs are sent to Amazon CloudWatch

* Open Amazon CloudWatch console
* Select "Logs → Log groups" on the left navigation bar.
* Check if following four log groups exist (performance log group will take longer than others).


```console
/aws/containerinsights/[CLUSTER_NAME]/application
/aws/containerinsights/[CLUSTER_NAME]/dataplane
/aws/containerinsights/[CLUSTER_NAME]/host
/aws/containerinsights/[CLUSTER_NAME]/performance
```

![alt text](charts/adot-exporter-for-eks-on-ec2/documentation/screenshots/Cloudwatch_dash.png)


## Verify the metrics are sent to Amazon Managed Prometheus(AMP)

Connect to Amazon Managed Grafana to visualize metrics stored in AMP. Please see the following [document](https://docs.aws.amazon.com/grafana/latest/userguide/AMP-adding-AWS-config.html) to setup the Grafana environment.


![alt text](charts/adot-exporter-for-eks-on-ec2/documentation/screenshots/Amp_dash.png)
