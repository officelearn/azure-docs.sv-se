---
title: 'Snabb start: Konfigurera NSG flödes loggar med Azure Resource Manager mall'
description: Lär dig hur du aktiverar NSG Flow-loggar via programmering med hjälp av en Azure Resource Manager-mall (ARM-mall) och Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042750"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Snabb start: Konfigurera NSG flödes loggar med en ARM-mall

I den här snabb starten aktiverar du [NSG Flow-loggar](network-watcher-nsg-flow-logging-overview.md) med en [Azure Resource Manager](../azure-resource-manager/management/overview.md) -mall (arm-mall) och Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vi börjar med att tillhandahålla en översikt över egenskaperna för NSG flödes logg objekt, följt av en liten exempel-mall. Sedan distribuerar vi mallen med en lokal PowerShell-instans.

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure** . Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Flera resurser definieras i mallen:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG flödes loggar objekt

Objektet NSG Flow-loggar med alla parametrar visas nedan. En fullständig översikt över egenskaperna finns i [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Om du vill skapa en `Microsoft.Network/networkWatchers/flowLogs` resurs lägger du till ovanstående JSON i avsnittet resurser i mallen.

## <a name="creating-your-template"></a>Skapa din mall

Om du använder ARM-mallar för första gången kan du lära dig mer om dem med hjälp av länkarna nedan.

- [Distribuera resurser med ARM-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Nedan visas ett exempel på en fullständig mall som är den enklaste versionen med minsta parametrar som skickas för att ställa in NSG flödes loggar. Fler exempel finns i den här [instruktions guiden](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Exempel** : mallen nedan aktiverar NSG flödes loggar på en mål-NSG och lagrar dem i ett angivet lagrings konto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Resurs namnet har formatet _Överordnad Resource_Child resurs_ . Här är den överordnade resursen den regionala Network Watcher-instansen (format: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centraluseuap)
> - `targetResourceId` är resurs-ID: t för mål-NSG.
> - `storageId` är resurs-ID: t för mål lagrings kontot.

## <a name="deploy-the-template"></a>Distribuera mallen

I den här självstudien förutsätter vi att du har en befintlig resurs grupp och en NSG. du kan aktivera flödes inloggning.
Du kan spara alla ovanstående exempel mallar lokalt som `azuredeploy.json` . Uppdatera egenskapsvärdena så att de pekar på giltiga resurser i din prenumeration.

Distribuera mallen genom att köra följande kommando i PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Ovanstående kommandon distribuerar en resurs till resurs gruppen NetworkWatcherRG och inte den resurs grupp som innehåller NSG

## <a name="validate-the-deployment"></a>Verifiera distributionen

Det finns ett par olika sätt att kontrol lera om distributionen har slutförts. Din PowerShell-konsol bör visas `ProvisioningState` som `Succeeded` . Dessutom kan du gå till [Portal sidan NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) för att bekräfta dina ändringar. Om det uppstod problem med distributionen läser du [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Rensa resurser

Med Azure kan du ta bort resurser via `Complete` distributions läget. Om du vill ta bort en flödes loggar resurs anger du en distribution i `Complete` läget utan att inkludera den resurs som du vill ta bort. Läs mer om [hela distributions läget](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Du kan också inaktivera en NSG Flow-logg från Azure Portal enligt stegen nedan:

1. Logga in på Azure-portalen
1. Välj **Alla tjänster** längst upp till vänster på portalen. Skriv _Network Watcher_ i **filterrutan** . När **Network Watcher** visas i sökresultatet väljer du posten.
1. Under **loggar** väljer du **NSG flödes loggar** .
1. I listan över NSG: er väljer du den NSG som du vill inaktivera flödes loggar för.
1. Under **Inställningar för flödes loggar** anger du logg status **Off** för flöden.
1. Rulla nedåt och välj **Spara** .

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du aktiverat NSG Flow-loggar. Nu måste du lära dig hur du visualiserar dina NSG flödes data med:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure-Trafikanalys](traffic-analytics.md)
