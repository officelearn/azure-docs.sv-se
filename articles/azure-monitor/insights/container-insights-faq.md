---
title: Azure Monitor för behållare vanliga frågor och svar | Microsoft Docs
description: Azure Monitor för behållare är en lösning som övervakar hälsotillståndet för dina AKS-kluster och Container Instances i Azure. Den här artikeln innehåller vanliga frågor och svar.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960524"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor för behållare vanliga frågor och svar
Den här Microsoft-FAQ är en lista över vanliga frågor om Azure Monitor för behållare. Om du har ytterligare frågor om lösningen går du till den [diskussionsforum](https://feedback.azure.com/forums/34192--general-feedback) och ställa frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>Jag kan inte finns några data i Log Analytics-arbetsytan vid en viss tidpunkt varje dag. Hur löser jag det? 

Du kanske har nått gränsen på standard-500 MB eller den dagliga gränsen som vill styra mängden data som ska samlas in dagligen. När gränsen är uppfyllt för dagen, datainsamling stoppar och återupptar endast på nästa dag. Om du vill granska din dataanvändning och uppdatera till en annan prisnivå baserat på ditt förväntade användningsmönster, se [logga dataanvändning och kostnader](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Vad är status för behållare som anges i tabellen ContainerInventory?
Tabellen ContainerInventory innehåller information om både stoppad och köra behållare. Tabellen innehåller ett arbetsflöde i agenten som frågar docker för alla behållare (som körs och stoppad) och vidarebefordrar dessa data till Log Analytics-arbetsytan.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Hur löser jag fel som rör **saknas prenumerationsregistreringen för Microsoft.OperationsManagement**?
Lös felet genom att registrera resursprovidern **Microsoft.OperationsManagement** i prenumerationen där arbetsytan har definierats. Dokumentation för hur du gör detta finns [här](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Azure Monitor för behållare omfattar support för RBAC-aktiverade AKS-kluster?
Behållarövervakning-lösningen stöder inte RBAC, men det stöds med Azure Monitor för behållare. Lösningens detaljsida kan inte visa rätt information i blad som visar data för klustren.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur aktiverar jag Logginsamling för behållare i namnområdet kube system via Helm?
Logginsamling från behållare i namnområdet kube system är inaktiverad som standard. Logginsamling kan aktiveras genom att ange en miljövariabel på omsagent. Mer information finns i den [Azure Monitor för behållare](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub-sidan. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur uppdaterar jag omsagent till den senaste versionen?
Om du vill lära dig mer om att uppgradera agenten, se [agenthantering](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Hur aktiverar jag loggning för flera rader?
Azure Monitor för behållare stöder inte för närvarande med flera rader loggning, men det finns lösningar tillgängliga. Du kan konfigurera alla tjänster att skriva i JSON-format och sedan Docker/Moby ska skriva dem som en enda rad.

Du kan till exempel omsluter din logg som ett JSON-objekt som visas i exemplet nedan för ett node.js-exempelprogram:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data kommer att se ut som i följande exempel i Azure Monitor för loggar när du frågar efter den:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

För detaljerad information om problemet, granska följande [github link](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Hur löser jag Azure Active Directory-fel när jag aktivera live loggar? 
Du kan se följande fel: **Svaret från URL: en som anges i begäran överensstämmer inte med svars-URL som konfigurerats för programmet: ' < program-ID\>'**. Lösning som löste det finns i artikeln [visa behållarens loggar realtid med Azure Monitor för behållare](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Nästa steg
Om du vill börja övervaka ditt AKS-kluster, granska [hur att publicera Azure övervakar för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning. 