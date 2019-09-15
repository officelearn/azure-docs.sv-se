---
title: Key Vault hemlighet med Azure Resource Manager-mall | Microsoft Docs
description: Visar hur du skickar en hemlighet från ett nyckel valv som en parameter under distributionen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: 489b09d2523393ae67668ed13c651c9b7b0217b4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998896"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använd Azure Key Vault för att skicka ett säkert parameter värde under distributionen

I stället för att ange ett säkert värde (till exempel ett lösen ord) direkt i din mall eller parameter fil kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-overview.md) under en distribution. Du hämtar värdet genom att referera till nyckel valvet och hemligheten i parameter filen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Nyckel valvet kan finnas i en annan prenumeration än den resurs grupp som du distribuerar till.

## <a name="deploy-key-vaults-and-secrets"></a>Distribuera nyckel valv och hemligheter

Om du vill få åtkomst till ett nyckel valv under `enabledForTemplateDeployment` mal Lav distributionen anger du `true`nyckel valvet till.

Följande exempel på Azure CLI och Azure PowerShell visar hur du skapar nyckel valvet och lägger till en hemlighet.

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

Som ägare till nyckel valvet har du automatiskt åtkomst till att skapa hemligheter. Om användaren som arbetar med hemligheter inte är ägare till nyckel valvet ger du till gång till:

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

Mer information om hur du skapar nyckel valv och lägger till hemligheter finns i:

- [Ange och hämta en hemlighet med hjälp av CLI](../key-vault/quick-create-cli.md)
- [Ange och hämta en hemlighet med hjälp av PowerShell](../key-vault/quick-create-powershell.md)
- [Ange och hämta en hemlighet med hjälp av portalen](../key-vault/quick-create-portal.md)
- [Ange och hämta en hemlighet med .NET](../key-vault/quick-create-net.md)
- [Ange och hämta en hemlighet med Node. js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Bevilja åtkomst till hemligheterna

Den användare som distribuerar mallen måste ha `Microsoft.KeyVault/vaults/deploy/action` behörighet för omfånget för resurs gruppen och nyckel valvet. [Ägare](../role-based-access-control/built-in-roles.md#owner) och [deltagar](../role-based-access-control/built-in-roles.md#contributor) roller beviljar den här åtkomsten. Om du har skapat nyckel valvet är du ägare så att du har behörigheten.

Följande procedur visar hur du skapar en roll med minsta behörighet och hur du tilldelar användaren

1. Skapa en JSON-fil för anpassad roll definition:

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
    Ersätt "00000000-0000-0000-0000-000000000000" med prenumerations-ID: t.

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

    Exemplen tilldelar användaren den anpassade rollen på resurs grupps nivå.  

När du använder en Key Vault med mallen för ett [hanterat program](../managed-applications/overview.md)måste du bevilja åtkomst till tjänstens huvud namn för enhets **resurs leverantören** . Mer information finns i [åtkomst Key Vault hemlighet när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referens hemligheter med statiskt ID

Med den här metoden refererar du till nyckel valvet i parameter filen, inte mallen. Följande bild visar hur parameter filen refererar till hemligheten och skickar värdet till mallen.

![Statiskt ID-diagram för Key Vault-integrering i Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Självstudier: Integrera Azure Key Vault i Resource Manager malldistribution](./resource-manager-tutorial-use-key-vault.md) använder den här metoden.

Följande mall distribuerar en SQL-Server som innehåller ett administratörs lösen ord. Lösen ords parametern är inställd på en säker sträng. Men mallen anger inte var värdet kommer från.

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

Skapa nu en parameter fil för föregående mall. I parameter filen anger du en parameter som matchar namnet på parametern i mallen. Som parameter värde refererar du till hemligheten från nyckel valvet. Du refererar till hemligheten genom att skicka resurs identifieraren för nyckel valvet och namnet på hemligheten:

I följande parameter fil måste nyckel valvets hemlighet redan finnas och du anger ett statiskt värde för resurs-ID: t.

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

Om du behöver använda en annan version av hemligheten än den aktuella versionen använder `secretVersion` du egenskapen.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och skicka den till parameter filen:

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

## <a name="reference-secrets-with-dynamic-id"></a>Referens hemligheter med dynamiskt ID

Föregående avsnitt visade hur du skickar ett statiskt resurs-ID för nyckel valvets hemlighet från parametern. I vissa fall måste du dock referera till en nyckel valvs hemlighet som varierar beroende på den aktuella distributionen. Eller så kanske du vill skicka parameter värden till mallen i stället för att skapa en referens parameter i parameter filen. I båda fallen kan du dynamiskt generera resurs-ID för en nyckel valv hemlighet genom att använda en länkad mall.

Du kan inte dynamiskt generera resurs-ID: t i parameter filen eftersom det inte går att använda Template-uttryck i parameter filen. 

I den överordnade mallen lägger du till den länkade mallen och skickar den till en parameter som innehåller det dynamiskt genererade resurs-ID: t. Följande bild visar hur en parameter i den länkade mallen refererar till hemligheten.

![Dynamiskt ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Följande mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) skapar dynamiskt Key Vault-ID och skickar det som en parameter.

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

Distribuera föregående mall och ange värden för parametrarna. Du kan använda exempel mal len från GitHub, men du måste ange parameter värden för din miljö.

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

- Allmän information om nyckel valv finns i [Azure Key Vault?](../key-vault/key-vault-overview.md).
- Fullständiga exempel på referenser till nyckel hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
