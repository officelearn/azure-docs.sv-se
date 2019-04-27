---
title: Distribuera resurser med REST API och en mall | Microsoft Docs
description: Använda Azure Resource Manager och Resource Manager REST API för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 15e4a7058dc1e74c726644e86c58381003eee937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782981"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuera resurser med Resource Manager-mallar och Resource Manager REST API

Den här artikeln förklarar hur du använder Resource Manager REST API med Resource Manager-mallar för att distribuera dina resurser till Azure.  

Du kan antingen inkludera mallen i förfrågans text eller länk till en fil. När du använder en fil, kan det vara en lokal fil eller en extern fil som är tillgänglig via en URI. När mallen är i ett lagringskonto kan du begränsa åtkomsten till mallen och ange en token för delad åtkomst (signatur) under distributionen.

## <a name="deployment-scope"></a>Distributionsomfattningen

Du kan begränsa distributionen till en Azure-prenumeration eller resursgrupp inom en prenumeration. I de flesta fall kommer du rikta distribution till en resursgrupp. Använda prenumerationsdistributioner för att tillämpa principer och rolltilldelningar i prenumerationen. Du kan också använda prenumerationsdistributioner för att skapa en resursgrupp och distribuera resurser till den. Beroende på omfattningen av distributionen, kan du använda olika kommandon.

Distribuera till en **resursgrupp**, använda [distributioner – skapa](/rest/api/resources/deployments/createorupdate).

Distribuera till en **prenumeration**, använda [distributioner – skapa på Prenumerationsomfattningen](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

I exemplen i den här artikeln används distribution av resursgrupper. Mer information om prenumerationsdistributioner finns i [skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API

1. Ange [gemensamma parametrar och rubriker](/rest/api/azure/), inklusive autentiseringstoken.

1. Om du inte har en befintlig resursgrupp, skapa en resursgrupp. Ange ditt prenumerations-ID, namnet på den nya resursgruppen och platsen som du behöver för din lösning. Mer information finns i [skapa en resursgrupp](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
   ```

   Med en begärandetext som:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Verifiera distributionen innan du kör genom att köra den [Validera malldistributionen av en](/rest/api/resources/deployments/validate) igen. När du testar distributionen kan du ange parametrar, precis som när du genomför distributionen (visas i nästa steg).

1. Skapa en distribution. Ange ditt prenumerations-ID, namnet på resursgruppen, namnet på distributionen och en länk till mallen. Läs om hur mallfilen [parameterfilen](#parameter-file). Läs mer om REST-API för att skapa en resursgrupp, [skapar en för malldistribution](/rest/api/resources/deployments/createorupdate). Observera den **läge** är inställd på **stegvis**. Om du vill köra en fullständig distribution **läge** till **Slutför**. Var försiktig när du använder det fullständiga läget som du kan oavsiktligt ta bort resurser som inte är i mallen.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   Med en begärandetext som:

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
   }
   ```

    Om du vill logga svarsinnehåll, begär innehåll eller både inkludera **debugSetting** i begäran.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Du kan ställa in ditt storage-konto du använder en token för delad åtkomst (signatur). Mer information finns i [delegera åtkomst med en signatur för delad åtkomst](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. I stället för att länka till filer för mallen och parametrarna kan inkludera du dem i begärandetexten.

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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
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

1. Hämta status för malldistributionen. Mer information finns i [få information om en malldistributionen](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *återställning vid fel*. När en distribution misslyckas, kan du automatiskt distribuera om en tidigare lyckad distribution från distributionshistoriken. Om du vill ange omdistribution, använda den `onErrorDeployment` -egenskapen i begärandetexten. Den här funktionen är användbar om du har har ett fungerande tillstånd för din distribution av infrastruktur och vill ha detta återställas till. Det finns ett antal varningar och begränsningar:

- Omdistributionen körs exakt som den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Föregående distributionen körs med hjälp av den [fullständig läge](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den föregående distributionen tas bort och alla resurskonfigurationer ställs till sina tidigare tillstånd. Kontrollera att du förstår de [distributionslägen](./deployment-modes.md).
- Omdistributionen påverkar endast resurserna, dataändringar påverkas inte.
- Den här funktionen stöds bara på resursgrupp distributioner, inte prenumerationen på distributioner. Mer information om prenumerationen på distribution finns i [skapa resursgrupper och resurser på prenumerationsnivå](./deploy-to-subscription.md).

Om du vill använda det här alternativet för måste dina distributioner ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn, kan den aktuella misslyckad distributionen skriva över tidigare distributionen i historiken. Du kan bara använda det här alternativet med rot på distributioner. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill distribuera om den senaste distributionen om den aktuella distributionen misslyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Om du vill distribuera om en specifik distribution om den aktuella distributionen misslyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Den angivna distributionen måste ha lyckats.

## <a name="parameter-file"></a>Parameterfilen

Om du använder en parameterfil för att ange parametervärden under distributionen kan behöva du skapa en JSON-fil med ett format som liknar följande exempel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

Storleken på parameterfilen får inte vara mer än 64 KB.

Om du behöver ange något känsligt värde för en parameter (till exempel ett lösenord), lägger du till detta värde till ett nyckelvalv. Hämta nyckelvalvet under distributionen som du ser i exemplet ovan. Mer information finns i [skicka säkra värden under distributionen](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Nästa steg

- Om du vill ange hur du hanterar resurser som finns i resursgruppen men inte har definierats i mallen, se [distributionslägen i Azure Resource Manager](deployment-modes.md).
- Läs om hur du hanterar asynkrona REST-åtgärder i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
- Ett exempel för att distribuera resurser via .NET-klientbiblioteket finns i [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- För att definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
- Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
