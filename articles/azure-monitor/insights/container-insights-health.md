---
title: Övervaka Kubernetes klusterhälsa med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan visa och analysera hälsotillståndet för dina AKS- och icke-AKS-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843998"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Förstå Kubernetes klusterhälsa med Azure Monitor för behållare

Med Azure Monitor för behållare övervakar och rapporterar den hälsostatus för de hanterade infrastrukturkomponenterna och alla noder som körs på alla Kubernetes-kluster som stöds av Azure Monitor för behållare. Den här upplevelsen sträcker sig bortom klusterhälsostatus som beräknas och rapporteras i [flerklustervyn](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), där du nu kan förstå om en eller flera noder i klustret är resursbegränsade eller att en nod eller pod inte är tillgänglig som kan påverka ett program som körs i klustret baserat på kurerade mått.

>[!NOTE]
>Hälsofunktionen är i offentlig förhandsversion just nu.
>

Information om hur du aktiverar Azure Monitor för behållare finns [i Onboard Azure Monitor för behållare](container-insights-onboard.md).

>[!NOTE]
>Kontrollera att aks-motorkluster stöds genom att kontrollera att de uppfyller följande:
>- Den använder den senaste versionen av [HELM-klienten](https://helm.sh/docs/using_helm/).
>- Den containeriserade agentversionen är *microsoft/oms:ciprod11012019*. Om du vill uppgradera agenten finns i [uppgraderingsagenten i Kubernetes-klustret](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Översikt

I Azure Monitor för behållare ger funktionen Hälsa (förhandsversion) proaktiv hälsoövervakning av kubernetes-klustret som hjälper dig att identifiera och diagnostisera problem. Det ger dig möjlighet att visa betydande problem som upptäckts. Övervakar utvärdera hälsotillståndet för klustret körs på behållaren agent i klustret och hälsodata skrivs till **KubeHealth** tabellen i logganalysarbetsytan. 

Kubernetes klusterhälsa baseras på ett antal övervakningsscenarier som organiseras av följande Kubernetes-objekt och abstraktioner:

- Kubernetes-infrastruktur – tillhandahåller en sammanslagning av Kubernetes API-server, ReplicaSets och DaemonSets som körs på noder som distribueras i klustret genom att utvärdera CPU- och minnesanvändning och en pods-tillgänglighet

    ![Kubernetes hälsosammanslagningsvy för infrastruktur](./media/container-insights-health/health-view-kube-infra-01.png)

- Noder - ger en sammanslagning av nodpoolerna och tillståndet för enskilda noder i varje pool, genom att utvärdera CPU- och minnesanvändning och en nods status som rapporterats av Kubernetes.

    ![Hälsosammadrfällningsvyn noder](./media/container-insights-health/health-view-nodes-01.png)

För närvarande stöds endast status för en virtuell kubelet. Hälsotillståndet för CPU- och minnesanvändning av virtuella kubletnoder rapporteras som **Okänd**, eftersom en signal inte tas emot från dem.

Alla övervakare visas i en hierarkisk layout i fönstret Hälsohierarki, där en aggregerad övervakare som representerar Kubernetes-objektet eller abstraktionen (det vill än Kubernetes-infrastruktur eller Noder) är den översta övervakaren som återspeglar den kombinerade hälsan för alla underordnade övervakare. De viktigaste övervakningsscenarierna som används för att härleda hälsa är:

* Utvärdera CPU-användning från noden och behållaren.
* Utvärdera minnesanvändning från noden och behållaren.
* Status för poddar och noder baserat på beräkning av deras färdiga tillstånd som rapporterats av Kubernetes.

Följande ikoner används för att visa status:

|Ikon|Betydelse|  
|--------|-----------|  
|![Grön kryssruteikon är lika med felfri](./media/container-insights-health/healthyicon.png)|Fungerar, hälsotillståndet är bra (grönt)|  
|![Gul triangel och utropstecken innebär en varning](./media/container-insights-health/warningicon.png)|Varning (gul)|  
|![Röd knapp med vitt X anger kritiskt tillstånd](./media/container-insights-health/criticalicon.png)|Kritisk (röd)|  
|![Ikon för nedtonad uttonad](./media/container-insights-health/grayicon.png)|Okänd (grå)|  

## <a name="monitor-configuration"></a>Bildskärmskonfiguration

Information om hur och konfigurerar varje bildskärm som stöder Azure Monitor för behållare Hälsofunktion finns i [konfigurationsguide för hälsoövervakare](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Visa hälsotillstånd för ett AKS- eller icke-AKS-kluster

Åtkomst till Azure Monitor for containers Health (preview) funktionen är tillgänglig direkt från ett AKS-kluster genom att välja **Insikter** från den vänstra rutan i Azure-portalen. Under avsnittet **Insikter** väljer du **Behållare**. 

Om du vill visa hälsotillstånd från ett icke-AKS-kluster, det vill veta ett AKS-motorkluster som finns lokalt eller på Azure Stack, väljer du **Azure Monitor** från den vänstra rutan i Azure-portalen. Under avsnittet **Insikter** väljer du **Behållare**.  På sidan med flera kluster väljer du icke-AKS-klustret i listan.

Välj **Hälsa**på sidan **Kluster** i Azure Monitor för behållare .

![Exempel på klusterhälsainstrumentpanel](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Granska klusterhälsa

När sidan Hälsa öppnas väljs **kubernetes-infrastruktur** som standard i rutnätet **Hälsoaspekt.**  Rutnätet sammanfattar det aktuella hälsosamma sammanslagningstillståndet för Kubernetes infrastruktur och klusternoder. Om du väljer antingen hälsoaspekten uppdateras resultaten i fönstret Hälsohierarki (det vill säga mittrutan) och alla underordnade övervakare visas i en hierarkisk layout som visar deras aktuella hälsotillstånd. Om du vill visa mer information om en beroende bildskärm kan du välja en och ett egenskapsfönster visas automatiskt till höger på sidan. 

![Egenskapsfönstret Klusterhälsa](./media/container-insights-health/health-view-property-pane.png)

I egenskapsfönstret får du lära dig följande:

- På fliken **Översikt** visas det aktuella tillståndet för den valda övervakaren, när övervakaren senast beräknades och när den senaste tillståndsändringen inträffade. Ytterligare information visas beroende på vilken typ av övervakare som valts i hierarkin.

    Om du väljer en aggregerad övervakare i fönstret Hälsohierarki visas en sammanslagning av det totala antalet underordnade övervakare i hierarkin under fliken **Översikt** i egenskapsfönstret och hur många aggregerade övervakare som är i kritiskt, varnings- och felfritt tillstånd. 

    ![Fliken Översiktsfönster för hälsogenskapsmall för aggregerad övervakare](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Om du väljer en enhetsövervakare i fönstret Hälsohierarki visas också under **Senaste tillståndsändring** av de tidigare exemplen som beräknats och rapporterats av behållarens agent inom de senaste fyra timmarna. Detta baseras på enheten övervakar beräkningen för att jämföra flera på varandra följande värden för att bestämma dess tillstånd. Om du till exempel har valt övervakaren för redo *lägesenhet* för pod visas de två sista exemplen som styrs av parametern *ConsecutiveSamplesForStateTransition*. Mer information finns i den detaljerade beskrivningen av [enhetsövervakare](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Fliken Översiktsfönster för hälsoegenskap](./media/container-insights-health/health-overview-unit-monitor.png)

    Om tiden som rapporteras av **Senaste tillståndsändring** är en dag eller äldre, är det resultatet av inga ändringar i tillståndet för övervakaren. Men om det sista exemplet som tas emot för en enhetsövervakare är mer än fyra timmar gammalt, indikerar detta sannolikt att behållaren inte har skickat data. Om agenten vet att det finns en viss resurs, till exempel en nod, men den inte har tagit emot data från nodens CPU- eller minnesanvändningsövervakare (som ett exempel), är övervakarens hälsotillstånd inställt på **Okänd**.  

- På fliken**Config** visas standardinställningarna för konfigurationsparameter (endast för enhetsövervakare, inte aggregerade övervakare) och deras värden.
- På fliken **Kunskap** innehåller den information som förklarar bildskärmens beteende och hur den utvärderar för det felaktiga tillståndet.

Övervakningsdata på den här sidan uppdateras inte automatiskt och du måste välja **Uppdatera** högst upp på sidan för att se det senaste hälsotillståndet som tagits emot från klustret.

## <a name="next-steps"></a>Nästa steg

Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill visa fördefinierade frågor och exempel som du kan utvärdera eller anpassa för att avisera, visualisera eller analysera kluster.
