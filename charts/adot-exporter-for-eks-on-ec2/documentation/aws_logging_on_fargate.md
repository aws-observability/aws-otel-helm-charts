# AWS EKS on Fargate

The prerequisites for [Fargate logging](https://docs.aws.amazon.com/eks/latest/userguide/fargate-logging.html) via Amazon EKS on [AWS Fargate](https://docs.aws.amazon.com/eks/latest/userguide/fargate.html) include: 1) [Create a Fargate profile for your cluster](https://docs.aws.amazon.com/eks/latest/userguide/fargate-getting-started.html#fargate-gs-create-profile) and 2) [Create a Fargate pod execution role](https://docs.aws.amazon.com/eks/latest/userguide/fargate-getting-started.html#fargate-sg-pod-execution-role).

Amazon EKS on Fargate features a Fluent Bit based built-in log router to send collected logs to various destinations, including Amazon CloudWatch. Fargate utilizes [AWS for Fluent Bit](https://github.com/aws/aws-for-fluent-bit), and the required configurations for Fargate to automatically detect and configure the log router are included in the Helm chart in configmap.yaml and values.yaml files based on the [Fargate logging](https://docs.aws.amazon.com/eks/latest/userguide/fargate-logging.html) user guide. The configurations in configmap.yaml must include the name: aws-logging and the namespace: aws-observability for Fargate logging. To deploy your application to Amazon EKS on Fargate, you need to include your application yaml file in the aws-fargate-logging folder of the Helm chart with the same namespace as your [AWS Fargate profile](https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html).

For more detailed information about Fargate logging, such as deployment of a sample-app.yaml or your application and the instructions to download, create, and attach IAM policy to the [pod execution role](https://docs.aws.amazon.com/eks/latest/userguide/pod-execution-role.html) for Fargate profile, please refer to the user guide for [Fargate logging](https://docs.aws.amazon.com/eks/latest/userguide/fargate-logging.html) and [Getting started with AWS Fargate using Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/fargate-logging.html).
This is an example of using the Helm chart for Fargate logging with the sample-app.yaml from [Fargate logging](https://docs.aws.amazon.com/eks/latest/userguide/fargate-logging.html).

```console
$ helm install \  
[RELEASE_NAME] [REPO_NAME]/adot-exporter-for-eks-on-ec2 \  
--set clusterName=[CLUSTER_NAME] --set awsRegion=[AWS_REGION] \  
--set fargateLogging.enabled=true
```

To confirm the `sample-app` is deployed and troubleshoot the logging is enabled/disabled, you can run the following commands.

```console
$ kubectl get pods --all-namespaces

NAMESPACE                  NAME                            READY   STATUS    RESTARTS   AGE
AGEaws-observability       sample-app-86b8cc866b-cr5x6     1/1     Running   0          13m
aws-observability          sample-app-86b8cc866b-q75z7     1/1     Running   0          13m
aws-observability          sample-app-86b8cc866b-t615c     1/1     Running   0          13m
```


```console
$ kubectl describe po -n aws-observability 

sample-app-86b8cc866b-cr5x6Events:  
Type    Reason          Age  From               Message   
----    ------          ---  ----               -------  
Normal  LoggingEnabled  13m  fargate-scheduler  Successfully enabled logging for pod
```