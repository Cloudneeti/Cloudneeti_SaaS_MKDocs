Cloudneeti offers an API to automate cloud account onboarding via this REST API. The following Cloud Providers are supported

- [Amazon Web Services (AWS)](../../userGuide/onboardCloudAccount/#aws){target=_blank} 
- [Microsoft Azure](../../userGuide/onboardCloudAccount/#azure){target=_blank} 
- [Microsoft Office365](../../userGuide/onboardCloudAccount/#office365){target=_blank} 

## AWS

### Prerequisistes
Please follow steps 1 to 3 in [page](../../onboardingGuide/amazonWebServiceAccounts/){target=_blank} to do prerequisite configurations.

        POST https://<CLOUDNEETI API DOMAIN>/onboarding/license/<LICENSE ID>/account/aws

| Environment	| Values for Cloudneeti API domain     |
|---------------|--------------------------------------|
| PROD 	        |   api.cloudneeti.com                 |
| TRIAL 	| trialapi.cloudneeti.com              |


### URI Parameters

| Parameter           |           Description                                |           Required/Optional  |
|-----------|----------------------------------------------------------------|----------------------------|
| license   |          [Cloudneeti License Id​](#license-id)                  | Required|

### Authorization
| Type           |           Description                                |           Required/Optional  |
|-----------|----------------------------------------------------------------|----------------------------|
| Bearer Token  |          [License Token](../../userGuide/tokenAPI/#license-token)                   | Required|

### Header

| Key	        | Value                                |
|---------------|--------------------------------------|
| Content-Type 	                |   application/json                 |
| Ocp-Apim-Subscription-Key 	| [Cloudneeti API key generated](../../administratorGuide/configureCloudneetiAPIAccess/)             |


### Request body

For prerequisites for AWS cloud account onboarding, please refer [Amazon Web Services (AWS) Account](../amazonWebServiceAccounts/)
        
        {
                "AccountName": "<ACCOUNT NAME>",
                "AWSAccountId": "<AWS ACCOUNT ID>",
                "AWSRoleName": "<AWS ROLE NAME>"
        }

### Response

| Name           |           Type       |          Description  |
|----------------|----------------------|-----------------------|
| accountId	 |           String     | Cloudneeti AWS cloud account id onboarded      |
| jobId  	 |           String     | Cloudneeti AWS cloud account scan job id      |
| statusCode	     |           Integer     | Cloudneeti API status code      |
| message |      String     | Response describing operation result as success or failed.     |

### Sample Response
        {
                "result": {
                        "accountId": "8457c2cd-b615-4c6e-8640-3b8491c89bfb",
                        "jobId": "2e9035d8-db07-4404-a09c-93f5344a8d9d"
                },
                "statusCode": 200,
                "message": "Request Successful"
        }



## Azure

### Prerequisistes
Please follow steps 1 to 8 in [page](../../onboardingGuide/azureSubscriptions/){target=_blank} to do prerequisite configurations.

        POST https://<CLOUDNEETI API DOMAIN>/onboarding/license/<LICENSE ID>/account/azure

        
| Environment	| Values for Cloudneeti API domain     |
|---------------|--------------------------------------|
| PROD 	        |   api.cloudneeti.com                 |
| TRIAL 	| trialapi.cloudneeti.com              |


### URI Parameters


| Parameter           |           Description                                |           Required/Optional  |
|-----------|----------------------------------------------------------------|----------------------------|
| license   |          [Cloudneeti License Id​](#license-id)                  | Required|

### Header

| Key	        | Value                                |
|---------------|--------------------------------------|
| Content-Type 	                |   application/json                 |
| Ocp-Apim-Subscription-Key 	| [Cloudneeti API key generated](../../administratorGuide/configureCloudneetiAPIAccess/)             |


### Request body

For prerequisites for Azure cloud account onboarding, please refer [Microsoft Azure Subscription](../azureSubscriptions/)

        {
                "AccountName": "<ACCOUNT NAME>",
                "DomainName": "<ACTIVE DIRECTORY DOMAIN NAME>",
                "TenantId": "<ACTIVE DIRECTORY ID>",
                "ADApplicationClientId": "<AD APPLICATION CLIENT ID>",
                "ADApplicationClientSecret": "<AD APPLICATION CLIENT SECRET>",
                "SubscriptionId":"<AZURE SUBSCRIPTION ID>"
        }


### Response

| Name           |           Type       |          Description  |
|----------------|----------------------|-----------------------|
| accountId	 |           String     | Cloudneeti AWS cloud account id onboarded      |
| jobId  	 |           String     | Cloudneeti AWS cloud account scan job id      |
| statusCode	     |           Integer     | Cloudneeti API status code      |
| message |      String     | Response describing operation result as success or failed.     |

### Sample Response

        {
                "result": {
                        "accountId": "5fc68c34-1225-413d-879b-02ff5e9c4d9c",
                        "jobId": "bd3c2340-d50d-4dce-8deb-f6e996e2e696"
                },
                "statusCode": 200,
                "message": "Request Successful"
        }



## Office365

### Prerequisistes
Please follow steps 1 and 2 in [page](../../onboardingGuide/office365Subscription/){target=_blank} to do prerequisite configurations.

        POST https://<CLOUDNEETI API DOMAIN>/onboarding/license/<LICENSE ID>/account/office

| Environment	| Values for Cloudneeti API domain     |
|---------------|--------------------------------------|
| PROD 	        |   api.cloudneeti.com                 |
| TRIAL 	| trialapi.cloudneeti.com              |

### URI Parameters

| Parameter           |           Description                                |           Required/Optional  |
|-----------|----------------------------------------------------------------|----------------------------|
| license   |          [Cloudneeti License Id​](#license-id)                  | Required|

### Header

| Key	        | Value                                |
|---------------|--------------------------------------|
| Content-Type 	                |   application/json                 |
| Ocp-Apim-Subscription-Key 	| [Cloudneeti API key generated](../../administratorGuide/configureCloudneetiAPIAccess/)             |


### Request body

For prerequisites for Office365 cloud account onboarding, please refer [Office 365 Subscription](../office365Subscription/)

       {
                "AccountName": "<CLOUD ACCOUNT NAME>",
                "DomainName": "<ACTIVE DIRECTORY DOMAIN NAME>",
                "TenantId": "<ACTIVE DIRECTORY ID>",
                "ADApplicationClientId": "<AD APPLICATION CLIENT ID>",
                "ADApplicationClientSecret": "<AD APPLICATION CLIENT SECRET>"
        }


### Response

| Name           |           Type       |          Description  |
|----------------|----------------------|-----------------------|
| accountId	 |           String     | Cloudneeti AWS cloud account id onboarded      |
| jobId  	 |           String     | Cloudneeti AWS cloud account scan job id      |
| statusCode	     |           Integer     | Cloudneeti API response status code      |
| message |      String     | Response describing operation result as success or failed.     |

### Sample Response
        {
                "result": {
                        "accountId": "20103420-7be6-4969-b1d2-cffd943033e3",
                        "jobId": "53f7d814-14ed-4981-85ef-d1862047f54b"
                },
                "statusCode": 200,
                "message": "Request Successful"
        }

