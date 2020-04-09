---
title: Network Watcher - Skapa NSG-flödesloggar med hjälp av en Azure Resource Manager-mall
description: Använd en Azure Resource Manager-mall och PowerShell för att enkelt konfigurera NSG-flödesloggar.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891466"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurera NSG-flödesloggar från en Azure Resource Manager-mall

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) är Azures inbyggda och kraftfulla sätt att hantera din [infrastruktur som kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Den här artikeln visar hur du aktiverar [NSG-flödesloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmässigt med hjälp av en Azure Resource Manager-mall och Azure PowerShell. Vi börjar med att ge en översikt över egenskaperna för NSG Flow Log-objektet, följt av några exempelmallar. Sedan distribuerar vi mallen med hjälp av en lokal PowerShell-instans.


## <a name="nsg-flow-logs-object"></a>NSG-flödesloggar objekt

NSG Flow Logs-objektet med alla parametrar visas nedan.
Om du vill ha en fullständig översikt över egenskaperna kan du läsa [mallreferensen för NSG-flödesloggar](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Om du vill skapa en Microsoft.Network/networkWatchers/flowLogs-resurs lägger du till ovanstående JSON i avsnittet resurser i mallen.


## <a name="creating-your-template"></a>Skapa mallen

Om du använder Azure Resource Manager-mallar för första gången kan du läsa mer om dem med hjälp av länkarna nedan.

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Självstudiekurs: Skapa och distribuera din första Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Nedan följer två exempel på fullständiga mallar för att ställa in NSG Flow Logs.

**Exempel 1:** Den enklaste versionen av ovanstående med minsta parametrar passerade. Mallen nedan gör det möjligt för NSG-flödesloggar på en mål-NSG och lagrar dem i ett visst lagringskonto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Namnet på resursen har formatet "Överordnad resurs>/Underordnad resurs". Här är den överordnade resursen den regionala Network Watcher-instansen (Format: NetworkWatcher_<RegionName>. Exempel: NetworkWatcher_centraluseuap)
> * targetResourceId är resurs-ID för målet NSG
> * storageId är resurs-ID för mållagringskontot

**Exempel 2:** Följande mallar som aktiverar NSG-flödesloggar (version 2) med kvarhållning i 5 dagar. Aktivera Traffic Analytics med ett bearbetningsintervall på 10 minuter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Distribuera din Azure Resource Manager-mall

Den här självstudien förutsätter att du har en befintlig resursgrupp och en NSG som du kan aktivera Flow-loggning på.
Du kan spara någon av ovanstående exempelmallar lokalt som `azuredeploy.json`. Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i prenumerationen.

Om du vill distribuera mallen kör du följande kommando i PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verifiera distributionen

Det finns ett par sätt att kontrollera om distributionen har lyckats. PowerShell-konsolen ska visa "Etableringstate" som "lyckades". Dessutom kan du besöka [portalsidan för NSG-flödesloggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta dina ändringar. Om det fanns problem med distributionen kan du ta en titt på [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Ta bort din resurs
Azure aktiverar resursborttagning via distributionsläget "Slutför". Om du vill ta bort en flödesloggresurs anger du en distribution i fullständigt läge utan att inkludera den resurs som du vill ta bort. Läs mer om [läget Slutför distribution](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar dina NSG Flow-data med hjälp av:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
