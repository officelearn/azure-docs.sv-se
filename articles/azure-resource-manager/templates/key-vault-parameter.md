---
title: Key Vault hemlighet med mall
description: Visar hur du skickar en hemlighet från ett nyckelvalv som en parameter under distributionen.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458274"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använd Azure Key Vault för att skicka säkert parametervärde under distributionen

I stället för att placera ett säkert värde (som ett lösenord) direkt i mall- eller parameterfilen kan du hämta värdet från ett [Azure Key Vault](../../key-vault/general/overview.md) under en distribution. Du hämtar värdet genom att referera till nyckelvalvet och hemligheten i parameterfilen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till.

Den här artikeln fokuserar på scenariot att skicka ett känsligt värde som en mallparameter. Det täcker inte scenariot med att ange en egenskap för virtuella datorer till URL:en för ett certifikat i ett Nyckelvalv. En snabbstartsmall för det scenariot finns i [Installera ett certifikat från Azure Key Vault på en virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Distribuera viktiga valv och hemligheter

Om du vill komma åt `enabledForTemplateDeployment` ett nyckelvalv `true`under malldistributionen anger du nyckelvalvet till .

Om du redan har ett Key Vault kontrollerar du att malldistributionerna tillåters.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Om du vill skapa ett nytt Key Vault och lägga till en hemlighet använder du:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Som ägare av nyckelvalvet har du automatiskt tillgång till att skapa hemligheter. Om användaren som arbetar med hemligheter inte är ägare till nyckelvalvet ger du åtkomst med:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Mer information om hur du skapar nyckelvalv och lägger till hemligheter finns i:

- [Ange och hämta en hemlighet med CLI](../../key-vault/secrets/quick-create-cli.md)
- [Ange och hämta en hemlighet med Powershell](../../key-vault/secrets/quick-create-powershell.md)
- [Ange och hämta en hemlighet med hjälp av portalen](../../key-vault/secrets/quick-create-portal.md)
- [Ange och hämta en hemlighet med hjälp av .NET](../../key-vault/secrets/quick-create-net.md)
- [Ange och hämta en hemlighet med nod.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Ge tillgång till hemligheterna

Användaren som distribuerar mallen `Microsoft.KeyVault/vaults/deploy/action` måste ha behörighet för resursgruppens och nyckelvalvets omfattning. [Rollerna Ägare](../../role-based-access-control/built-in-roles.md#owner) och [Deltagare](../../role-based-access-control/built-in-roles.md#contributor) ger båda den här åtkomsten. Om du har skapat nyckelvalvet är du ägare så att du har behörighet.

Följande procedur visar hur du skapar en roll med minsta behörighet och hur du tilldelar användaren

1. Skapa en JSON-fil för anpassad rolldefinition:

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
    Ersätt "00000000-0000-0000-0000-00000000000000000" med prenumerations-ID.

2. Skapa den nya rollen med JSON-filen:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Exemplen tilldelar den anpassade rollen till användaren på resursgruppsnivå.

När du använder ett Nyckelvalv med mallen för ett [hanterat program](../managed-applications/overview.md)måste du bevilja åtkomst till tjänsten Resursproviderns huvudnamn för **apparatresurs.** Mer information finns i [Hemlighet till Åtkomstnyckelvalv när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referenshemligheter med statiskt ID

Med den här metoden refererar du till nyckelvalvet i parameterfilen, inte mallen. Följande bild visar hur parameterfilen refererar till hemligheten och skickar det värdet till mallen.

![Resurshanterarens nyckelvalvsintegrering Statiskt ID-diagram](./media/key-vault-parameter/statickeyvault.png)

[Självstudiekurs: Integrera Azure Key Vault i Resource Manager-malldistributionen](./template-tutorial-use-key-vault.md) använder den här metoden.

Följande mall distribuerar en SQL-server som innehåller ett administratörslösenord. Lösenordsparametern är inställd på en säker sträng. Men mallen anger inte var värdet kommer ifrån.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Nu skapar du en parameterfil för föregående mall. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. Referensvärdet för parametervärdet från nyckelvalvet. Du refererar till hemligheten genom att skicka resursidentifieraren för nyckelvalvet och namnet på hemligheten:

I följande parameterfil måste nyckelvalvets hemlighet redan finnas och du anger ett statiskt värde för dess resurs-ID.

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

Om du behöver använda en annan version av hemligheten `secretVersion` än den aktuella versionen använder du egenskapen.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och skicka in parameterfilen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Referenshemligheter med dynamiskt ID

Föregående avsnitt visade hur du skickar ett statiskt resurs-ID för nyckelvalvets hemlighet från parametern. I vissa fall måste du dock referera till en nyckelvalvshemlighet som varierar beroende på den aktuella distributionen. Du kanske vill skicka parametervärden till mallen i stället för att skapa en referensparameter i parameterfilen. I båda fallen kan du dynamiskt generera resurs-ID för en nyckelvalvshemlighet med hjälp av en länkad mall.

Du kan inte dynamiskt generera resurs-ID i parameterfilen eftersom malluttryck inte är tillåtna i parameterfilen.

I den överordnade mallen lägger du till den kapslade mallen och skickar in en parameter som innehåller det dynamiskt genererade resurs-ID:et. Följande bild visar hur en parameter i den länkade mallen refererar till hemligheten.

![Dynamiskt ID](./media/key-vault-parameter/dynamickeyvault.png)

Följande mall skapar dynamiskt nyckelvalvs-ID:et och skickar det som en parameter.

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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Allmän information om nyckelvalv finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md).
- Fullständiga exempel på refererande av nyckelhemligheter finns i [Exempel på Nyckelvalv](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
