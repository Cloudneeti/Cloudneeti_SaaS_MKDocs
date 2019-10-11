Remediation of AWS Resources
----------------------------

Resources that are non-compliant to a security policy can be put into a
compliant state through Remediation. Remediation is accomplished by instructing
Security Policy to update the configuration of your existing resources and is
effective for new resource deployment. This article shows the steps needed to
understand and accomplish remediation with Cloudneeti Remediation.The following
steps are required to onboard Microsoft Azure to the Cloudneeti application.

![Remediation of AWS Resources](.././images/cloudneetiRemediation/AWS_Remediation1.png#thumbnail_1)

1.  **Deploy the Cloudneeti remediation framework on AWS account** includes
    provisioning remediation framework. Cloudneeti remediation framework has
    in-built capability to automatically remediate the non-compliant resources.
    Once remediation policies are enabled in Cloudneeti, the new resources get
    automatically remediated in near real time

2.  **Configure Cloud Account for remediation** includes enabling Cloud Account
    remediation and linking Cloudneeti Remediation framework deployed AWS
    account.

3.  **Configure Policy Remediation** includes configuring security policies in
    the respective cloud account.

4.  **Initiate Remediation** includes triggering remediation feature to
    remediate resources that are non-compliant to a security policy.

5.  **Verify results** includes verifying policy result post next data scan on
    Cloudneeti.

       | **Step**                                                       | **Product**     | **Role**              |
|------------------------------------------------------------|-------------|-------------------|
| Deploy the Cloudneeti remediation framework on AWS account | AWS Console | AWS Account Admin |
| Configure Cloud Account for remediation                    | Cloudneeti  | License Admin     |
| Configure Policy Remediation                               | Cloudneeti  | License Admin     |
| Initiate Remediation                                       | Cloudneeti  | License Admin     |
| Verify results                                             | Cloudneeti  | License Admin     |

Required Roles
--------------

One or more people with the following roles are required to complete Microsoft
Azure onboarding process.

 | **Pre-requisite Role** | **Portal to use**      |
|------------------------|------------------------|
| License Admin          | Cloudneeti application |
| AWS Account Admin      | AWS Console            |

-   Enabling policy remediation and initializing remediation requires the logged
    in user to be assigned the **License Admin** role in the Cloudneeti
    application.

-   The Microsoft Azure **AWS Account Admin** role is required for provision
    required roles, remediation framework.

Required Permissions
--------------------

Cloudneeti AWS account requires the invoke lambda function permission to invoke
the remediation actions.

The same role is used by the AWS account to be remediated on remediation
framework for auto-remediation purpose in multi account remediation.

Remediation framework will require the below role and actions to perform the
remediation action. This role will gets updated upon new remediation policy
additions in remediation framework.

| **Information**        | **Portal to use** | **Role**                       | **Step** |
|------------------------|-------------------|--------------------------------|----------|
| Cloudneeti AWS Account | AWS Console       | CN-Remediation-Invocation-Role | STEP 3   |
| Remediation Framework  | AWS Console       | CN-Auto-Remediation-Role       | STEP 1   |

Workstation Readiness
---------------------

| **Activity**                                        | **Description**                                                                                                                                                                             |
|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Workstation:** Install Bash                       | Either a Linux workstation or Windows Subsystem for Linux can be used.                                                                                                                      |
| **Workstation:** Install AWS Command Line Interface | To install AWS cli follow [link](https://docs.aws.amazon.com/cli/latest/userguide/install-windows.html) **AWS Command Line** Interface (CLI) is a unified tool to manage your AWS services. |
| **Workstation:** Install Nodejs                     | Download latest stable version of nodejs from [here](https://nodejs.org/en/) and install on the workstation.                                                                                |
| **Workstation:** Install serverless npm module      | Serverless Framework is a CLI tool to manage AWS deployments. Execute below command to install serverless module, \# npm install –g serverless                                              |


STEP 1: Provision Remediation framework on same or different AWS Account
------------------------------------------------------------------------

### 1.1 Local Account Remediation

1.  Open bash

2.  Clone the aws-remediation framework from the git

        git clone https://github.com/Cloudneeti/aws-auto-remediation

3.  Go to remediation framework repository

        cd aws-auto-remediation 

4.  Configure AWS account where remediation framework is to be deployed

        aws configure

5.  Deploy remediation framework in AWS account which need to be remediated.

        bash deploy-remediation-framework.sh -a <AWS-acount-id> -e <Cloudneeti-environment-prefix> -v 1.0 

    (-a) Account Id: 12-digit AWS account Id of the account where you want the remediation framework to be deployed

    (-e) Environment prefix: Enter any suitable prefix for your deployment

    (-v) Version: Enter the remediation framework version (Would be provided by Cloudneeti)


6.  Verify the Remediation framework deployment. Run the below commands to validate remediation
    framework deployments and necessary permissions.

        bash verify-remediation-setup.sh -a <12-digit-account-id> -e <environment-prefix> 
    
    (-a) Account Id: 12-digit AWS account Id of the account for which you want to verify if remediation framework is deployed or not.
    
    (-e) Environment prefix: Enter any suitable prefix for your deployment


### 1.2 (Optional) Multi Account Remediation

1.  Open bash

2.  Clone the aws-remediation framework from the git

        git clone https://github.com/Cloudneeti/aws-auto-remediation  

3. Go to remediation framework repository

        cd aws-auto-remediation 

4. Configure remediation framework

    a. Configure AWS account where remediation framework is to be deployed, using below command.
    
        aws configure 

    b. Deploy remediation framework in AWS account which will be used as the remediator in multi-mode: 

    (Optional if you have provisioned the remediation framework earlier)

        bash deploy-remediation-framework.sh -a <12-digit-account-id> -e <environment-prefix> -v <1.0>

    (-a) Account Id: 12-digit AWS account Id of the account where you want the remediation framework to be deployed

    (-e) Environment prefix: Enter any suitable prefix for your deployment
    
    (-v) Version: Enter the remediation framework version (Would be provided by Cloudneeti)
 
    
    c. Update the role in the account with the remediation framework so that it is aware of the new account that is/ will be added for remediation. (Make sure you have configured the account with the       remediation framework until this step using “aws configure”)

        bash update-remediation-role.sh -r <12-digit-account-id> -a <12-digit-account-id>

    (-r) Remediation Account Id: 12-digit AWS account Id of the account where the remediation framework is deployed

    (-a) New AWS Account Id: 12-digit AWS Account Id of the account which is newly added to use the remediation framework

5.  Configure AWS account to be remediated

    a.  Go to multi-mode remediation framework repository

        cd aws-auto-remediation\multi-mode-remediation

    b.  Execute below command to configure your aws account where remediation is to be enabled: (Enter the requested values)

        aws configure

    c.  Deploy automatic remediation invoker resources and associated role on AWS account which is to be remediated.

        bash configure-multi-mode-remediation.sh -a <12-digit-account-id> -r <12-digit-account-id> [-e <environment-prefix> -v <1.0>]

    
    (-a) Account Id: 12-digit AWS account Id of the account for which you want to enable the remediation
    
    (-r) Remediation Account Id: 12-digit AWS account Id of the account where the remediation framework is deployed.
    
    (-e) Environment prefix: Enter any suitable prefix for your deployment
    
    (-v) Version: Enter the remediation framework version (Would be provided by Cloudneeti)



6.  Verify the Remediation framework deployment. After deployment of remediation
    invoker resources on AWS account which is to be remediate, run the below
    commands to validate remediation invoker resources along with necessary
    permissions along with remediation framework.

        bash verify-multi-mode-remediation-setup.sh -a <12-digit-account-id> -r <12-digit-account-id>] -e <environment-prefix>


    (-a) New AWS Account Id: 12-digit AWS Account Id of the account which is newly added to use the remediation framework. 

    (-r) Remediation Account Id: 12-digit AWS account Id of the account where the remediation framework is deployed

    (-e) Environment prefix: Enter any suitable prefix for your deployment
 


STEP 2: Configure Cloud Account for remediation 
------------------------------------------------

Login to Cloudneeti portal with **License Admin** role

1.  Select desired **License** (1) and **Cloud Account** (2)

2.  Click **Configure**

3.  Select **Configure Account Remediation**

    ![STEP 2: Configure Cloud Account for remediation ](.././images/cloudneetiRemediation/AWS_RemediationStep2.1.png#thumbnail_1)

4. **Enable** remediation (1)

5. Enter the AWS account id (2)

    This can be same as AWS account or different account id where Cloudneeti remediation framework is deployed.

6. **Save** (3)

    This will validate Cloudneeti Remediation framework on given AWS account and enable remediation on success.

    ![STEP 2: Configure Cloud Account for remediation ](.././images/cloudneetiRemediation/AWS_RemediationStep2.2.png#thumbnail_1)

**Note:**

In case of invalid remediation account id, missing remediation framework or
roles on the provided remediation account id, Cloudneeti displays the error
message.

Configured email ids will receive an email with details and steps for fixing
the issues, once fixed please repeat above steps to enable* [Cloud Account
Remediation](#_STEP_2:_Configure).

STEP 3: Configure policy remediation 
-------------------------------------

Login to Cloudneeti portal with **License Admin** role

1. Select desired **License** (1) and **Cloud Account** (2)

2. Click **Configure**

3. Select **Configure Security Policies**

    ![STEP 3: Configure policy remediation ](.././images/cloudneetiRemediation/AWS_RemediationStep3.1.png#thumbnail_1)

4. Select **Remediation available** in filter (1)

5. **Enable** policies for remediation (2) (3)

6. Click **Save (4)**

    ![STEP 3: Configure policy remediation ](.././images/cloudneetiRemediation/AWS_RemediationStep3.2.png#thumbnail_1)

7. Confirm by hitting “Yes Please” on confirmation message box.

    ![STEP 3: Configure policy remediation ](.././images/cloudneetiRemediation/AWS_RemediationStep3.3.png#thumbnail_1)


STEP 4: Remediate non-compliant resources
-----------------------------------------

Cloudneeti supports two ways to remediate non-compliant resources.

**Manually Triggered [for existing resources]** - To remediate all the existing
non-compliant resources, follow below procedure.

**Auto Remediation [for new resources] -** Cloudneeti remediation framework has
in-built capability to automatically remediate the non-compliant resources. Once
the remediation policies are enabled in Cloudneeti, the new resources get
automatically remediated in near real time.

### Manually Triggered [for existing resources]

Login to Cloudneeti portal with **License Admin** role

1.  Select desired **License** (1) and **Cloud Account** (2)

2.  Click **Cloud Security Best Practices** (3)

3.  Click **Remediate** (4)

    ![STEP 4: Remediate non-compliant resources ](.././images/cloudneetiRemediation/AWS_RemediationStep4.1.png#thumbnail_1)

4. All remediation enabled policies will appear. If the selection checkbox is
    disable state please check all prerequisites are met.

5. Select desired **policy/policies** (1)

6. Click **Remediate Now** (2)

    ![STEP 4: Remediate non-compliant resources ](.././images/cloudneetiRemediation/AWS_RemediationStep4.2.png#thumbnail_1)

7. Remediation will change the configuration for resources to make them compliant with the policy with deploy if not exist.

    ![STEP 4: Remediate non-compliant resources ](.././images/cloudneetiRemediation/AWS_RemediationStep4.3.png#thumbnail_1)

STEP 5: Verify Resource Remediation
-----------------------------------

On remediation of non-compliant resources using manually triggered or
auto-remediation will not show the updated resource configuration in Cloudneeti
immediately. Verification of latest resource configurations can be done after
next scan in the Cloudneeti portal. To get the latest configuration perform an
on-demand re-scan the in Cloudneeti portal using below steps,

Login to Cloudneeti portal with **License Admin** role

1. Select desired **License** (1) and **Cloud Account** (2)

2. Click **Configure** (3)

3. Select **re-scan (4)**

    ![STEP 5: Verify Resource Remediation](.././images/cloudneetiRemediation/AWS_RemediationStep5.1.png#thumbnail_1)

4. It will take a few minutes to scan and process data. On Dashboards **Last Scanned On** will be updated once data scan and processing done.

5. Verify data on **Dashboard**

    ![STEP 5: Verify Resource Remediation](.././images/cloudneetiRemediation/AWS_RemediationStep5.2.png#thumbnail_1)

Decommission of remediation
---------------------------

To decommission the remediation functionality in Azure subscription,

**Disable all the remediation policies** - Azure account security policy
remediation functionality can be disabled from Cloudneeti portal. On disabling
the remediation policies stops the auto-remediation of new resources whenever
deployed.

### Disable Remediation policies

Login to Cloudneeti portal with **License Admin** role

1.  Select desired License and Account

2.  Click **Configure**

3. Select **Configure security policies**

    ![Decommission of remediation](.././images/cloudneetiRemediation/AWS_RemediationStep5.3.png#thumbnail_1)

4. Select **Remediation Available** in filter to see only the policies with
    remediation available. (1)

5. Disable the policy remediation (2)

6. Click **Save** (3)

    ![Decommission of remediation](.././images/cloudneetiRemediation/AWS_RemediationStep5.4.png#thumbnail_1)

**Note:**

-   The resource configuration updated during remediation will remain the same
    after disabling the remediation policies.

### Disable AWS account Remediation

1.  Select desired **License** (1) and **Cloud Account** (2)

2.  Click **Configure**

3.  Select **Configure Account Remediation**

    ![Disable AWS account Remediation](.././images/cloudneetiRemediation/AWS_RemediationStep5.5.png#thumbnail_1)

4. Disable Remediation (1)

5. **Save** (2)

6. After successful change in the configuration policies enabled earlier will get deleted.
  In case remediation policies are to be enabled again then policies
  remediation needs to be reconfigured.

### Decommission Remediation framework

Decommission of remediation framework take place in two phases.

#### Remove remediation configuration of multi-account remediation

1.  Open bash

2.  Clone the aws-remediation framework from the git

        git clone https://github.com/Cloudneeti/aws-auto-remediation

3.  Go to remediation framework repository

        cd aws-auto-remediation

4.	Remove remediation configuration of multi-account remediation

        bash decommission-multi-mode-remediation.sh -a <12-digit-account-id> -e <environment-prefix>

(-a) Account Id: 12-digit AWS account Id of the account for which you want to disable remediation 
(-e) Environment prefix: Enter any suitable prefix for your deployment
 


### Remove remediation framework

1.  Open bash

2.  Clone the aws-remediation framework from the git

        git clone https://github.com/Cloudneeti/aws-auto-remediation

3.  Go to remediation framework repository

        cd aws-auto-remediation 

4.  Deploy remediation framework in AWS account which need to be remediated.

        bash decommission-remediation-framework.sh [-a <12-digit-account-id>] [-e <environment-prefix>]

    (-a) Account Id: 12-digit AWS account Id of the account where the remediation framework is deployed
    (-e) Environment prefix: Enter any suitable prefix for your deployment



Upgrade of Remediation framework
--------------------------------

Cloudneeti periodically releases new sets of remediation policies to the customer. To use the latest remediation policies, customer needs to upgrade their AWS remediation framework. Cloudneeti will notify customers whenever new version of remediation framework gets release.
To upgrade the remediation framework, you need to use the deployment commands as it is with the same input parameters.


Cloudneeti Remediation Framework
--------------------------------

### Local Account Remediation

The remediation framework uses CloudTrail, CloudWatch log group, the remediation lambda functions, and the appropriate IAM roles.

![Local Account Remediation](.././images/cloudneetiRemediation/AWS_RemediationLocal1.png#thumbnail_1)

1. AWS account administrator creates/updates/reconfigure resources in aws account

2. CloudTrail and CloudWatch logs group collects the events occurred in AWS account

3. CloudWatch triggered the auto-remediation invoker in near real-time

4. Auto-remediation invoker lambda calls the appropriate remediation functions present in the remediation framework

5. Remediation functions setup required security configuration on the resources

### Multi Account Remediation

![Multi Account Remediation](.././images/cloudneetiRemediation/AWS_RemediationMulti.png#thumbnail_1)