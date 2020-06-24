---
title: Exempel på Resource Manager-mallar för Azure Monitor
description: Distribuera och konfigurera Azure Monitor funktioner med Resource Manager-mallar
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: e71cd5f3989a175f883f3a08b4f55d36a154dcee
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753479"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Exempel på Resource Manager-mallar för Azure Monitor

Azure Monitor kan distribueras och konfigureras i skala med [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md). Följande artiklar innehåller exempel på mallar för olika Azure Monitor-funktioner. De här exemplen kan ändras för dina specifika krav och distribueras med valfri standard metod för distribution av Resource Manager-mallar. 

## <a name="deploying-the-sample-templates"></a>Distribuera exempel mallarna
De grundläggande stegen för att använda exemplen är:

1. Kopiera mallen och Spara som en JSON-fil.
2. Ändra parametrarna för din miljö och Spara som en JSON-fil.
4. Distribuera mallen med [valfri distributions metod för Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md). 

Använd exempelvis följande kommandon för att distribuera mallen mall och parametrar till en resurs grupp med hjälp av PowerShell eller Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista över exempel på mallar

- [Agenter](resource-manager-agent.md) – distribuera och konfigurera Log Analytics agent och diagnostiskt tillägg.
- Aviseringar
  - [Logg varnings regler](resource-manager-alerts-log.md) – aviseringar från logg frågor och Azure aktivitets logg.
  - [Mått varnings regler](resource-manager-alerts-metric.md) – aviseringar från mått som använder olika typer av logik.
- Application Insights – kommer snart.
- [Diagnostikinställningar](resource-manager-diagnostic-settings.md) – skapa diagnostiska inställningar för att vidarebefordra loggar och mått från olika resurs typer.
- [Logg frågor](resource-manager-log-queries.md) – skapa sparade logg frågor på en Log Analytics-arbetsyta.
- [Log Analytics arbets yta](resource-manager-workspace.md) – skapa Log Analytics arbets yta och konfigurera insamling av olika data källor från Log Analytics agenten.
- [Arbets böcker](resource-manager-workbooks.md) – skapa arbets böcker.
- [Azure Monitor for containers](resource-manager-container-insights.md) -onboarding-kluster som ska Azure Monitors för behållare.
- [Azure Monitor for VMS](resource-manager-vminsights.md) virtuella datorer som ska Azure Monitor for VMS.



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Resource Manager-mallar](../../azure-resource-manager/templates/overview.md)