---
title: Nyckelvalvshemligheten med Azure Resource Manager-mall | Microsoft Docs
description: Visar hur du skickar en hemlighet från key vault som en parameter under distributionen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: e04ee6b74be0bb7ec81166f43295a8a9377f8cf8
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381525"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använda Azure Key Vault för att skicka säkra parametervärdet under distributionen

När du vill skicka ett säkert värde (t.ex. ett lösenord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md). Du kan hämta värdet genom att referera till nyckelvalvet och hemligheten i parameterfilen. Värdet exponeras aldrig eftersom du bara referera till dess nyckelvalv-ID. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till.

## <a name="deploy-a-key-vault-and-secret"></a>Distribuera ett nyckelvalv och en hemlighet

Använd Azure CLI eller PowerShell för att skapa ett nyckelvalv och hemlighet. `enabledForTemplateDeployment` är en egenskap för nyckelvalvet. Åtkomst till hemligheter i den här Key Vault från Resource Manager-distribution, `enabledForTemplateDeployment` måste vara `true`. 

Följande Azure PowerShell och Azure CLI-exempelskript visar hur du skapar ett Nyckelvalv och en hemlighet.

Om du använder Azure CLI använder du:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Om du kör PowerShell-skriptet utanför Cloud Shell, använder du följande kommando för att generera lösenord i stället:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

För att använda Resource Manager-mall: Se [självstudien: Integrera Azure Key Vault vid malldistribution i Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Varje Azure-tjänst har specifika lösenordskrav. Till exempel krav för Azure virtuella datorer finns på [vilka är lösenordskraven för när du skapar en virtuell dator?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>Aktivera åtkomst till hemligheten

Annat än inställningen `enabledForTemplateDeployment` till `true`, användaren distribuerar mallen måste ha den `Microsoft.KeyVault/vaults/deploy/action` behörighet för scope som innehåller Nyckelvalvet inklusive resursgrupp och Key Vault. Den [ägare](../role-based-access-control/built-in-roles.md#owner) och [deltagare](../role-based-access-control/built-in-roles.md#contributor) båda bevilja åtkomst. Om du skapar Key Vault kan är du ägare så att du har behörighet. Om Key Vault hanteras av en annan prenumeration kan ägaren av Key Vault måste ge åtkomst.

Följande procedur visar hur du skapar en roll med den minsta behörigheten och tilldela användaren
1. Skapa en anpassad roll definition JSON-fil:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Ersätt ”00000000-0000-0000-0000-000000000000” med prenumerations-ID för den användare som behöver kunna distribuera mallarna.

2. Skapa den nya rollen med hjälp av JSON-filen:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    Den `New-AzureRmRoleAssignment` exempel tilldela den anpassade rollen till användaren på resursgruppsnivå.  

När du använder ett Nyckelvalv med hjälp av mallen för en [Managed Application](../managed-applications/overview.md), måste du bevilja åtkomst till den **installation Resource Provider** tjänstens huvudnamn. Mer information finns i [åtkomst till Key Vault-hemlighet när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Referera till en hemlighet med statiska ID

Mallen som tar emot en hemlighet i nyckelvalvet är som alla andra mallar. Det beror på **du referera till key vault i parameterfilen, inte i mallen.** Följande bild visar hur parameterfilen refererar till hemligheten och skickar det värdet i mallen.

![Statisk ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Till exempel den [följande mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) distribuerar en SQL-databas som innehåller ett administratörslösenord. Lösenordsparametern har angetts till en säker sträng. Men mallen anger inte där värdet kommer från.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Nu skapa en parameterfil för föregående mall. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. För parametern, referera till hemligheten från nyckelvalvet. Du kan referera hemligheten genom att skicka resurs-ID för nyckelvalvet och namnet på hemligheten. I den [följa parameterfilen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)key vault-hemlighet måste redan finnas och du kan ange ett statiskt värde för dess resurs-ID. Kopiera filen lokalt och ange prenumerations-ID, valvnamnet och SQL-servernamnet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Om du vill använda en version av hemligheten än den aktuella versionen kan du använda den `secretVersion` egenskapen.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och skicka in parameterfilen. Du kan använda mallen exempel från GitHub, men du måste använda en lokal parameterfil med de värden som anges i miljön.

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Om du använder PowerShell använder du:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referera till en hemlighet med dynamiska ID

Föregående avsnitt visade hur du skickar en statisk resurs-ID för key vault-hemlighet från parametern. Men i vissa fall kan behöva du referera till en key vault-hemlighet som varierar baserat på den aktuella distributionen. Eller så kan du helt enkelt ange parametervärden för mallen i stället skapa en referens-parameter i parameterfilen. I båda fallen kan du dynamiskt generera resurs-ID för en hemlighet i nyckelvalvet genom att använda en länkad mall.

Du kan inte dynamiskt generera resurs-ID i parameterfilen eftersom malluttryck inte tillåts i parameterfilen. 

I den överordnade mallen, du lägger till länkad mall och skicka in en parameter som innehåller dynamiskt skapade resurs-ID. Följande bild visar hur en parameter i mallen länkade refererar till hemligheten.

![Dynamic ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Den [följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dynamiskt skapar nyckelvalvs-ID och skickar det som en parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Distribuera föregående mall och ange värden för parametrarna. Du kan använda mallen exempel från GitHub, men du måste ange parametervärden för din miljö.

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Nästa steg
* Allmän information om viktiga valv finns i [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).
* Komplett exempel på refererar till viktiga hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).