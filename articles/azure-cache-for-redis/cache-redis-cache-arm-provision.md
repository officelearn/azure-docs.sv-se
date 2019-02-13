---
title: Etablera en Azure Cache för Redis med hjälp av Azure Resource Manager | Microsoft Docs
description: Använd Azure Resource Manager-mall för att distribuera en Azure Cache för Redis.
services: app-service
documentationcenter: ''
author: yegu-ms
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 7cdf0263824e59e17a77289d335e3b6a5f208969
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110670"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Skapa en Azure Cache för Redis med hjälp av en mall

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet får du lära dig hur du skapar en Azure Resource Manager-mall som distribuerar en Azure Cache för Redis. Cacheminnet kan användas med ett befintligt lagringskonto för att hålla diagnostikdata. Du också lära dig hur du definierar vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

För närvarande kan delas diagnostikinställningar för alla cacheminnen i samma region för en prenumeration. Uppdaterar en cache i regionen påverkar alla cacheminnen i regionen.

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mer information om JSON-syntax och egenskaper för cache-resurstyper, se [Microsoft.Cache resurstyper](/azure/templates/microsoft.cache/allversions).

Läs den fullständiga mallen, [Azure Cache för Redis-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Resource Manager-mallar för den nya [premiumnivån](cache-premium-tier-intro.md) är tillgängliga. 
> 
> * [Skapa en Premium Azure Cache för Redis med klustring](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Skapa Premium Azure Cache för Redis med datapersistens](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Skapa Premium Azure Cache för Redis med virtuellt nätverk och valfritt kluster](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Om du vill söka efter de senaste mallarna, se [Azure-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/) och Sök efter `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>Vad du ska distribuera
I den här mallen distribuerar du en Azure Cache för Redis som använder ett befintligt lagringskonto för diagnostikdata.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter parametrar som innehåller alla parametervärden.
Du bör definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Platsen för Azure Cache för Redis. För bästa prestanda bör du använda samma plats som appen som ska användas med cachen.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Namnet på det befintliga lagringskontot ska användas för diagnostik. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Ett booleskt värde som anger om åtkomst ska tillåtas via icke-SSL-portar.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Ett värde som anger om diagnostik är aktiverat. Använd ON eller OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Resurser som ska distribueras
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Skapar Azure Cache för Redis.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


