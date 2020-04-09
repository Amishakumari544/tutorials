summary: Install Keptn
id: keptn-installation
categories: Tutorial
tags: keptn
status: Draft 
authors: Jürgen
Feedback Link: https://keptn.sh


# Keptn Installation


## Prerequisites
Duration: 5:00

- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- For Linux: [bc](https://www.gnu.org/software/bc/manual/html_mono/bc.html)

## Setup Kubernetes cluster
Duration: 10:00

We are going to setup a Kubernetes cluster.

<!-- AKS -->
environment:aks

Azure Kubernetes Service (AKS)

1. Install local tools
  - [az](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

1. Create AKS cluster
  - Master version >= `1.15.x` (tested version: `1.15.5`)
  - One **D8s_v3** node
 
### new section

general

<!-- EKS -->
environment:eks

Amazon Elastic Kubernetes Service (EKS)

1. Install local tools
  - [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) (version >= 1.16.156)

1. Create EKS cluster on AWS
  - version >= `1.13`, version >= `1.14` recommended (tested version: `1.14`)
  - One `m5.2xlarge` node
  - Sample script using [eksctl](https://eksctl.io/introduction/installation/) to create such a cluster

    ```console
    eksctl create cluster --version=1.14 --name=keptn-cluster --node-type=m5.2xlarge --nodes=1 --region=eu-west-3
    ```


Known bug in EKS 1.13:
 Please note that for EKS version `1.13` in our testing we learned that the default CoreDNS that comes with certain EKS versions has a bug. In order to solve that issue we can use eksctl to update the CoreDNS service like this: 
```console
eksctl utils update-coredns --name=keptn-cluster --region=eu-west-3 --approve
```


<!-- gke -->
environment:gke

Google Kubernetes Engine (GKE)

Run your Keptn installation for free on GKE!
If you [sign up for a Google Cloud account](https://console.cloud.google.com/getting-started), Google gives you an initial $300 credit. For deploying Keptn you can apply for an additional $200 credit which you can use towards that GKE cluster needed to run Keptn.

[Apply for your credit here](http://bit.ly/keptnongke)

1. Install local tools
  - [gcloud](https://cloud.google.com/sdk/gcloud/)
  - [python 2.7](https://www.python.org/downloads/release/python-2716/) (required for Ubuntu 19.04)

2. Create GKE cluster
  - Master version >= `1.14.x` (tested version: `1.14.10-gke.24`)
  - One **n1-standard-8** node
  - Image type `ubuntu` or `cos` (**Note:** If you plan to use Dynatrace monitoring, select `ubuntu` for a more [convenient setup](../../reference/monitoring/dynatrace/).)
  - Sample script to create such cluster:

    ```console
    // set environment variables
    PROJECT=nameofgcloudproject
    CLUSTER_NAME=nameofcluster
    ZONE=us-central1-a
    REGION=us-central1
    GKE_VERSION="1.14"
    ```

```console
gcloud container clusters create $CLUSTER_NAME --project $PROJECT --zone $ZONE --no-enable-basic-auth --cluster-version $GKE_VERSION --machine-type "n1-standard-8" --image-type "UBUNTU" --disk-type "pd-standard" --disk-size "100" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --num-nodes "1" --enable-stackdriver-kubernetes --no-enable-ip-alias --network "projects/$PROJECT/global/networks/default" --subnetwork "projects/$PROJECT/regions/$REGION/subnetworks/default" --addons HorizontalPodAutoscaling,HttpLoadBalancing --no-enable-autoupgrade
```

<!--
e nvironment:oc

OpenShift 3.11

1. Install local tools

  - [oc CLI - v3.11](https://github.com/openshift/origin/releases/tag/v3.11.0)


1. On the OpenShift master node, execute the following steps:

- Set up the required permissions for your user:

    ```console
oc adm policy --as system:admin add-cluster-role-to-user cluster-admin <OPENSHIFT_USER_NAME>
    ```

- Set up the required permissions for the installer pod:

    ```console
oc adm policy  add-cluster-role-to-user cluster-admin system:serviceaccount:default:default
oc adm policy  add-cluster-role-to-user cluster-admin system:serviceaccount:kube-system:default
    ```

- Enable admission WebHooks on your OpenShift master node:

    ```console
sudo -i
cp -n /etc/origin/master/master-config.yaml /etc/origin/master/master-config.yaml.backup
oc ex config patch /etc/origin/master/master-config.yaml --type=merge -p '{
    "admissionConfig": {
    "pluginConfig": {
        "ValidatingAdmissionWebhook": {
        "configuration": {
            "apiVersion": "apiserver.config.k8s.io/v1alpha1",
            "kind": "WebhookAdmission",
            "kubeConfigFile": "/dev/null"
        }
        },
        "MutatingAdmissionWebhook": {
        "configuration": {
            "apiVersion": "apiserver.config.k8s.io/v1alpha1",
            "kind": "WebhookAdmission",
            "kubeConfigFile": "/dev/null"
        }
        }
    }
    }
}' >/etc/origin/master/master-config.yaml.patched
if [ $? == 0 ]; then
    mv -f /etc/origin/master/master-config.yaml.patched /etc/origin/master/master-config.yaml
    /usr/local/bin/master-restart api && /usr/local/bin/master-restart controllers
else
    exit
fi
    ```

-->

<!-- pks -->
environment:pks

Pivotal Container Service (PKS)

1. Install local tools
  - [pks CLI - v1.0.4](https://docs.pivotal.io/runtimes/pks/1-4/installing-pks-cli.html)

1. Create PKS cluster on GCP
  - Use the provided instructions for [Enterprise Pivotal Container Service (Enterprise PKS) installation on GCP](https://docs.pivotal.io/runtimes/pks/1-4/gcp-index.html)

  - Create a PKS cluster by using the PKS CLI and executing the following command:

    ```console
    // set environment variables
    CLUSTER_NAME=name_of_cluster
    HOST_NAME=host_name
    PLAN=small
    ```

    ```console
    pks create-cluster $CLUSTER_NAME --external-hostname $HOST_NAME --plan $PLAN
    ```


<!-- EKS -->
environment:minikube

Minikube

1. Install Minikube in [version 1.2](https://github.com/kubernetes/minikube/releases/tag/v1.2.0) (newer versions do not work).

1. Setup a Minikube VM with at least 6 CPU cores and 12 GB memory using:

       ```console
    minikube stop # optional
    minikube delete # optional
    minikube start --cpus 6 --memory 12200
       ``` 

1. Start the Minikube LoadBalancer service in a second terminal by executing:

    ```console
   minikube tunnel 
   ``` 





environment:

## Download Keptn CLI
Duration: 2:00

Every release of Keptn provides binaries for the Keptn CLI. These binaries are available for Linux, macOS, and Windows.


1. Download the version for your operating system from [Download CLI](https://github.com/keptn/keptn/releases/tag/0.6.1)
1. Unpack the download
1. Find the `keptn` binary in the unpacked directory

  - *Linux / macOS*: Add executable permissions (``chmod +x keptn``), and move it to the desired destination (e.g. `mv keptn /usr/local/bin/keptn`)

  - *Windows*: Copy the executable to the desired folder and add the executable to your PATH environment variable.

1. Now, you should be able to run the Keptn CLI: 
    - Linux / macOS
      ```console
      keptn --help
      ```
    
    - Windows
      ```console
      .\keptn.exe --help
      ```

**Note:** For the rest of the documentation we will stick to the *Linux / macOS* version of the commands.


## Install Keptn in your cluster
Duration: 7:00

To install the latest release of Keptn on a Kuberntes cluster, execute the [keptn install](../../reference/cli/#keptn-install) command with the ``platform`` flag specifying the target platform you would like to install Keptn on. 



environment:aks

```console
keptn install --platform=aks
```
  
environment:eks

```console
keptn install --platform=eks
```
If you have a custom domain or cannot use *xip.io* (e.g., when running Keptn on EKS with an ELB (Elastic Load Balancer) from AWS), there is the CLI command [keptn configure domain](../../reference/cli/#keptn-configure-domain) to configure Keptn for your custom domain:

```console
keptn configure domain YOUR_DOMAIN
```

environment:gke

```console
keptn install --platform=gke
```

environment:oc

```console
keptn install --platform=openshift
```

environment:pks

```console
keptn install --platform=pks
```

environment:minikube

```console
keptn install --platform=kubernetes
```

### Installation details 

In the Kubernetes cluster, this command creates the **keptn**, **keptn-datastore**, and **istio-system** namespace. While istio-system contains all Istio related resources, keptn and keptn-datastore contain the complete infrastructure to run Keptn. 

### Troubleshooting

Please note that in case of any errors, the install process might leave some files in an inconsistent state. Therefore [keptn install](../../reference/cli/#keptn-install) cannot be executed a second time without [keptn uninstall](../../reference/cli/#keptn-uninstall). To address a unsuccessful installation: 

1. [Verify the Keptn installation](../../reference/troubleshooting#verifying-a-keptn-installation).

1. Uninstall Keptn by executing the [keptn uninstall](../../reference/cli#keptn-uninstall) command before conducting a re-installation.  
