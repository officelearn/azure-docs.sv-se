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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494682"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor för behållare vanliga frågor och svar

Den här Microsoft-FAQ är en lista över vanliga frågor om Azure Monitor för behållare. Om du har ytterligare frågor om lösningen går du till den [diskussionsforum](https://feedback.azure.com/forums/34192--general-feedback) och ställa frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data i Min arbetsyta för Log Analytics?

Om du inte ser några data i Log Analytics-arbetsytan vid en viss tid dagliga har du nått gränsen på standard-500 MB eller den dagliga gränsen som vill styra mängden data som ska samlas in dagligen. När gränsen är uppfyllt för dagen, datainsamling stoppar och återupptar endast på nästa dag. Om du vill granska din dataanvändning och uppdatera till en annan prisnivå baserat på ditt förväntade användningsmönster, se [logga dataanvändning och kostnader](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är de behållare stater som anges i tabellen ContainerInventory?

Tabellen ContainerInventory innehåller information om både stoppad och köra behållare. Tabellen innehåller ett arbetsflöde i agenten som frågar docker för alla behållare (som körs och stoppad) och vidarebefordrar dessa data till Log Analytics-arbetsytan.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur löser jag **saknas prenumerationsregistreringen** fel?

Om du får felet **saknas prenumerationsregistreringen för Microsoft.OperationsManagement**, du kan lösa det genom att registrera resursprovidern **Microsoft.OperationsManagement** i den prenumeration där arbetsytan har definierats. Dokumentation för hur du gör detta finns [här](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Finns det stöd för RBAC-aktiverade AKS-kluster?

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

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur löser jag Azure AD-fel när jag aktivera live loggar? 

Du kan se följande fel: **Svaret från URL: en som anges i begäran överensstämmer inte med svars-URL som konfigurerats för programmet: ' < program-ID\>'**. Lösning som löste det finns i artikeln [visa behållarens loggar realtid med Azure Monitor för behållare](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter?

Om när du aktiverar Azure Monitor för behållare för ett AKS-kluster kan du ta bort Log Analytics-arbetsytan i klustret har skickat data, när du försöker uppgradera klustret kommer att misslyckas. Undvik detta genom kommer du behöva inaktivera övervakning och sedan återaktivera det refererar till en annan giltig arbetsyta i din prenumeration. Den ska bearbeta och slutförs när du försöker uppgradera klustret igen.  

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster, granska [hur att publicera Azure övervakar för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning. 