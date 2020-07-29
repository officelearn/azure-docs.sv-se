---
title: Övervaka Kubernetes kluster hälsa med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du kan visa och analysera hälso tillståndet för dina AKS-och icke-AKS-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649570"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Förstå Kubernetes-kluster hälsa med Azure Monitor för behållare

Med Azure Monitor för behållare övervakas och rapporteras hälso status för de hanterade infrastruktur komponenterna och alla noder som körs på ett Kubernetes-kluster som stöds av Azure Monitor for containers. Den här upplevelsen överskrider kluster hälso statusen som beräknas och rapporteras i [vyn multi-Cluster](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), där nu kan du förstå om en eller flera noder i klustret är begränsade eller om en nod eller Pod inte är tillgänglig som kan påverka ett program som körs i klustret baserat på granskade mått.

>[!NOTE]
>Hälso funktionen kommer att övergå till en privat för hands version i slutet av juni 2020. För ytterligare information, se följande [meddelande om Azure-uppdateringar](https://azure.microsoft.com/updates/ci-health-limited-preview/).
>

Information om hur du aktiverar Azure Monitor för behållare finns i [Onboard Azure Monitor for containers](container-insights-onboard.md).

>[!NOTE]
>Om du vill ha stöd för AKS-motor kluster kontrollerar du att det uppfyller följande:
>- Den använder den senaste versionen av Helm- [klienten](https://helm.sh/docs/using_helm/).
>- Agent versionen för behållare är *Microsoft/OMS: ciprod11012019*. Information om hur du uppgraderar agenten finns i [Uppgradera agent på Kubernetes-kluster](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent).
>

## <a name="overview"></a>Översikt

I Azure Monitor för behållare ger funktionen Health (förhands granskning) proaktiv hälso övervakning av ditt Kubernetes-kluster för att hjälpa dig att identifiera och diagnostisera problem. Det ger dig möjlighet att Visa viktiga problem som har identifierats. Övervakare utvärderar hälso tillståndet för klustret som körs på behållarens agent i klustret och hälso data skrivs till tabellen **KubeHealth** i din Log Analytics-arbetsyta. 

Kubernetes-klustrets hälsa är baserat på ett antal övervaknings scenarier ordnade efter följande Kubernetes-objekt och abstraktioner:

- Kubernetes-infrastruktur – innehåller en sammanställning av Kubernetes API-servern, ReplicaSets och DaemonSets som körs på noder som distribueras i klustret genom att utvärdera processor-och minnes användning och en poddar-tillgänglighet

    ![Vy över Kubernetes Infrastructure Health Health](./media/container-insights-health/health-view-kube-infra-01.png)

- Noder – tillhandahåller en sammanställning av nodernas pooler och status för enskilda noder i varje pool, genom att utvärdera processor-och minnes användning och en nods status som rapporteras av Kubernetes.

    ![Vy över hälso tillstånd för noder](./media/container-insights-health/health-view-nodes-01.png)

För närvarande stöds endast statusen för en virtuell kubelet. Hälso tillståndet för processor-och minnes användning för virtuella kublet-noder rapporteras som **Okänt**, eftersom en signal inte tas emot från dem.

Alla Övervakare visas i en hierarkisk layout i fönstret hälsohierarki, där en sammanställd Övervakare som representerar Kubernetes-objektet eller abstraktionen (dvs. Kubernetes-infrastruktur eller noder) är den översta övervakaren som återspeglar den kombinerade hälsan för alla beroende underordnade övervakare. De nyckel övervaknings scenarier som används för att härleda hälsan är:

* Utvärdera processor användningen från noden och behållaren.
* Utvärdera minnes användningen från noden och behållaren.
* Status för poddar och noder baserat på beräkningen av deras färdiga tillstånd som rapporteras av Kubernetes.

Följande ikoner används för att visa status:

|Ikon|Innebörd|  
|--------|-----------|  
|![Grön kryssruteikon är lika med felfri](./media/container-insights-health/healthyicon.png)|Fungerar, hälsotillståndet är bra (grönt)|  
|![Gul triangel och utropstecken innebär en varning](./media/container-insights-health/warningicon.png)|Varning (gul)|  
|![Röd knapp med vitt X anger kritiskt tillstånd](./media/container-insights-health/criticalicon.png)|Kritisk (röd)|  
|![Nedtonad ikon](./media/container-insights-health/grayicon.png)|Okänt (grått)|  

## <a name="monitor-configuration"></a>Övervaka konfiguration

För att förstå beteendet och konfigurationen av varje Övervakare som stöder Azure Monitor för behållares hälso funktion, se [konfigurations guide för hälso övervakning](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Visa hälso tillståndet för ett AKS-eller icke-AKS-kluster

Åtkomst till funktionen Azure Monitor för behållare hälsa (för hands version) är tillgänglig direkt från ett AKS-kluster genom att välja **insikter** i den vänstra rutan i Azure Portal. Under avsnittet **insikter** väljer du **behållare**. 

Om du vill visa hälso tillståndet från ett icke-AKS kluster, det vill säga ett AKS-motor kluster lokalt eller på Azure Stack väljer **Azure Monitor** i det vänstra fönstret i Azure Portal. Under avsnittet **insikter** väljer du **behållare**.  På sidan flera kluster väljer du det icke-AKS klustret i listan.

På sidan **kluster** i Azure Monitor för behållare väljer du **hälsa**.

![Exempel på kluster hälso instrument panel](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Granska kluster hälsa

När sidan hälsa öppnas, väljs som standard **Kubernetes-infrastruktur** i **hälso aspekt** rutnätet.  Rutnätet sammanfattar det aktuella tillståndet för hälso sammanslagning för Kubernetes-infrastruktur och klusternoder. Om du väljer hälso aspekt uppdaterar du resultatet i fönstret hälsohierarki (det vill säga i mitten-fönstret) och visar alla underordnade övervakare i en hierarkisk layout och visar deras aktuella hälso tillstånd. Om du vill visa mer information om en beroende övervakare, kan du välja en och ett egenskaps fönster visas automatiskt till höger på sidan. 

![Egenskaps fönstret för kluster hälsa](./media/container-insights-health/health-view-property-pane.png)

I egenskaps fönstret får du lära dig följande:

- På fliken **Översikt** visas det aktuella läget för den valda övervakaren, när övervakaren senast beräknades och när den senaste tillstånds ändringen skedde. Ytterligare information visas beroende på vilken typ av Övervakare som valts i hierarkin.

    Om du väljer en sammanställd övervakare i fönstret hälsohierarki, på fliken **Översikt** i egenskaps fönstret, visas en sammanställning av det totala antalet underordnade övervakare i hierarkin och hur många aggregerade Övervakare som är i ett kritiskt, varnings-och felfritt tillstånd. 

    ![Fliken Översikt för hälso egenskaps fönstret för sammanställd övervakare](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Om du väljer en enhets övervakare i fönstret hälsohierarki visas även under **senaste tillstånd att ändra** de tidigare exemplen som beräknats och rapporter ATS av agent agenten under de senaste fyra timmarna. Detta baseras på enhets övervakarens beräkning för att jämföra flera värden i följd för att fastställa dess status. Om du till exempel valde Pod-övervakaren för *klar läges* enhet visar den de senaste två exemplen som kontrol leras av parametern *ConsecutiveSamplesForStateTransition*. Mer information finns i den detaljerade beskrivningen av [enhets övervakare](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Fliken Översikt för hälso egenskaps fönstret](./media/container-insights-health/health-overview-unit-monitor.png)

    Om tiden som rapporteras av **senaste tillstånds ändringen** är en dag eller äldre är det resultatet av inga ändringar i status för övervakaren. Men om det senaste exemplet som togs emot för en enhets övervakare är mer än fyra timmar gammalt, indikerar detta sannolikt att den behållare som agenten inte har skickat data. Om agenten vet att en viss resurs finns, till exempel en nod, men inte har tagit emot data från nodens processor-eller minnes användnings övervakare (som ett exempel), är hälso tillståndet för övervakaren inställt på **okänd**.  

- På fliken**konfiguration** visas standard konfigurations parameter inställningarna (endast för enhets övervakare, inte sammanställda övervakare) och deras värden.
- På fliken **kunskap** innehåller den information som förklarar hur övervakaren fungerar och hur den utvärderar för fel tillstånd.

Övervakning av data på den här sidan uppdateras inte automatiskt och du måste välja **Uppdatera** överst på sidan för att se det senaste hälso tillståndet som tas emot från klustret.

## <a name="next-steps"></a>Nästa steg

Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa till avisering, visualisera eller analysera dina kluster.
