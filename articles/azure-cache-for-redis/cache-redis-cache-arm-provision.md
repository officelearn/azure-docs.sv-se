---
title: Distribuera Azure-cache för Redis med Azure Resource Manager
description: Lär dig hur du använder en Azure Resource Manager-mall för att distribuera en Azure Cache för Redis-resurs. Mallar finns för vanliga scenarier.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412409"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Skapa en Azure-cache för Redis med hjälp av en mall

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet får du lära dig hur du skapar en Azure Resource Manager-mall som distribuerar en Azure-cache för Redis. Cachen kan användas med ett befintligt lagringskonto för att behålla diagnostikdata. Du lär dig också hur du definierar vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

För närvarande delas diagnostikinställningar för alla cacheminnen i samma region för en prenumeration. Om du uppdaterar en cache i regionen påverkas alla andra cacheminnen i regionen.

Mer information om hur du skapar mallar finns i [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md). Mer information om JSON-syntaxen och egenskaperna för cacheresurstyper finns i [Microsoft.Cache-resurstyper](/azure/templates/microsoft.cache/allversions).

Den fullständiga mallen finns i [Azure Cache för Redis-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Resource Manager-mallar för den nya [Premium-nivån](cache-premium-tier-intro.md) är tillgängliga. 
> 
> * [Skapa en Premium Azure-cache för Redis med klustring](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Skapa Premium Azure-cache för Redis med databeständighet](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Skapa Premium Redis-cache som distribueras till ett virtuellt nätverk](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Information om hur du söker efter de senaste `Azure Cache for Redis`mallarna finns i [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/) och söker efter .
> 
> 

## <a name="what-you-will-deploy"></a>Vad du ska distribuera
I den här mallen distribuerar du en Azure-cache för Redis som använder ett befintligt lagringskonto för diagnostikdata.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt med namnet Parametrar som innehåller alla parametervärden.
Du bör definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Platsen för Azure Cache för Redis. För bästa prestanda bör du använda samma plats som appen som ska användas med cacheminnet.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>befintligaDiagnostikStorageAccountName
Namnet på det befintliga lagringskontot som ska användas för diagnostik. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Ett booleskt värde som anger om åtkomst ska tillåtas via icke-SSL-portar.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnostikStatus
Ett värde som anger om diagnostik är aktiverat. Använd PÅ eller AV.

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
Skapar Azure-cache för Redis.

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
