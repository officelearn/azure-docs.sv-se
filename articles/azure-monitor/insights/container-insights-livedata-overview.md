---
title: Visa livedata (förhandsversion) med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs realtidsvyn för Kubernetes loggar, händelser och pod-mått utan att använda kubectl i Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216549"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Så här visar du Kubernetes loggar, händelser och pod-mått i realtid

Azure Monitor för behållare innehåller livedata (förhandsversion) funktion, som är en avancerad diagnostisk funktion som gör att du direkt tillgång till din Azure Kubernetes Service (AKS) behållarloggar (stdout /stderror), händelser och pod-mått. Den exponerar direkt `kubectl logs -c` `kubectl get` åtkomst till `kubectl top pods`, händelser och . En konsolruta visar loggar, händelser och mått som genereras av behållarmotorn för att ytterligare hjälpa till med felsökningsproblem i realtid.

Den här artikeln innehåller en detaljerad översikt och hjälper dig att förstå hur du använder den här funktionen. 

>[!NOTE]
>AKS-kluster som aktiveras som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen är beroende av direkt åtkomst till Kubernetes API via en proxyserver från din webbläsare. Om du aktiverar nätverkssäkerhet för att blockera Kubernetes-API:et från den här proxyn blockeras den här trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure China. Den är för närvarande inte tillgänglig i Azure US Government.

Om du vill ha hjälp med att konfigurera eller felsöka funktionen Live Data (förhandsversion) läser du vår [installationsguide](container-insights-livedata-setup.md). Den här funktionen har direkt åtkomst till Kubernetes API och ytterligare information om autentiseringsmodellen finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Översikt över funktioner i Live Data (förhandsgranska)

### <a name="search"></a>Search

![Exempel på filter i fönstret Live Data Console](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

Funktionen Live Data (förhandsversion) innehåller sökfunktioner. I fältet **Sök** kan du filtrera resultat genom att skriva ett nyckelord eller en term och eventuella matchande resultat markeras så att snabb granskning kan granskas snabbt. När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filterpiller** som finns till höger om sökfältet. Beroende på vilken resurs du har valt visar p-pilleret en pod, namnområde eller ett kluster att välja mellan.  

### <a name="scroll-lock-and-pause"></a>Bläddra lås och paus 

Om du vill pausa automatisk registrering och styra fönstrets beteende, så att du kan bläddra igenom de nya data som läss manuellt, kan du använda alternativet **Bläddra.** Om du vill aktivera autoscroll igen väljer du helt enkelt alternativet **Bläddra** igen. Du kan också pausa hämtningen av logg- eller händelsedata genom att välja alternativet **Pausa,** och när du är redo att återuppta väljer du helt enkelt **Spela upp**.  

![Pausa livevisningsfönstret i live-fönstret](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Vi rekommenderar att du bara pausar eller pausar automatisk registrering under en kort tidsperiod medan du felsöker ett problem. Dessa begäranden kan påverka tillgängligheten och begränsningen av Kubernetes API i klustret. 

>[!IMPORTANT]
>Inga data lagras permanent under drift av den här funktionen. All information som samlas in under sessionen tas bort när du stänger webbläsaren eller navigerar bort från den. Data finns bara kvar för visualisering i fem minutersfönstret i måttfunktionen. Alla mått som är äldre än fem minuter tas också bort. Live Data (förhandsversion) buffertfrågor inom rimliga minnesanvändningsgränser (måste vara mer specifika här, vad är rimligt?). 

## <a name="view-logs"></a>Visa loggar

Du kan visa loggdata i realtid när de genereras av behållarmotorn från vyn **Noder,** **Controllers**och **Containers.** Om du vill visa loggdata utför du följande steg.

1. I Azure-portalen bläddrar du till AKS-klusterresursgruppen och väljer din AKS-resurs.

2. Välj **Insikter**under **Övervakning** till vänster på AKS-klusterinstrumentpanelen. 

3. Välj antingen fliken **Noder,** **Controllers**eller **Behållare.**

4. Markera ett objekt i prestandarutnätet och välj **Visa livedata (förhandsgranskning)** i egenskapsfönstret som finns till höger. Om AKS-klustret är konfigurerat med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under den webbläsarsessionen. Välj ditt konto och fullständig autentisering med Azure.  

    >[!NOTE]
    >När du visar data från arbetsytan Log Analytics genom att välja alternativet **Visa i analys i** egenskapsfönstret, visar loggsökresultaten potentiellt **Noder**, **Daemonuppsättningar**, **Replikuppsättningar**, **Jobb**, **Cron-jobb**, **Poddar**och **Behållare** som kanske inte längre finns. Försök att söka loggar för en behållare `kubectl` som inte är tillgänglig i kommer också att misslyckas här. Läs funktionen [Visa i analys](container-insights-log-search.md#search-logs-to-analyze-data) om du vill veta mer om hur du visar historiska loggar, händelser och mått.  

När konsolfönstret Live Data (preview) har autentiserats visas det under rutnätet för prestandadata där du kan visa loggdata i en kontinuerlig ström. Om hämtningsstatusindikatorn visar en grön bock, som ligger längst till höger i fönstret, betyder det att data kan hämtas och att strömmas till konsolen.  

![Alternativet För nodegenskaper vyn](./media/container-insights-livedata-overview/node-properties-pane.png)  

I fönstrets rubrik visas namnet på den pod som behållaren är grupperad med.

## <a name="view-events"></a>Visa händelser

Du kan visa händelsedata i realtid när de genereras av behållarmotorn från **vyn Noder,** **Controllers**, **Containers**och **Deployments (preview)** när en behållare, pod, nod, ReplicaSet, DaemonSet, jobb, CronJob eller Distribution väljs. Om du vill visa händelser utför du följande steg.

1. I Azure-portalen bläddrar du till AKS-klusterresursgruppen och väljer din AKS-resurs.

2. Välj **Insikter**under **Övervakning** till vänster på AKS-klusterinstrumentpanelen. 

3. Välj antingen fliken **Noder,** **Controllers,** **Behållare**eller **Distributioner (förhandsversion).**

4. Markera ett objekt i prestandarutnätet och välj **Visa livedata (förhandsgranskning)** i egenskapsfönstret som finns till höger. Om AKS-klustret är konfigurerat med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under den webbläsarsessionen. Välj ditt konto och fullständig autentisering med Azure.  

    >[!NOTE]
    >När du visar data från arbetsytan Log Analytics genom att välja alternativet **Visa i analys i** egenskapsfönstret, visar loggsökresultaten potentiellt **Noder**, **Daemonuppsättningar**, **Replikuppsättningar**, **Jobb**, **Cron-jobb**, **Poddar**och **Behållare** som kanske inte längre finns. Försök att söka loggar för en behållare `kubectl` som inte är tillgänglig i kommer också att misslyckas här. Läs funktionen [Visa i analys](container-insights-log-search.md#search-logs-to-analyze-data) om du vill veta mer om hur du visar historiska loggar, händelser och mått.  

När konsolfönstret Live Data (preview) har autentiserats visas det under rutnätet för prestandadata. Om hämtningsstatusindikatorn visar en grön bock, som ligger längst till höger i fönstret, betyder det att data kan hämtas och att strömmas till konsolen. 
    
Om objektet du markerade var en behållare väljer du alternativet **Händelser** i fönstret. Om du har valt en nod, pod eller handkontroll markeras visningshändelser automatiskt. 

![Visa händelser för kontroll av kontrollantegenskaper](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

I fönstrets rubrik visas namnet på den pod som behållaren är grupperad med.

### <a name="filter-events"></a>Filtrera händelser 

När du visar händelser kan du dessutom begränsa resultaten med hjälp av **filterpiller** som finns till höger om sökfältet. Beroende på vilken resurs du har valt visar p-pilleret en pod, namnområde eller ett kluster att välja mellan.  

## <a name="view-metrics"></a>Visa mått 

Du kan visa mätdata i realtid när de genereras av behållarmotorn från **vyn Noder** eller **Controllers** endast när en **pod** är markerad. Om du vill visa mått utför du följande steg.

1. I Azure-portalen bläddrar du till AKS-klusterresursgruppen och väljer din AKS-resurs.

2. Välj **Insikter**under **Övervakning** till vänster på AKS-klusterinstrumentpanelen. 

3. Välj antingen fliken **Noder** eller **Controllers.**

4. Markera ett **Pod-objekt** i prestandarutnätet och välj Visa **livedata (förhandsgranskning)** på egenskapsfönstret som finns till höger. Om AKS-klustret är konfigurerat med enkel inloggning med Azure AD uppmanas du att autentisera vid första användningen under den webbläsarsessionen. Välj ditt konto och fullständig autentisering med Azure.  

    >[!NOTE]
    >När du visar data från arbetsytan Log Analytics genom att välja alternativet **Visa i analys i** egenskapsfönstret, visar loggsökresultaten potentiellt **Noder**, **Daemonuppsättningar**, **Replikuppsättningar**, **Jobb**, **Cron-jobb**, **Poddar**och **Behållare** som kanske inte längre finns. Försök att söka loggar för en behållare `kubectl` som inte är tillgänglig i kommer också att misslyckas här. Läs funktionen [Visa i analys](container-insights-log-search.md#search-logs-to-analyze-data) om du vill veta mer om hur du visar historiska loggar, händelser och mått.  

När konsolfönstret Live Data (preview) har autentiserats visas det under rutnätet för prestandadata. Måttdata hämtas och börjar strömma till konsolen för presentation i de två diagrammen. I fönstrets rubrik visas namnet på den pod som behållaren är grupperad med.

![Visa exempel på pod-mått](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Nästa steg

- Information om hur du fortsätter att lära dig hur du använder Azure Monitor och övervakar andra aspekter av AKS-klustret finns i [Visa Azure Kubernetes Service health](container-insights-analyze.md).

- Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av dina kluster.
