---
title: Distribuera resurser med REST-API och mall | Microsoft Docs
description: Använd Azure Resource Manager och hanteraren för filserverresurser REST API för att distribuera en resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6915395abfedb75ec3ec0a5bd8f569ef2490e5be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuera resurser med Resource Manager-mallar och Resource Manager REST API

Den här artikeln förklarar hur du använder Resource Manager REST-API med Resource Manager-mallar för att distribuera resurserna till Azure.  

> [!TIP]
> För hjälp med felsökning av ett fel under distributionen, se:
> 
> * [Visa distributionsåtgärder](resource-manager-deployment-operations.md) att lära dig om att få information som hjälper du felsöka din fel
> * [Felsök vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md) att lära dig hur du löser vanliga distributionsfel
> 
> 

Mallen kan vara en lokal fil eller en extern fil som är tillgängliga via en URI. När mallen finns i ett lagringskonto kan du begränsa åtkomsten till mallen och erbjuder en token för delad åtkomst-signatur (SAS) under distributionen.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API
1. Ange [gemensamma parametrar och rubriker](/rest/api/azure/), inklusive autentiseringstoken.

2. Om du inte har en befintlig resursgrupp kan du skapa en resursgrupp. Ange ditt prenumerations-ID, namn på ny resursgrupp och plats som du behöver för din lösning. Mer information finns i [skapa en resursgrupp](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Verifiera distributionen innan du kör den genom att köra den [validera en malldistribution](/rest/api/resources/deployments/validate) igen. När du testar distributionen ange parametrar, precis som när du kör distributionen (visas i nästa steg).

4. Skapa en distribution. Ange ditt prenumerations-ID, namnet på resursgruppen, namnet på distributionen och en länk till mallen. Information om mallfilen finns [parameterfilen](#parameter-file). Mer information om REST-API för att skapa en resursgrupp finns [skapar en för malldistribution](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Observera den **läge** är inställd på **stegvis**. Om du vill köra en fullständig distribution **läge** till **Slutför**. Var försiktig när du använder fullständig läget som av misstag kan du ta bort resurser som inte finns i mallen.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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

    Om du vill logga svar innehåll, begär innehåll eller båda **debugSetting** i begäran.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    Du kan ställa in storage-konto du använder en delad åtkomsttoken signatur (SAS). Mer information finns i [delegera åtkomst med en signatur för delad åtkomst](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

5. Hämta status för malldistributionen av. Mer information finns i [få information om en malldistribution](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Distribuera om distributionen misslyckas

För distributioner som inte kan ange du att en tidigare distribution från distributionshistoriken automatiskt omdistribueras. Om du vill använda det här alternativet måste dina distributioner ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn, den aktuella distributionen misslyckades kan skriva över tidigare lyckad distribution i historiken. Du kan bara använda det här alternativet med rot nivån distributioner. Distributioner från en kapslad mall är inte tillgängliga för omdistributionen.

Om du vill distribuera om den senaste distributionen om den aktuella distributionen misslyckas, använder du:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Om du vill distribuera en viss distribution om den aktuella distributionen misslyckas, använder du:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

Den angivna distributionen måste ha slutförts.

## <a name="parameter-file"></a>Parameterfilen

Om du använder en parameterfil för att ange parametervärden under distributionen, måste du skapa en JSON-fil med ett format som liknar följande exempel:

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

Parametern-filens storlek kan inte vara mer än 64 KB.

Lägga till värdet i ett nyckelvalv om du behöver ange något känsligt värde för en parameter (till exempel ett lösenord). Hämta nyckelvalvet under distributionen som visas i föregående exempel. Mer information finns i [skicka säkra värden under distributionen av](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Nästa steg
* Läs om hur du hanterar asynkrona REST-åtgärder i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
* Ett exempel för att distribuera resurser via .NET-klientbiblioteket finns [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Om du vill definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](solution-dev-test-environments.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

