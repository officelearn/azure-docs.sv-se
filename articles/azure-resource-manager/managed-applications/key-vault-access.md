---
title: Använda Key Vault vid distribution av hanterad app
description: Visar hur du använder åtkomsthemligheter i Azure Key Vault när du distribuerar hanterade program
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458291"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Åtkomst till nyckelvalv hemlighet vid distribution av Azure Managed Applications

När du behöver skicka ett säkert värde (som ett lösenord) som en parameter under distributionen kan du hämta värdet från ett [Azure Key Vault](../../key-vault/general/overview.md). Om du vill komma åt Nyckelvalvet när du distribuerar hanterade program måste du bevilja åtkomst till tjänsten **Resursproviderns** huvudnamn för Resursprovidern. Tjänsten Hanterade program använder den här identiteten för att köra åtgärder. Om du vill hämta ett värde från ett Nyckelvalv under distributionen måste tjänstens huvudnamn kunna komma åt Nyckelvalvet.

I den här artikeln beskrivs hur du konfigurerar Nyckelvalvet så att det fungerar med hanterade program.

## <a name="enable-template-deployment"></a>Aktivera malldistribution

1. Välj ditt Nyckelvalv i portalen.

1. Välj **Åtkomstprinciper**.   

   ![Välj åtkomstprinciper](./media/key-vault-access/select-access-policies.png)

1. Välj **Click to show advanced access policies** (Klicka för att visa avancerade åtkomstprinciper).

   ![Visa avancerade åtkomstprinciper](./media/key-vault-access/advanced.png)

1. Välj **Aktivera åtkomst till Azure Resource Manager för malldistribution**. Välj sedan **Spara**.

   ![Aktivera malldistribution](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Lägg till tjänst som deltagare

1. Välj **Åtkomstkontroll (IAM)**.

   ![Välj åtkomstkontroll](./media/key-vault-access/access-control.png)

1. Välj **Lägg till rolltilldelning**.

   ![Välj Lägg till](./media/key-vault-access/add-access-control.png)

1. Välj **Deltagare** för rollen. Sök efter Resursleverantör för **apparat** och välj den bland de tillgängliga alternativen.

   ![Sök efter leverantör](./media/key-vault-access/search-provider.png)

1. Välj **Spara**.

## <a name="reference-key-vault-secret"></a>Referensnyckelvalv hemlighet

Om du vill skicka en hemlighet från ett Nyckelvalv till en mall i det hanterade programmet måste du använda en [länkad eller kapslad mall](../templates/linked-templates.md) och referera till Nyckelvalvet i parametrarna för den länkade eller kapslade mallen. Ange resurs-ID för Key Vault och namnet på hemligheten.

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

Du har konfigurerat nyckelvalvet så att det blir tillgängligt under distributionen av ett hanterat program.

* Information om hur du skickar ett värde från ett Nyckelvalv som mallparameter finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](../templates/key-vault-parameter.md).
* Exempel på hanterade program finns i [Exempel på projekt för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.
