

# Offload EKS cluster (on EC2) metrics to Amazon CloudWatch and/or Amazon Managed Prometheus(AMP)

## Install Chart

The helm chart provides flexibility to offload mertrics into target platform(CloudWatch, AMP) of your choice. Here are the possible deployment options - 
* Offload metrics to Amazon Managed Prometheus(AMP)
* Offload metrics to Amazon CloudWatch 
* Offload metrics to both Amazon Managed Prometheus(AMP) and Amazon CloudWatch

The above-mentioned deployment modes are explained in-detail in the following sections. 

### Default Mode (Offload metrics to only Amazon Managed Prometheus)

 By default, the helm chart is configured to offload metrics data to the Amazon Managed Prometheus(AMP) workspace only. You need update the `values.yaml` file to add the Remote Write Endpoint URL and the region for your AMP workspace. By default, the region is set to `us-west-2`. By default, only the `prometheus` receiver and `prometheusremotewrite` exporter is included to be able to send metrics to AMP.

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


### Offload metrics to Amazon CloudWatch

To offload metrics to CloudWatch, you need to disable the AMP pipeline and enable the CloudWatch pipeline. You can either update the `values.yaml` to change the values of the following variable `exporters` and then run the `helm install` command as above. Only include the `awscontainerinsightreceiver` receiver and `awsemf` exporter to send metrics to CloudWatch Container Insights.

```console
helm install \  
  [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \   
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \
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


### Offload metrics to both CloudWatch and Amazon Managed Prometheus(AMP)

To send metrics to CloudWatch, you need to enable the CloudWatch pipeline. You can either update the `values.yaml` to change the values of the following variable `exporters` and then run the `helm install` command as above. Include both `prometheus`, `awscontainerinsightreceiver` as receivers, `awsemf`,`prometheusremotewrite` exporter to send metrics to both CloudWatch and AMP.

```console
helm install \  
  [RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \   
  --set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \
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

## Verify the metrics are sent to Amazon CloudWatch

* Open Amazon CloudWatch console
* Select "Logs → Log groups" on the left navigation bar.
* Check if following four log groups exist (performance log group will take longer than others).


```console
/aws/containerinsights/[CLUSTER_NAME]/application
/aws/containerinsights/[CLUSTER_NAME]/dataplane
/aws/containerinsights/[CLUSTER_NAME]/host
/aws/containerinsights/[CLUSTER_NAME]/performance
```

![alt text](screenshots/Cloudwatch_dash.png?raw=true "CW Metrics")


## Verify the metrics are sent to Amazon Managed Service for Prometheus(AMP)

Connect to Amazon Managed Service for Grafana to visualize metrics stored in AMP. Please see the following [document](https://docs.aws.amazon.com/grafana/latest/userguide/AMP-adding-AWS-config.html) to setup the Grafana environment.

![alt text](screenshots/Amp_dash.png?raw=true "Amp Metrics")

