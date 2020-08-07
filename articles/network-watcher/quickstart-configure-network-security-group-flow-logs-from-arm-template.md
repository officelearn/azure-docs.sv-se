---
title: 'Snabb start: Konfigurera NSG flödes loggar med Azure Resource Manager mall'
description: Lär dig hur du aktiverar NSG Flow-loggar via programmering med hjälp av en Azure Resource Manager-mall och Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986325"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Snabb start: Konfigurera NSG flödes loggar från ARM-mallen

I den här snabb starten aktiverar du [NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) via programmering med hjälp av en [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) -mall (arm-mall) och Azure PowerShell. 

Vi börjar med att tillhandahålla en översikt över egenskaperna för NSG flödes logg objekt, följt av en liten exempel-mall. Sedan distribuerar vi mallen med en lokal PowerShell-instans.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="nsg-flow-logs-object"></a>NSG flödes loggar objekt

Objektet NSG Flow-loggar med alla parametrar visas nedan.
För en fullständig översikt över egenskaperna kan du läsa [mallen för NSG Flow-referenser](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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
Om du vill skapa en resurs för Microsoft. Network/networkWatchers/flowLogs lägger du till ovanstående JSON i avsnittet resurser i mallen.


## <a name="creating-your-template"></a>Skapa din mall

Om du använder Azure Resource Manager mallar för första gången kan du läsa mer om dem med hjälp av länkarna nedan.

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Självstudie: skapa och distribuera din första Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Nedan visas ett exempel på en fullständig mall som är den enklaste versionen med minsta parametrar som skickas för att ställa in NSG flödes loggar. För fler exempel går du till den här [länken](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Exempel**: mallen nedan aktiverar NSG flödes loggar på en mål-NSG och lagrar dem i ett angivet lagrings konto.

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
> * Resurs namnet har formatet "överordnad Resource_Child resurs". Här är den överordnade resursen den regionala Network Watcher-instansen (format: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centraluseuap)
> * targetResourceId är resurs-ID för mål NSG
> * storageId är resurs-ID för mål lagrings kontot


## <a name="deploying-your-azure-resource-manager-template"></a>Distribuera din Azure Resource Manager-mall

I den här självstudien förutsätter vi att du har en befintlig resurs grupp och en NSG. du kan aktivera flödes inloggning.
Du kan spara alla ovanstående exempel mallar lokalt som `azuredeploy.json` . Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i din prenumeration.

Distribuera mallen genom att köra följande kommando i PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Ovanstående kommandon distribuerar en resurs till resurs gruppen NetworkWatcherRG och inte den resurs grupp som innehåller NSG


## <a name="validate-the-deployment"></a>Verifiera distributionen

Det finns ett par olika sätt att kontrol lera om distributionen har slutförts. PowerShell-konsolen ska Visa "ProvisioningState" som "lyckades". Dessutom kan du gå till [Portal sidan NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta dina ändringar. Om det uppstod problem med distributionen kan du ta en titt på [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Tar bort resursen
Med Azure kan du ta bort resurser genom att slutföra distributions läget. Om du vill ta bort en flödes loggar resurs anger du en distribution i slutfört läge utan att inkludera den resurs som du vill ta bort. Läs mer om [hela distributions läget](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Du kan också inaktivera en NSG Flow-logg från Azure Portal enligt stegen nedan:
1. Logga in på Azure-portalen
2. Välj **Alla tjänster** längst upp till vänster på portalen. Skriv *Network Watcher* i **filterrutan**. När **Network Watcher** visas i sökresultatet väljer du posten.
3. Under **loggar**väljer du **NSG flödes loggar**
4. I listan över NSG: er väljer du den NSG som du vill inaktivera flödes loggar för
5. Under **flödes loggar inställningar**anger du flöden logg status som **av**
6. Rulla nedåt och välj **Spara**

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du aktiverat NSG Flow-loggar. Nu måste du lära dig hur du visualiserar dina NSG flödes data med: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure-Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
