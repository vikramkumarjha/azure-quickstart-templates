# chef-automate-ha

This Azure Quickstart provides an 'unmanaged' full installation of Chef Server configured for high availability mode and a separate instance of Chef Automate.

Whilst the template is fully functional, it is expected that some customization may be required for your particular environment.  It is aimed at experienced administrators of Chef.

This template uses Azure Key Vault to securely store and transfer secrets between VMs in the solution.

## Deployment Outcomes

After deploying this template into your subscription, you will have deployed:

- 3x Chef Server frontend VMs behind a load balancer
- 3x Chef Server backend VMs
- 1x Chef Automate VM
- 1x Key Vault

Frontend and Backend will be configured with individual Availability Sets and Premium storage.  All servers use Managed Disk and have configurable VM sizes.  Chef Server will automatically be configured to send run data to Chef Automate.

## Prerequisites

Installation can proceed via [Azure Cloud Shell](https://azure.microsoft.com/en-us/features/cloud-shell/) or by using the [Azure CLI v2](https://docs.microsoft.com/en-us/cli/azure/overview) on a workstation.

The identity running this installation should have the **Owner** role on the required Subscription.

## Installation Instructions

### 1. Create Service Principal [optional]

The template shares information via Key Vault and to do this we need to create a Service Principal to pass in to the template properties.  If you do not have an existing Service Principal, you can create one using the **az ad sp create-for-rbac** command:

```bash
stuart@Azure:~$ az ad sp create-for-rbac
Retrying role assignment creation: 1/36
    {
      "appId": "a530c3a0-YOUR-GUID-HERE-21e3d7ede80c",
      "displayName": "azure-cli-2017-05-23-15-28-34",
      "name": "http://azure-cli-2017-05-23-15-28-34",
      "password": "ef795567-YOUR-GUID-HERE-59158b2246c3",
      "tenant": "a2b2d6bc-YOUR-GUID-HERE-f97a7ac416d7"
    }
```

You can then use the appId to retrieve further details required via the **az ad sp show --id [appId]** command:

```bash
stuart@Azure:~$ az ad sp show --id a530c3a0-YOUR-GUID-HERE-21e3d7ede80c
    {
        "appId": "a530c3a0-YOUR-GUID-HERE-21e3d7ede80c",
        "displayName": "azure-cli-2017-05-23-15-28-34",
        "objectId": "1a439c30-YOUR-GUID-HERE-9df19f9b1c89",
        "objectType": "ServicePrincipal",
        "servicePrincipalNames": [
          "http://azure-cli-2017-05-23-15-28-34",
          "a530c3a0-YOUR-GUID-HERE-21e3d7ede80c"
        ]
    }
```

Note the values for **appId**, **objectId** and **password** for the parameters file.

### 2. Customize azuredeploy.parameters file

*[Cloud Shell]* You will need to copy ```azuredeploy.json``` and ```azuredeploy.json.parameters``` to your Cloud Shell before updating the parameters:

Update **appId, password, objectId, firstname, lastname, emailid** and **organization name** and any other required parameters in the ```azuredeploy.parameters.json``` file.

### 3. Create Resource Group for solution

Use the **az group create** command to create a Resource Group in your region, e.g:

```bash
az group create -n chef-automate-ha -l westus
```

### 4. Execute the template deployment

Use the **az group deployment create** command to deploy the ARM template, e.g.:

```bash
az group deployment create --template-file './azuredeploy.json' --parameters '@./azuredeploy.parameters.json' -g chef-automate-ha -n deploy
```

NB: Deployment may take between 30-60 minutes depending on deployment size.

After successful deployment you can see the ```adminusername```, ```chef-server-URL```, ```chef-server-fqdn```, ```keyvaultName```, ```chef-server-weblogin-username```, ```chef-server-weblogin-password```, ```chef-automate-URL``` and ```chef-automate-fqdn``` for Chef Server, Chef Backend and Chef Automate in the deployment output section of your Resource Group.

## Post-Installation and Verification

## Further information

- To learn more about Chef, visit [learn.chef.io](https://learn.chef.io)

## Licensing

New users may try the features of this template (including Chef Automate and Chef Backend components) using a trial license for up to 30 days. Contact us at [azuremktplcsales@chef.io](mailto:azuremktplcsales@chef.io) to obtain a full license.

## Contact

Contact the [Partner Engineering team at Chef](mailto:partnereng@chef.io) for queries relating to thie template.

(c) 2017 Chef Software, Inc.