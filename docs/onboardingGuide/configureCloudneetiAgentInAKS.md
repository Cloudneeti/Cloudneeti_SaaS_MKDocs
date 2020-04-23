STEP 8: Configuring Cloudneeti agent in Azure Kubernetes Service (AKS)
======================================================================

**This step is optional.**

Cloudneeti includes and extends Azure Security center recommendations for AKS by deploying a Cloudneeti agent to Azure Kubernetes Cluster. A docker container agent is deployed to collect data for additional security policies. Cloudneeti then provides out-of-box mappings for all 13+ compliance frameworks included in the product. 

Deploying Cloudneeti agent on Azure Kubernetes Service enables compliance monitoring of Kubernetes cluster for security policies [listed here](../../onboardingGuide/configureCloudneetiAgentInAKS/#kubernetes-policy-list).


Prerequisites
-------------

| **Activity**                                                                                                               | **Description**                                                              |
|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.	Download and review **yaml** files for configuration of Cloudneeti Agent | The yaml files are used to configure Cloudneeti Agent in Azure Kubernetes Cluster:<br>  [cloudneeti-namespace.yaml](https://github.com/Cloudneeti/docs_cloudneeti/blob/master/scripts/kubernetes-onboarding/cloudneeti-namespace.yaml){target=_blank}<br>[cloudneeti-agent-config.yaml](https://github.com/Cloudneeti/docs_cloudneeti/blob/master/scripts/kubernetes-onboarding/cloudneeti-agent-config.yaml){target=_blank}<br>[cloudneeti-agent-secret.yaml](https://github.com/Cloudneeti/docs_cloudneeti/blob/master/scripts/kubernetes-onboarding/cloudneeti-agent-secret.yaml){target=_blank}<br>[cloudneeti-agent.yaml](https://github.com/Cloudneeti/docs_cloudneeti/blob/master/scripts/kubernetes-onboarding/cloudneeti-agent.yaml){target=_blank} (In case of AKS-engine) <br>[cloudneeti-agent-worker.yaml](https://github.com/Cloudneeti/docs_cloudneeti/blob/master/scripts/kubernetes-onboarding/cloudneeti-agent-worker.yaml){target=_blank} (In case of AKS) |
| 2.	**Workstation**: Ensure you have the latest PowerShell version (v5 and above) | Verify PowerShell version by running the following command<br>`$PSVersionTable.PSVersion`<br>on the workstation where you will run the ServicePrincipal creation script. If PowerShell version is lower than 5, then follow this link for installation of a later version: [Download Link.](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-windows-powershell?view=powershell-6){target=_blank} |
| 3.	**Workstation:** Before executing the script, make sure there are no restrictions in running the PowerShell script  | Use this PowerShell command:<br>``Set-ExecutionPolicy ` ``<br>``-Scope Process ` ``<br>``-ExecutionPolicy Bypass``<br>PowerShell contains built-in execution policies that limit its use as an attack vector. By default, the execution policy is set to Restricted, which is the primary policy for script execution. The bypass allows for running scripts and keeps the lowered permissions isolated to just the current running process. |                                                     
| 4. **Workstation:** Azure CLI version 2.0.46 | Please follow [link](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest){target=_blank} to install Azure CLI version 2.0.46       |
| 5. **Workstation:** Install and set up kubectl to execute PowerShell commands within Cloudneeti Agent configuration script | Please follow [link](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows){target=_blank} to install and set up **kubectl** <br>``choco install kubernetes-cli``<br>      |
| 6. **Workstation:** Install OpenSSH | Please follow [link](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse){target=_blank} to install and set up **OpenSSH**   |

STEP 1: Associate Kubernetes cluster with Cloud account in Cloudneeti
---------------------------------------------------------------------

Login to Cloudneeti portal with **License Admin** role

1.  Navigate to **Configurations** and **Cloud Accounts**

    ![Associate Kubernetes](.././images/kubernetes/CN_ManageAccounts_1.png#thumbnail)

2.  Expand Azure (1) section

3.  Click **Configure Accounts** (2) for the Cloud account where Kubernetes Cluster
    is to be associated.

3.  Click **Manage K8s Clusters** (3)

    ![Associate Kubernetes](.././images/kubernetes/CN_ManageK8Cluster_2.png#thumbnail)

4.  Add **Kubernetes Cluster Name**

5.  **Save**

    ![Associate Kubernetes](.././images/kubernetes/CN_AssociateK8Cluster_3.png#thumbnail)

6.  It will download a JSON file **cloudneeti-agent-config** which will be used in
    step 2 to update agent configuration script.

    ![Associate Kubernetes](.././images/kubernetes/CN_ConfigFile_4.png#thumbnail)

Sample JSON file

        {"LicenseId":"XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX","AccountId":"XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX","ClusterName":"K8 Cluster Demo","ClientId":"XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"}

STEP 2: Deploy Cloudneeti agent
-------------------------------

Please use below steps to deploy Cloudneeti Agent on AKS, AKS engine.

### AKS

#### 2.1 Update agent configuration scripts

- **cloudneeti-namespace.yaml** metadata section with value for namespace name.

                metadata:
                    name: <Namespace>
    
- **cloudneeti-agent-config.yaml** data section with values **cloudneeti-agent-config** downloaded in [STEP 1.](../../onboardingGuide/configureCloudneetiAgentInAKS/#step-1-associate-kubernetes-cluster-with-cloud-account-in-cloudneeti)

                
                data:
                    clusterName: "<uniqueclustername>"
                    licenseId: "<cloudneetilicenseid>"
                    accountId: "<cloudneetiaccountid>"
                    cloudneetiEnvironment: "<prod/trial>"

-  **cloudneeti-agent-secret.yaml** set the below values.
    -  **namespace** as given in cloudneeti-namespace.yaml.
    -  **cloudneetiAPIKey** Please follow [steps](../../onboardingGuide/configureCloudneetiAgentInAKS/#set-api-key-in-base64) to generate the key and set the key in base64 format.
    - **cloudneetiAPIAppSecret** Please follow [steps](../../administratorGuide/configureCloudneetiAPIAccess/#step-1-create-cloudneeti-api-application)

                metadata:
                    name: cloudneeti-agent
                    namespace: <Namespace>
                data:
                    cloudneetiAPIKey: <cloudneetiapikey>

-  **cloudneeti-agent-worker.yaml** update value for **schedule** in spec section, set cron job schedule as per your requirement.

                spec:
                    schedule: "0 12 * * *"
                

Note: The default value is set to scan the cluster every day at 12PM. It is recommended to set the execution time of Cloudneeti agent once a day.

#### 2.2 Access Kubernetes cluster with root account from local machine

        az aks get-credentials --name <cluster-name> --resource-group <cluster-resource-group> --overwrite-existing

#### 2.3 Deploy Cloudneeti agent on Kubernetes cluster node

1.  Create/copy below files on Kubernets master 
    - cloudneeti-namespace.yaml
    - cloudneeti-agent-config.yaml
    - cloudneeti-agent-secret.yaml
    - cloudneeti-agent-worker.yaml

    ![Associate Kubernetes](.././images/kubernetes/Master_1.png#thumbnail)
    
2. Create a Cloudneeti namespace 

        kubectl apply -f cloudneeti-namespace.yaml

3.  Create Cloudneeti agent secret

        kubectl apply -f cloudneeti-agent-secret.yaml --namespace <namespace name>

4.  Create Cloudneeti agent config

        kubectl apply -f cloudneeti-agent-config.yaml --namespace <namespace name>

5.  Deploy Cloudneeti agent

        kubectl apply -f cloudneeti-agent-worker.yaml --namespace <namespace name>


### AKS Engine

#### 2.1 Update agent configuration scripts

- **cloudneeti-namespace.yaml** metadata section with value for namespace name.

                metadata:
                    name: <Namespace>
    
- **cloudneeti-agent-config.yaml** data section with values **cloudneeti-agent-config** downloaded in [STEP 1.](../../onboardingGuide/configureCloudneetiAgentInAKS/#step-1-associate-kubernetes-cluster-with-cloud-account-in-cloudneeti)

                
                data:
                    clusterName: "<uniqueclustername>"
                    licenseId: "<cloudneetilicenseid>"
                    accountId: "<cloudneetiaccountid>"
                    cloudneetiEnvironment: "<prod/trial>"

-  **cloudneeti-agent-secret.yaml** set the below values.
    -  **namespace** as given in cloudneeti-namespace.yaml.
    -  **cloudneetiAPIKey** Please follow [steps](../../onboardingGuide/configureCloudneetiAgentInAKS/#set-api-key-in-base64) to generate the key and set the key in base64 format.
    -  **cloudneetiAPIAppSecret** Please follow [steps](../../administratorGuide/configureCloudneetiAPIAccess/#step-1-create-cloudneeti-api-application) to configure API access

                metadata:
                    name: cloudneeti-agent
                    namespace: <Namespace>
                data:
                    cloudneetiAPIKey: <cloudneetiapikey>
                    cloudneetiAPIAppSecret: <cloudneetiapiappsecret>

-  **cloudneeti-agent.yaml** update value for **schedule** in spec section, set cron job schedule as per your requirement.

                spec:
                    schedule: "0 12 * * *"
                

Note: The default value is set to scan the cluster every day at 12PM. It is recommended set the execution time of Cloudneeti agent once a day.

#### 2.2 Access Kubernetes cluster with root account

1. Access the AKS-Engine cluster by using the kubeconfig generated for the cluster at the time of provisioning aks-engine in below directory.

        	_output/<ResourceGroupName>/kubeconfig/

    

2. Copy kubeconfig. *region* .json file on local/dev machine at secure place.

    aks-engine generates kubeconfig files for each possible region (for eastus location refer kubeconfig.eastus.json file)

3. Verify K8S cluster access

     ![Verify access to Kubernetes](.././images/kubernetes/K8_engine.png#thumbnail)


#### 2.3 Deploy Cloudneeti agent on Kubernetes cluster node

1.  Create/copy below files on Kubernets master 
    - cloudneeti-namespace.yaml
    - cloudneeti-agent-config.yaml
    - cloudneeti-agent-secret.yaml
    - cloudneeti-agent.yaml

    ![Associate Kubernetes](.././images/kubernetes/Master_1.png#thumbnail)
    
2. Create a Cloudneeti namespace 

        kubectl apply -f cloudneeti-namespace.yaml

3.  Create Cloudneeti agent secret

        kubectl apply -f cloudneeti-agent-secret.yaml --namespace <namespace name>

4.  Create Cloudneeti agent config

        kubectl apply -f cloudneeti-agent-config.yaml --namespace <namespace name>

5.  Deploy Cloudneeti agent

        kubectl apply -f cloudneeti-agent.yaml --namespace <namespace name>

    ![Associate Kubernetes](.././images/kubernetes/Master_2.png#thumbnail)


STEP 3: Verify Cloudneeti agent installation
--------------------------------------------

Verify Cloudneeti agent installation using Kubernetes dashboard. Please follow [link](https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard#start-the-kubernetes-dashboard){target=_blank}

1. Verify namespace created

    ![Associate Kubernetes](.././images/kubernetes/Verify_1.png#thumbnail)

2. Navigate to the cron jobs

    ![Associate Kubernetes](.././images/kubernetes/Verify_2.png#thumbnail)

3. Select a latest job 

    ![Associate Kubernetes](.././images/kubernetes/Verify_3.png#thumbnail)

4. Check if Cloudneeti agent has sent the data successfully.

    ![Associate Kubernetes](.././images/kubernetes/Verify_4.png#thumbnail)    


STEP 4: Verify policy results
-----------------------------

Login to Cloudneeti portal with **License Admin** role

1. Navigate to CIS Kubernetes v1.5.0 benchmark

    ![Associate Kubernetes](.././images/kubernetes/CN_K8_Verify_1.png#thumbnail)

2. On successful agent configuration, policy results will appear on Cloudneeti portal

    ![Associate Kubernetes](.././images/kubernetes/CN_K8_Verify_2.png#thumbnail)


## Appendix

### Kubernetes policy list

The following CIS Kubernetes policies get enabled due to Cloudneeti Kubernetes agent configuration.

| **Category** | **Policy Title** |
        | --- | --- |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the API server pod specification file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the API server pod specification file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the controller manager pod specification file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the controller manager pod specification file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the scheduler pod specification file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the scheduler pod specification file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the etcd pod specification file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the etcd pod specification file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the etcd data directory permissions are set to 700 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the etcd data directory ownership is set to etcd:etcd |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the admin.conf file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the admin.conf file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the scheduler.conf file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the scheduler.conf file ownership is set to root:root |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the controller-manager.conf file permissions are set to 644 or more restrictive |
        | Kubernetes - Control Plane Components - Master Node Configuration Files | Ensure that the controller-manager.conf file ownership is set to root:root |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --basic-auth-file argument is not set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --token-auth-file argument is not set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --kubelet-https argument is set to true |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --kubelet-client-certificate and --kubelet-client-key arguments are set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --kubelet-certificate-authority argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --authorization-mode argument is not set to AlwaysAllow |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --authorization-mode argument includes Node |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --authorization-mode argument includes RBAC |
        | Kubernetes - Control Plane Components - API Server | Ensure that the admission control plugin AlwaysAdmit is not set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the admission control plugin ServiceAccount is set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the admission control plugin NamespaceLifecycle is set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the admission control plugin PodSecurityPolicy is set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the admission control plugin NodeRestriction is set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --insecure-bind-address argument is not set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --insecure-port argument is set to 0 |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --secure-port argument is not set to 0 |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --profiling argument is set to false |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --request-timeout argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --service-account-lookup argument is set to true |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --service-account-key-file argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --etcd-certfile and --etcd-keyfile arguments are set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --tls-cert-file and --tls-private-key-file arguments are set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --client-ca-file argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --etcd-cafile argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --encryption-provider-config argument is set as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --audit-log-path argument is set |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --audit-log-maxage argument is set to 30 or as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --audit-log-maxbackup argument is set to 10 or as appropriate |
        | Kubernetes - Control Plane Components - API Server | Ensure that the --audit-log-maxsize argument is set to 100 or as appropriate |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --terminated-pod-gc-threshold argument is set as appropriate |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --profiling argument is set to false |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --use-service-account-credentials argument is set to true |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --service-account-private-key-file argument is set as appropriate |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --root-ca-file argument is set as appropriate |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the RotateKubeletServerCertificate argument is set to true |
        | Kubernetes - Control Plane Components - Controller Manager | Ensure that the --bind-address argument is set to 127.0.0.1 |
        | Kubernetes - Control Plane Components - Scheduler | Ensure that the --profiling argument is set to false |
        | Kubernetes - Control Plane Components - Scheduler | Ensure that the --bind-address argument is set to 127.0.0.1 |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet service file has permissions of 644 or more restrictive |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet service file ownership is set to root:root |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the proxy kubeconfig file permissions are set to 644 or more restrictive |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the proxy kubeconfig file ownership is set to root:root |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet.conf file permissions are set to 644 or more restrictive |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet.conf file ownership is set to root:root |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the client certificate authorities file ownership is set to root:root |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet configuration file has permissions set to 644 or more restrictive |
        | Kubernetes - Worker Nodes - Worker Node Configuration Files | Ensure that the kubelet configuration file ownership is set to root:root |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --anonymous-auth argument is set to false |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --authorization-mode argument is not set to AlwaysAllow |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --client-ca-file argument is set as appropriate |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --read-only-port argument is set to 0 |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --streaming-connection-idle-timeout argument is not set to 0 |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --protect-kernel-defaults argument is set to true |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --make-iptables-util-chains argument is set to true |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --tls-cert-file and --tls-private-key-file arguments are set as appropriate |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the --rotate-certificates argument is not set to false |
        | Kubernetes - Worker Nodes - Kubelet | Ensure that the RotateKubeletServerCertificate argument is set to true |

### Generate Cloudneeti API key

#### Sign-up on Cloudneeti API portal.

1. Go to [API portal](https://portal.cloudneeti.com/){target=_blank} and Sign up.

2. Fill the required fields in the sign-up form

3. You will receive a confirmation mail for sign-up, Click on the confirmation
    link.

4. The confirmation link will ask you for change password (info: You can use
    the password your used when signing up)

5. You are signed up successfully

#### Retrieve and activate API key

Retrieve and activate your API key using the Cloudneeti API portal

1. Click on **PRODUCTS**
2. Select **Unlimited**
	![Cloudneeti API](.././images/onboardingOffice365Subscription/Cloudneeti_API.png#thumbnail)
3. Click on **Subscribe**
	![Subscribe](.././images/azureSubscriptions/API_Subscribe.png#thumbnail)

This will notify Cloudneeti to activate your API subscription access. Please
wait for the activation to be done. When Cloudneeti activates your subscription, you
will get an email notification.

Once you receive the confirmation, proceed with the following steps.

1. Click on **Username**
2. Select **Profile**
3. Click on **Show**
4. Copy the **Primary key** to your notepad.
	![Primary key](.././images/onboardingOffice365Subscription/Primary_key.png#thumbnail)

#### Set API key in base64

Below commands can be used to set API key in base64

##### Bash

        echo <apikey> | base64

##### Powershell

        $PlainTextKey = <apikey>
        $Bytes = [System.Text.Encoding]::Unicode.GetBytes($PlainTextKey)
        $EncodedKey =[Convert]::ToBase64String($Bytes)
        $EncodedKey
