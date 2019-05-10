---
title: Nyckelvalvshemligheten med Azure Resource Manager-mall | Microsoft Docs
description: Visar hur du skickar en hemlighet från key vault som en parameter under distributionen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: e47a087e27b6a8ade947e36ded762ce2e518ca25
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508000"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använda Azure Key Vault för att skicka säkra parametervärdet under distributionen

Istället för att sätta ett säkert värde (t.ex. ett lösenord) direkt i din mall eller parametern-fil, kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md) under en distribution. Du kan hämta värdet genom att referera till nyckelvalvet och hemligheten i parameterfilen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till.

## <a name="deploy-key-vaults-and-secrets"></a>Distribuera nyckelvalv och hemligheter

Om du vill komma åt ett nyckelvalv när mallen distribueras, ange `enabledForTemplateDeployment` i nyckelvalvet till `true`.

Följande Azure CLI och Azure PowerShell-exemplen visar hur du skapar nyckelvalvet och lägga till en hemlighet.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Som ägare av key vault kan har du automatiskt tillgång till skapa hemligheter. Om användare som arbetar med hemligheter inte är ägare av key vault kan du bevilja åtkomst med:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Mer information om att skapa nyckelvalv och lägger till hemligheter finns:

- [Ange och hämta en hemlighet med hjälp av CLI](../key-vault/quick-create-cli.md)
- [Ange och hämta en hemlighet med hjälp av Powershell](../key-vault/quick-create-powershell.md)
- [Ange och hämta en hemlighet med hjälp av portalen](../key-vault/quick-create-portal.md)
- [Ange och hämta en hemlighet med hjälp av .NET](../key-vault/quick-create-net.md)
- [Ange och hämta en hemlighet med hjälp av Node.js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Bevilja åtkomst till hemligheterna

Den användare som distribuerar mallen måste ha den `Microsoft.KeyVault/vaults/deploy/action` behörighet för detta scope för resursgruppen och nyckelvalvet. Den [ägare](../role-based-access-control/built-in-roles.md#owner) och [deltagare](../role-based-access-control/built-in-roles.md#contributor) båda bevilja åtkomst. Om du skapade nyckelvalvet är ägare så att du har behörighet.

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

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Exemplen tilldela den anpassade rollen till användaren på resursgruppsnivå.  

När du använder ett Nyckelvalv med hjälp av mallen för en [Managed Application](../managed-applications/overview.md), måste du bevilja åtkomst till den **installation Resource Provider** tjänstens huvudnamn. Mer information finns i [åtkomst till Key Vault-hemlighet när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referens för hemligheter med statiska ID

Med den här metoden kan referera du till key vault i parameterfilen, inte i mallen. Följande bild visar hur parameterfilen refererar till hemligheten och skickar det värdet i mallen.

![Resource Manager key vault-integrering Static ID diagram](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Självstudie: Integrera Azure Key Vault i Resource Manager-mall distribution](./resource-manager-tutorial-use-key-vault.md) använder den här metoden.

Följande mallen distribuerar en SQLServer som innehåller ett administratörslösenord. Lösenordsparametern har angetts till en säker sträng. Men mallen ange inte där värdet kommer från.

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

Nu skapa en parameterfil för föregående mall. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. För parametern, referera till hemligheten från nyckelvalvet. Du kan referera hemligheten genom att skicka resurs-ID för nyckelvalvet och namnet på hemligheten:

I följande parameterfilen, key vault-hemlighet måste redan finnas och du kan ange ett statiskt värde för dess resurs-ID.

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och ange parameterfilen:

Om du använder Azure CLI använder du:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Om du använder PowerShell använder du:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Referens för hemligheter med dynamiska ID

Föregående avsnitt visade hur du skickar en statisk resurs-ID för key vault-hemlighet från parametern. Men i vissa fall kan behöva du referera till en key vault-hemlighet som varierar baserat på den aktuella distributionen. Eller så kan du ange parametervärden för mallen i stället för att skapa en referens-parameter i parameterfilen. I båda fallen kan du dynamiskt generera resurs-ID för en hemlighet i nyckelvalvet genom att använda en länkad mall.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Om du använder PowerShell använder du:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Nästa steg

- Allmän information om viktiga valv finns i [vad är Azure Key Vault?](../key-vault/key-vault-overview.md).
- Komplett exempel på refererar till viktiga hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
