---
title: Visa mått i realtid med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs realtidsvyn för mått utan att använda kubectl med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216599"
---
# <a name="how-to-view-metrics-in-real-time"></a>Så här visar du mätvärden i realtid

Azure Monitor för behållare Live Data (preview) funktion kan du visualisera mått om nod och pod tillstånd i ett kluster i realtid. Den emulerar direkt `kubectl top nodes`åtkomst `kubectl get pods –all-namespaces`till `kubectl get nodes` , och kommandon för att anropa, tolka och visualisera data i prestandadiagram som ingår i den här insikten. 

Den här artikeln innehåller en detaljerad översikt och hjälper dig att förstå hur du använder den här funktionen.  

>[!NOTE]
>AKS-kluster som aktiveras som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen är beroende av direkt åtkomst till Kubernetes API via en proxyserver från din webbläsare. Om du aktiverar nätverkssäkerhet för att blockera Kubernetes-API:et från den här proxyn blockeras den här trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure China. Den är för närvarande inte tillgänglig i Azure US Government.

Om du vill ha hjälp med att konfigurera eller felsöka funktionen Live Data (förhandsversion) läser du vår [installationsguide](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Hur det fungerar 

Live Data (förhandsversion) funktionen direkt tillgång till Kubernetes API, och ytterligare information om autentiseringsmodellen finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Den här funktionen utför en avsökningsåtgärd mot `/api/v1/nodes` `/apis/metrics.k8s.io/v1beta1/nodes`måttslutpunkterna (inklusive , , och `/api/v1/pods`), vilket är var femte sekund som standard. Dessa data cachelagras i webbläsaren och kartläggs i de fyra prestandadiagram som ingår i Azure Monitor för behållare på fliken **Kluster** genom att välja **Go Live (förhandsversion).** Varje efterföljande omröstning kartläggs i ett rullande fem minuters visualiseringsfönster. 

![Alternativet Gå live i klustervyn](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Avsökningsintervallet konfigureras från listrutan **Ange intervall** så att du kan ange avsökning för nya data var 1, 5, 15 och 30 sekunder. 

![Rullgardinsmenyn Gå live](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Vi rekommenderar att du ställer in avsökningsintervallet till en sekund medan du felsöker ett problem under en kort tidsperiod. Dessa begäranden kan påverka tillgängligheten och begränsningen av Kubernetes API i klustret. Konfigurera sedan om till ett längre avsökningsintervall. 

>[!IMPORTANT]
>Inga data lagras permanent under drift av den här funktionen. All information som samlas in under den här sessionen raderas omedelbart när du stänger webbläsaren eller navigerar bort från funktionen. Data finns bara kvar för visualisering i femminutersfönstret. Alla mått som är äldre än fem minuter tas också bort permanent.

Dessa diagram kan inte fästas på den senaste Azure-instrumentpanelen som du visade i live-läge.

## <a name="metrics-captured"></a>Infångade mätvärden

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Nod CPU-användning % / Nod Minne utnyttjande % 

Dessa två prestandadiagram mappas `kubectl top nodes` till en motsvarighet till att anropa och fånga resultaten av kolumnerna **CPU%** och **MEMORY%** till respektive diagram. 

![Kubectl toppnoder exempel resultat](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Diagram för cpu-utnyttjande av noder](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Nodminnesutnyttjande procent diagram](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Percentilberäkningarna fungerar i större kluster för att identifiera avvikare noder i klustret. Om du till exempel vill förstå om noder är underutnyttjade för nedskalningsändamål. Använda **Min** aggregering kan du se vilka noder har låg användning i klustret. Om du vill undersöka ytterligare väljer du fliken **Noder** och sorterar rutnätet efter CPU- eller minnesanvändning.

Detta hjälper dig också att förstå vilka noder som skickas till sina gränser och om utskalning kan krävas. Om du använder både **Max-** och **P95-aggregeringarna** kan du se om det finns noder i klustret med hög resursutnyttjande. För ytterligare undersökning växlar du återigen till fliken **Noder.**

### <a name="node-count"></a>Antal nod

Det här prestandadiagrammet mappas till en motsvarighet till att anropa och mappa `kubectl get nodes` kolumnen **STATUS** till ett diagram grupperat efter statustyper.

![Kubectl få noder exempel resultat](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Antal noder diagram](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Noder rapporteras antingen i ett **färdigt** eller **inte redo-tillstånd.** De räknas (och ett totalt antal skapas) och resultaten av dessa två aggregeringar kartläggs.
Till exempel för att förstå om dina noder faller i misslyckade tillstånd. Med aggregering av **inte klar** kan du snabbt se antalet noder i klustret som för närvarande är i läget **Inte redo.**

### <a name="active-pod-count"></a>Antal aktiva kapslar

Det här prestandadiagrammet mappas till en motsvarighet till att anropa och mappa `kubectl get pods –all-namespaces` kolumnen **STATUS** som diagrammet grupperas efter statustyper.

![Kubectl få poddar exempel resultat](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Antal noder pod diagram](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Namn på status som `kubectl` tolkas av kanske inte exakt matchar i diagrammet. 

## <a name="next-steps"></a>Nästa steg

Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av dina kluster.
