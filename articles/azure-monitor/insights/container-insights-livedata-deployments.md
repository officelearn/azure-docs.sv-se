---
title: Visa Azure Monitor för behållare Distributioner (förhandsversion) | Microsoft-dokument
description: I den här artikeln beskrivs realtidsvyn för Kubernetes-distributioner utan att använda kubectl i Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404780"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Så här visar du distributioner (förhandsversion) i realtid

Med Azure Monitor för behållare emulerar funktionen visa distributioner (förhandsversion) direkt åtkomst till Kubernetes deployment-objekt i realtid genom att exponera `kubeclt get deployments` kommandon och `kubectl describe deployment {your deployment}` kommandon. 

>[!NOTE]
>AKS-kluster som aktiveras som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen är beroende av direkt åtkomst till Kubernetes API via en proxyserver från din webbläsare. Om du aktiverar nätverkssäkerhet för att blockera Kubernetes-API:et från den här proxyn blockeras den här trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure China. Den är för närvarande inte tillgänglig i Azure US Government.

Mer information finns i Kubernetes-dokumentationen om [distributioner](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Hur det fungerar

Live Data (förhandsversion) funktionen direkt tillgång till Kubernetes API, och ytterligare information om autentiseringsmodellen finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funktionen Distributioner (förhandsversion) utför en engångsbelastning (uppdateringsbar) `/apis/apps/v1/deployments`mot slutpunkten för distributioner . Det låter dig välja en viss distribution och läsa in beskriva `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`detaljer för den specifika distributionen mot distributionslutpunkten . 

Om du väljer **Uppdatera** längst upp till vänster på sidan uppdateras distributionslistan. Detta simulerar att `kubectl` kommandot körs på för att köras igen. 

>[!IMPORTANT]
>Inga data lagras permanent under drift av den här funktionen. All information som samlas in under sessionen tas bort när du stänger webbläsaren eller navigerar bort från den.  

>[!NOTE]
>Du kan inte fästa Live Data (Preview) data från konsolen till en Azure-instrumentpanel.

## <a name="deployments-describe"></a>Distributioner beskriver

Om du vill visa Beskriv information för `kubectl describe deployment`en distribution, vilket motsvarar, utför du följande steg.

1. I Azure-portalen bläddrar du till AKS-klusterresursgruppen och väljer din AKS-resurs.

2. Välj **Insikter**under **Övervakning** till vänster på AKS-klusterinstrumentpanelen. 

3. Välj fliken **Distributioner (förhandsgranskning).**

    ![Vyn Distributioner i Azure-portalen](./media/container-insights-livedata-deployments/deployment-view.png)

Vyn visar en lista över alla distributioner som körs tillsammans med namnområdet och `kubectl get deployments –all-namespaces`annan detaljerad information, vilket efterliknar körningen av kommandot . Du kan sortera resultaten genom att markera någon av kolumnerna. 

![Information om fönstret Egenskaper för distributioner](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

När du väljer en distribution i listan visas automatiskt ett egenskapsfönster till höger på sidan. Den visar information om den valda distributionen som `kubectl describe deployment {deploymentName}`du skulle visa om du körde kommandot . Du kanske har märkt att beskriva information saknas några detaljer. Framför allt saknas **mallen.** Om du väljer fliken **Rå** kan du navigera till den otolkade beskrivsinformationen.  

![Egenskaper för distributioner råinformation om råinformation](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

När du granskar distributionsinformation kan du se behållarloggar och händelser i realtid. Välj **visa live-konsolen** så visas konsolfönstret Live Data (preview) under datarutnätet för distributioner där du kan visa liveloggdata i en kontinuerlig ström. Om hämtningsstatusindikatorn visar en grön bock, som ligger längst till höger i fönstret, betyder det att data kan hämtas och att strömmas till konsolen.

Du kan också filtrera efter namnområdes- eller klusternivåhändelser. Mer information om visningsdata i realtid i konsolen finns i [Visa livedata (förhandsgranskning) med Azure Monitor för behållare](container-insights-livedata-overview.md). 

![Distributioner visa live-data i konsolen](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Nästa steg

- Information om hur du fortsätter att lära dig hur du använder Azure Monitor och övervakar andra aspekter av AKS-klustret finns i [Visa Azure Kubernetes Service health](container-insights-analyze.md).

- Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av dina kluster.
