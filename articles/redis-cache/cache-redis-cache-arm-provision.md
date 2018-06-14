---
title: Etablera ett Redis-Cache med Azure Resource Manager | Microsoft Docs
description: Använd Azure Resource Manager-mall för att distribuera ett Azure Redis-Cache.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: b26116b974abbfe410b0a6ebc0186d73f4eea1bf
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910749"
---
# <a name="create-a-redis-cache-using-a-template"></a>Skapa en Redis Cache med hjälp av en mall
Lär dig hur du skapar en Azure Resource Manager-mall som distribuerar ett Azure Redis-Cache i det här avsnittet. Cachen kan användas med ett befintligt lagringskonto för att hålla diagnostikdata. Du också lära dig hur du definierar vilka resurser har distribuerats och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

För närvarande är diagnostikinställningar gemensamma för alla i samma region för en prenumeration. Uppdatera en cacheminnet i region påverkar alla cacheminnen i region.

Mer information om hur du skapar mallar finns [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Den fullständiga mallen finns [Redis-Cache mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Resource Manager-mallar för den nya [premiumnivån](cache-premium-tier-intro.md) är tillgängliga. 
> 
> * [Skapa en Premium Redis-Cache med kluster](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Skapa Premium Redis-Cache med-datapersistence](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Skapa Premium Redis-Cache med virtuella nätverk och valfritt kluster](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Om du vill söka efter de senaste mallarna finns [Azure-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/) och Sök efter `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Vad du ska distribuera
I den här mallen ska du distribuera ett Azure Redis-Cache som använder ett befintligt lagringskonto för diagnostikdata.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter parametrar som innehåller alla parametervärdena.
Du bör definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Platsen för Redis-Cache. För bästa prestanda bör du använda samma plats som appen som ska användas med cachen.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Namnet på det befintliga lagringskontot för diagnostik. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Ett booleskt värde som anger om du vill tillåta åtkomst via SSL-portar.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Ett värde som anger om diagnostik är aktiverad. Använd ON eller OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Resurser som ska distribueras
### <a name="redis-cache"></a>Redis Cache
Skapar Azure Redis-Cache.

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
          "apiVersion": "2015-07-01",
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
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


