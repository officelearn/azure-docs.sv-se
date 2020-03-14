---
title: Distribuera resurser med REST API och mall
description: Använd Azure Resource Manager-och Resource Manager-REST API för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: fc386f51073c256fd083a04bbed39316784827b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273818"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuera resurser med Resource Manager-mallar och Resource Manager REST API

Den här artikeln förklarar hur du använder Resource Manager-REST API med Resource Manager-mallar för att distribuera dina resurser till Azure.

Du kan antingen inkludera din mall i begär ande texten eller länka till en fil. När du använder en fil kan det vara en lokal fil eller en extern fil som är tillgänglig via en URI. När din mall finns i ett lagrings konto kan du begränsa åtkomsten till mallen och tillhandahålla en SAS-token (signatur för delad åtkomst) under distributionen.

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta din distribution till en hanterings grupp, en Azure-prenumeration eller en resurs grupp. I de flesta fall ska du rikta distributioner till en resurs grupp. Använd hanterings grupper eller prenumerations distributioner för att tillämpa principer och roll tilldelningar i det angivna omfånget. Du kan också använda prenumerations distributioner för att skapa en resurs grupp och distribuera resurser till den. Beroende på distributionens omfattning använder du olika kommandon.

Använd [distributioner – skapa](/rest/api/resources/deployments/createorupdate)för att distribuera till en **resurs grupp**. Begäran skickas till:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Om du vill distribuera till en **prenumeration**använder du [distributioner – skapa vid prenumerations omfång](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Begäran skickas till:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

Om du vill distribuera till en **hanterings grupp**använder du [distributioner – skapa i hanterings gruppens omfång](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Begäran skickas till:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

I exemplen i den här artikeln används resurs grupps distributioner.

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API

1. Ange [vanliga parametrar och rubriker](/rest/api/azure/), inklusive autentiseringstoken.

1. Om du inte har en befintlig resurs grupp skapar du en resurs grupp. Ange ditt prenumerations-ID, namnet på den nya resurs gruppen och den plats som du behöver för din lösning. Mer information finns i [skapa en resurs grupp](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Med en begär ande text som:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Verifiera distributionen innan du kör den genom att köra [distributions åtgärden verifiera en mall](/rest/api/resources/deployments/validate) . När du testar distributionen anger du parametrar exakt som du skulle göra när du utförde distributionen (visas i nästa steg).

1. Om du vill distribuera en mall anger du ditt prenumerations-ID, namnet på resurs gruppen, namnet på distributionen i begärande-URI: n.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   I begär ande texten anger du en länk till mallen och parameter filen. Mer information om parameter filen finns i [create Resource Manager parameter File](parameter-files.md).

   Observera att **läget** är inställt på **stegvis**. Om du vill köra en fullständig distribution anger du att **läget** ska **slutföras**. Var försiktig när du använder det fullständiga läget eftersom du oavsiktligt kan ta bort resurser som inte finns i din mall.

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

    Om du vill logga svars innehåll, begära innehåll eller både och inkluderar **debugSetting** i begäran.

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

    Du kan konfigurera ditt lagrings konto så att det använder en SAS-token (signatur för delad åtkomst). Mer information finns i [Delegera åtkomst med en signatur för delad åtkomst](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Om du behöver ange ett känsligt värde för en parameter (till exempel ett lösen ord) lägger du till värdet i ett nyckel valv. Hämta nyckel valvet under distributionen som visas i föregående exempel. Mer information finns i [Skicka säkra värden under distributionen](key-vault-parameter.md).

1. I stället för att länka till filer för mallen och parametrarna kan du ta med dem i begär ande texten. I följande exempel visas begär ande texten med mallen och den infogade parametern:

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

1. Om du vill hämta distributionens status använder du [distributioner-get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Läs mer om hur du hanterar asynkrona REST-åtgärder i [spåra asynkrona Azure-åtgärder](../management/async-operations.md).
- Mer information om mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).

