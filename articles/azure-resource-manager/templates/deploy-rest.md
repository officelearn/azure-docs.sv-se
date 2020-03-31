---
title: Distribuera resurser med REST API och mall
description: Använd AZURE Resource Manager och Resource Manager REST API för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153241"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Distribuera resurser med ARM-mallar och RESURSHANTERARENS REST API

I den här artikeln beskrivs hur du använder REST-API:et för Resurshanteraren med Azure Resource Manager -mallar (ARM) för att distribuera dina resurser till Azure.

Du kan antingen inkludera mallen i förfråspråkstexten eller länka till en fil. När du använder en fil kan det vara en lokal fil eller en extern fil som är tillgänglig via en URI. När mallen finns i ett lagringskonto kan du begränsa åtkomsten till mallen och tillhandahålla en SAS-token (Shared Access Signature) under distributionen.

## <a name="deployment-scope"></a>Distributionsomfattning

Du kan rikta distributionen till en hanteringsgrupp, en Azure-prenumeration eller en resursgrupp. I de flesta fall riktar du distributioner till en resursgrupp. Använd hanteringsgrupp eller prenumerationsdistributioner för att tillämpa principer och rolltilldelningar i det angivna scopet. Du kan också använda prenumerationsdistributioner för att skapa en resursgrupp och distribuera resurser till den. Beroende på distributionens omfattning använder du olika kommandon.

Om du vill distribuera till en **resursgrupp**använder du [Distributioner - Skapa](/rest/api/resources/deployments/createorupdate). Begäran skickas till:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Om du vill distribuera till en **prenumeration**använder du [Distributioner - Skapa i prenumerationsomfattning](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Begäran skickas till:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Mer information om distributioner på prenumerationsnivå finns i [Skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

Om du vill distribuera till en **hanteringsgrupp**använder du [Distributioner - Skapa i hanteringsgruppomfattning](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Begäran skickas till:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Mer information om distributioner på hanteringsgruppsnivå finns i [Skapa resurser på hanteringsgruppsnivå](deploy-to-management-group.md).

Exemplen i den här artikeln använder resursgruppsdistributioner.

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API

1. Ange [vanliga parametrar och rubriker](/rest/api/azure/), inklusive autentiseringstoken.

1. Om du inte har en befintlig resursgrupp skapar du en resursgrupp. Ange ditt prenumerations-ID, namnet på den nya resursgruppen och den plats som du behöver för din lösning. Mer information finns i [Skapa en resursgrupp](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Med en begäran kropp som:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Verifiera distributionen innan du kör den genom att köra åtgärden [Verifiera en malldistribution.](/rest/api/resources/deployments/validate) När du testar distributionen anger du parametrar precis som när du kör distributionen (visas i nästa steg).

1. Om du vill distribuera en mall anger du ditt prenumerations-ID, namnet på resursgruppen, namnet på distributionen i URI-begäran.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   Ange en länk till mall- och parameterfilen i brödtexten. Mer information om parameterfilen finns i [Skapa Resource Manager-parameterfil](parameter-files.md).

   Observera att **läget** är inställt **på Inkrementell**. Om du vill köra en fullständig distribution ställer du in **läget** till **Slutför**. Var försiktig när du använder hela läget eftersom du av misstag kan ta bort resurser som inte finns i mallen.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Om du vill logga svarsinnehåll, begära innehåll eller både och inkludera **felsökning** i begäran.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Du kan konfigurera ditt lagringskonto så att det använder en SAS-token (Shared Access Signature). Mer information finns i [Delegera åtkomst med en signatur för delad åtkomst](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Om du behöver ange ett känsligt värde för en parameter (till exempel ett lösenord) lägger du till det värdet i ett nyckelvalv. Hämta nyckelvalvet under distributionen som visas i föregående exempel. Mer information finns i [Skicka säkra värden under distributionen](key-vault-parameter.md).

1. I stället för att länka till filer för mallen och parametrarna kan du inkludera dem i begärandetexten. I följande exempel visas förfråextörstexten med mallen och parametern infogad:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Om du vill hämta status för malldistributionen använder du [Distributioner - Get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel finns [i Återställning vid fel till en lyckad distribution](rollback-on-error.md).
- Information om hur resurser som finns i resursgruppen men som inte har definierats i mallen ska [hanteras](deployment-modes.md).
- Mer information om hur du hanterar asynkrona REST-åtgärder finns i [Spåra asynkrona Azure-åtgärder](../management/async-operations.md).
- Mer information om mallar finns [i Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).

