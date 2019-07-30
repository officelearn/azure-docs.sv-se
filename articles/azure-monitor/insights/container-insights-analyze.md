---
title: Övervaka prestanda för AKS-kluster med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan visa och analysera prestanda och loggar data med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: bbfc8cc61571de8b76ef1f7f0216501ef6d2cdee
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377467"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Förstå prestanda för AKS-kluster med Azure Monitor för behållare 
Med Azure Monitor för behållare kan använda du prestandadiagram och hälsostatus för att övervaka arbetsbelastningen för dina Azure Kubernetes Service (AKS) kluster ur två perspektiv, direkt från ett AKS-kluster eller alla AKS-kluster i en prenumeration från Azure Övervaka. Visa Azure Container Instances (ACI) är också möjligt när du övervakar ett specifikt AKS-kluster.

Den här artikeln hjälper dig att förstå upplevelse mellan två perspektiv och hur den hjälper dig att snabbt utvärdera, undersöka och lösa problem som identifieras.

Information om hur du aktiverar Azure Monitor för behållare finns i [publicera Azure Monitor för behållare](container-insights-onboard.md).

Azure Monitor ger en vy med flera kluster som visar hälso status för alla övervakade AKS-kluster som kör Linux och Windows Server 2019 som har distribuerats över resurs grupper i dina prenumerationer.  Den visar AKS-kluster identifieras som inte övervakas av lösningen. Du kan omedelbart förstår klusterhälsa och härifrån kan du öka detaljnivån till sidan noden och controller prestanda eller navigera för att se prestandadiagram för klustret.  AKS-kluster identifieras och identifieras som oövervakade kan aktivera du övervakning för klustret när som helst.  

De viktigaste skillnaderna vid övervakning av ett Windows Server-kluster med Azure Monitor för behållare jämfört med ett Linux-kluster är följande:

- Minnets RSS-mått är inte tillgängligt för Windows-noden och behållare.
- Information om disk lagrings kapacitet är inte tillgänglig för Windows-noder.
- Det finns stöd för Live-loggar med undantag för Windows container-loggar.
- Endast Pod-miljöer övervakas, inte Docker-miljöer.
- I för hands versionen stöds maximalt 30 Windows Server-behållare. Den här begränsningen gäller inte för Linux-behållare.  

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Visa flera kluster från Azure Monitor

Om du vill visa alla AKS-kluster som distribueras hälsostatus, Välj **övervakaren** från det vänstra fönstret i Azure-portalen.  Under den **Insights** väljer **behållare**.  

![Exempel på Azure Monitor flera klusterinstrumentpanel](./media/container-insights-analyze/azmon-containers-multiview.png)

På den **övervakas kluster** fliken du kan lära dig följande:

1. Hur många kluster är i ett kritiskt eller dåligt tillstånd, jämfört med hur många är felfri eller inte reporting (kallas ett okänt tillstånd)?
2. Är alla mina [Azure Kubernetes-motor (AKS-motor)](https://github.com/Azure/aks-engine) distributioner felfria?
3. Hur många noder distribueras användare och system-poddar per kluster?
4. Hur mycket disk utrymme är tillgängligt och är det ett kapacitets problem?

Den hälsotillstånd statusen som ingår är: 

* **Felfria** – inga problem har identifierats för den virtuella datorn och den fungerar som krävs. 
* **Kritiska** – en eller flera kritiska problem har identifierats som behöver åtgärdas om du vill återställa normal Användningsstatus som förväntat.
* **Varning** – ett eller flera problem har identifierats som behöver åtgärdas eller hälsostatus kan bli kritiska.
* **Okänd** – om tjänsten inte kunde upprätta en anslutning med noden eller pod, att status ändras till ett okänt tillstånd.
* **Det gick inte att hitta** – antingen arbetsytan, resursgruppen eller prenumerationen som innehåller arbetsytan för den här lösningen har tagits bort.
* **Obehörig** -användaren har inte behörighet att läsa data i arbetsytan.
* **Fel** -fel uppstod vid försök att läsa data från arbetsytan.
* Felkonfigurerad – Azure Monitor för behållare har inte kon figurer ATS korrekt på den angivna arbets ytan.
* **Inga data** -Data inte har rapporterat till arbetsytan under de senaste 30 minuterna.

Hälso tillstånd beräknar övergripande kluster status som *sämsta av* de tre tillstånden med ett undantag – om något av de tre tillstånden är *Okänt*visar det övergripande kluster tillståndet **Okänt**.  

Följande tabell innehåller en detaljerad analys av beräkningen Kontrollera hälsotillstånd för ett övervakade kluster för flera kluster-vyn.

| |Status |Tillgänglighet |  
|-------|-------|-----------------|  
|**Användaren Pod**| | |  
| |Felfri |100% |  
| |Varning |90 - 99% |  
| |Kritisk |< 90% |  
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |  
|**System Pod**| | |  
| |Felfri |100% |
| |Varning |Gäller inte |
| |Kritisk |< 100% |
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |
|**Node** | | |
| |Felfri |> 85% |
| |Varning |60 - 84% |
| |Kritisk |< 60% |
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |

Från listan över kluster, du kan öka detaljnivån ned till den **kluster** sidan genom att klicka på namnet på klustret, den **noder** prestanda sidan genom att klicka på samlade av noder i den **noder** kolumnen för den specifika kluster, eller öka detaljrikedomen för att den **domänkontrollanter** prestanda sidan genom att klicka på samlade av **användaren poddar** eller **System poddar**kolumn.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Visa prestanda direkt från ett AKS-kluster

Åtkomst till Azure Monitor för behållare som är tillgängliga direkt från ett AKS-kluster genom att välja **Insights** från det vänstra fönstret. Visa information om AKS-klustret är uppdelad i fyra perspektiv:

- Kluster
- Noder 
- Kontrollanter  
- Containrar

Standardsidan öppnas när du klickar på **Insights** är **kluster**, och innehåller fyra prestanda linjediagram visar viktiga prestandamått för ditt kluster. 

![Prestandadiagram för exempel på fliken kluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Prestandadiagrammet visar fyra prestandamått:

- **CPU-användning&nbsp;%för nod**: Ett sammanställt perspektiv av CPU-belastning för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **&nbsp;Minnes användningpånod:%** Ett sammanställt perspektiv för minnes användning för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **Antal noder**: Antalet noder och status från Kubernetes. Status för noderna i klustret som representeras är *alla*, *redo*, och *inte klara* och kan filtreras individuellt eller kombineras i Väljaren ovanför diagrammet. 
- **Antal aktiviteter Pod**: Ett Pod antal och status från Kubernetes. Statusen för poddarna representeras är *alla*, *väntande*, *kör*, och *okänd* och kan filtreras individuellt eller kombineras i den väljare ovanför diagrammet. 

Du kan använda vänster/höger-piltangenterna för att gå igenom varje data punkt i diagrammet och upp-och nedpilen för att gå igenom percentils linjerna. Om du klickar på ikonen fäst i det övre högra hörnet i något av diagrammen fästs det valda diagrammet på den sista Azure-instrumentpanelen som du senast visade. På instrument panelen kan du ändra storlek på och flytta diagrammet. Genom att välja diagrammet på instrument panelen kommer du att omdirigera dig till Azure Monitor för behållare och läsa in rätt omfång och vy.

Azure Monitor för behållare stöder också Azure Monitor [Metrics Explorer](../platform/metrics-getting-started.md), där du kan skapa egna rit diagram, korrelera och undersöka trender och fästa på instrument paneler. Från metrics Explorer kan du också använda de kriterier som du har angett för att visualisera måtten som grund för en [Metric-baserad varnings regel](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Visa behållar mått i Metrics Explorer

I Metrics Explorer kan du visa aggregerade noder och Pod användnings mått från Azure Monitor för behållare. I följande tabell sammanfattas information som hjälper dig att förstå hur du använder mått diagram för att visualisera container mått.

|Namnrymd | Mått |
|----------|--------|
| Insights. container/Nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| Insights. container/poddar | |
| | PodCount |

Du kan använda [uppdelning](../platform/metrics-charts.md#apply-splitting-to-a-chart) av ett mått för att visa det efter dimension och visualisera hur olika segment jämförs med varandra. För en nod kan du segmentera diagrammet med *värd* dimensionen och från en POD kan du segmentera det med följande dimensioner:

* Kontrollenhet
* Kubernetes-namnrymd
* Nod
* Fas

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysera noder, styrenheter och hälso tillstånd för behållare

När du växlar till **noder**, **domänkontrollanter**, och **behållare** fliken automatiskt visas till höger på sidan är egenskapsrutan. Den visar egenskaperna för det markerade objektet, inklusive etiketter som du definierar för att ordna Kubernetes-objekt. När en Linux-nod väljs visas även under avsnittet **lokal disk kapacitet** tillgängligt disk utrymme och procent som används för varje disk som visas för noden. Klicka på den **>>** länkar i fönstret till view\hide fönstret. 

![Egenskapsrutan för exempel Kubernetes perspektiv](./media/container-insights-analyze/perspectives-preview-pane-01.png)

När du expanderar objekt inom hierarkin egenskaper fönstret uppdateringar baserat på de objekt som valts. Fönstret kan du också visa Kubernetes-händelser med fördefinierade loggsökningar genom att klicka på den **visa Kubernetes-händelseloggar** länken längst upp i fönstret. Mer information om hur du visar loggdata för Kubernetes finns i [söka loggarna för att analysera data](container-insights-log-search.md). När du har granskat kluster resurserna kan du se behållar loggar och händelser i real tid. Mer information om den här funktionen och konfigurationen som krävs för att bevilja och kontrol lera åtkomst finns i [så här visar du loggar real tid med Azure Monitor för behållare](container-insights-live-logs.md). 

Använd alternativet **+ Lägg till filter** överst på sidan för att filtrera resultaten för vyn efter **tjänst**, **nod**, **namnrymd**eller **adresspool** och när du har valt filter omfånget väljer du från ett av värdena som visas i **Välj värde** fält.  När filtret har konfigurerats kan tillämpas globalt medan du visar alla perspektiv AKS-klustret.  Formeln har endast stöd för likhetstecknet.  Du kan lägga till ytterligare filter på den första mallen för att ytterligare begränsa dina resultat.  Om du har angett ett filter genom att exempelvis **nod**, andra filtret skulle bara kan du välja **Service** eller **Namespace**.  

![Exempel med filtret för att begränsa resultaten](./media/container-insights-analyze/add-filter-option-01.png)

Ange ett filter i en flik fortsätter att tillämpas när du väljer en annan utan tas bort när du klickar på den **x** bredvid det angivna filtret.   

Växla till den **noder** fliken och raden hierarkin följer objektmodellen Kubernetes börjar med en nod i klustret. Expandera noden och du kan visa en eller flera poddar som körs på noden. Om flera behållare är grupperat till en pod, visas de som den sista raden i hierarkin. Du kan också visa hur många icke-pod relaterade arbetsbelastningar körs på värden om värden har processor eller minne.

![Exempel Kubernetes Node-hierarkin i prestandavyn](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-behållare som kör Windows Server 2019 OS visas efter alla Linux-baserade noder i listan. När du expanderar en Windows Server-nod kan du Visa en eller flera poddar och behållare som körs på noden. När en nod väljs, visar egenskaps fönstret versions information, exklusive agent information eftersom det inte finns någon agent installerad på Windows Server-noder.  

![Exempel på Node-hierarki med Windows Server-noder listade](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances virtuella noder kör Linux-operativsystem visas efter den sista noden i AKS klustret i listan.  När du har expanderat en virtuell ACI-nod kan visa du en eller flera poddar i ACI och behållare som körs på noden.  Mått är inte samlas in och rapporteras för noderna, endast poddar.

![Exempel-Node-hierarkin med Container Instances visas](./media/container-insights-analyze/nodes-view-aci.png)

Från en utökad noden kan granska du nedåt från pod eller behållare som körs på noden till den kontrollenheten för att visa prestandadata som visar den styrenheten. Klicka på värde under den **Controller** kolumnen för den specifika noden.   
![Exemplet nedåt från nod till controller i prestandavyn](./media/container-insights-analyze/drill-down-node-controller.png)

Du kan välja domänkontrollanter eller behållare överst på sidan och granska status och Resursanvändning för dessa objekt.  Om istället du vill granska minnesanvändningen, i den **mått** listrutan, väljer **minne RSS** eller **arbetsminne**. **Minne RSS** stöds endast för Kubernetes version 1.8 och senare. Annars kan du visa värden för **Min&nbsp; %**  som *NaN&nbsp;%* , vilket är ett värde av numeriska data som representerar en odefinierad eller inte går att representera värde.

I arbets minnet för minnet visas både det inhemska minnet och det virtuella minnet (cache) som ingår, och det innehåller en summa av vad programmet använder. Minne RSS visar bara huvud minnet, vilket är det inhemska minnet. Det här måttet visar den faktiska kapaciteten för tillgängligt minne.

![Behållaren noder prestandavy](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Som standard prestandadata baseras på de senaste sex timmarna, men du kan ändra i fönstret med hjälp av den **TimeRange** alternativet i det övre vänstra hörnet. Du kan också filtrera resultaten inom tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i Väljaren: e percentilen. 

![: E percentilen val för filtrering av data](./media/container-insights-analyze/containers-metric-percentile-filter.png)

När du för muspekaren över stapeldiagrammet under den **Trend** kolumnen visar varje fältet CPU eller minne användning, beroende på vilken enskilt mått har valts, inom 15 minuter exemplet. När du har valt trend diagrammet i ett tangent bord kan du använda knapparna Alt + PageUp eller ALT + PageDown för att gå igenom varje stapel individuellt och få samma information som i mouseover.

![Trend rads diagram hovra över exempel](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

I nästa exempel Observera för första i listan - nod *aks-nodepool1 -* , värdet för **behållare** är 9, vilket är en sammanslagning av det totala antalet behållare som distribueras.

![Sammanslagning av behållare per nod-exempel](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Det kan du snabbt kan identifiera om du har en rätt balans mellan behållare mellan noderna i klustret. 

I följande tabell beskrivs den information som visas när du visar noder:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden. |
| Status | Kubernetes vy över nodstatusen. |
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Genomsnittlig nod procent baserat på: e percentilen under den valda perioden. |
| Genomsn, Min, Max, 50, 90 | Genomsnittlig noder faktiskt värde baserat på: e percentilen under den tidslängd som valts. Medelvärdet mäts från processor/minne gränsen för en nod. Det är genomsn värdet som rapporteras av värden för poddar och behållare. |
| Containrar | Antal behållare. |
| Drifttid | Representerar tid eftersom en nod startas eller startades om. |
| Kontrollanter | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt i. Inte alla poddar är i en kontrollant så vissa kan visa **ej tillämpligt**. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Stapeldiagram trend representerar genomsnittliga: e percentilen mått procentandelen kontrollanten. |

Välj i Väljaren, **styrenheter**.

![Välj domänkontrollanter vy](./media/container-insights-analyze/containers-controllers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina domänkontrollanter och ACI virtuell nod domänkontrollanter eller virtuell nod poddar som inte är anslutna till en domänkontrollant.

![\<Namn > kontrollantens prestanda vy](./media/container-insights-analyze/containers-controllers-view.png)

Hierarki för raden som börjar med en domänkontrollant och när du har expanderat en domänkontrollant kan du visa en eller flera poddar.  Expandera pod och den sista raden visar behållaren grupperade poden. Från en utökad kontrollant du ökar detaljnivån till den noden som den körs på att visa prestandadata som filtreras för noden. ACI-poddar som inte är ansluten till en styrenhet visas senast i listan.

![Exempel-styrenheter hierarki med Container Instances poddar i listan](./media/container-insights-analyze/controllers-view-aci.png)

Klicka på värde under den **noden** kolumnen för den specifika styrenheten.   

![Exempel granska nedåt från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

Informationen som visas när du visar domänkontrollanter beskrivs i följande tabell:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Insamling av status för behållarna när den har slutförts kör med status, till exempel *OK*, *Uppsagd*, *misslyckades* *stoppad*, eller *Pausats*. Om behållaren körs, men status har inte korrekt visas eller hämtades inte av agenten och har inte svarat mer än 30 minuter, statusen är *okänd*. Ytterligare information om statusikonen finns i tabellen nedan.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Summera medelvärdet av den genomsnittliga procent andelen för varje enhet för det valda måttet och percentilen. |
| Genomsn, Min, Max, 50, 90  | Sammanslagning av den genomsnittliga CPU-Millicore eller minnes prestanda för den valda percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Containrar | Totalt antal behållare för domänkontrollant eller pod. |
| Startar om | Sammanslagning av antalet omstarter från behållare. |
| Drifttid | Representerar tid efter att en behållare startades. |
| Node | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;%| Stapeldiagram trend representerar genomsnittliga: e percentilen mått för kontrollenheten. |

Ikonerna i statusfältet onlinestatus av behållarna:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/container-insights-analyze/containers-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterat körs men har inte svarat mer än 30 minuter|
| ![Lyckad statusikon](./media/container-insights-analyze/containers-green-icon.png) | Har stoppats eller gick inte att stoppa|

Statusikonen visar ett antal baserat på din pod tillhandahåller. Den visar sämsta två tillstånd och när du hovrar över statusen visar statusen samlad från alla poddar i behållaren. Om det inte finns ett färdigt tillstånd, statusvärdet visar **(0)** . 

Välj i Väljaren, **behållare**.

![Välj behållare vy](./media/container-insights-analyze/containers-containers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina Azure Kubernetes och Azure Container Instances-behållare.  

![\<Namn > kontrollantens prestanda vy](./media/container-insights-analyze/containers-containers-view.png)

Från en behållare kan du öka detaljnivån till en pod eller noden för att visa prestandadata som filtreras för objektet. Klicka på värde under den **Pod** eller **noden** kolumnen för den specifika behållaren.   

![Exempel granska nedåt från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

I följande tabell beskrivs den information som visas när du visar behållare:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Status för behållare, om sådana. Ytterligare information om statusikonen finns i nästa tabell.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Insamling av den genomsnittliga procentandelen av varje entitet för valda mått- och: e percentilen. |
| Genomsn, Min, Max, 50, 90  | Insamling av Genomsnittlig CPU millicore eller minne prestanda för behållaren för den valda: e percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Pod | Behållaren där poden finns.| 
| Node |  Noden där behållaren finns. | 
| Startar om | Representerar tid efter att en behållare startades. |
| Drifttid | Representerar tid eftersom en behållare startades eller startas om. |
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Stapeldiagram trend representerar genomsnittliga: e percentilen mått procentandelen av behållaren. |

Ikonen i statusfältet anger online status för poddar, enligt beskrivningen i följande tabell:
 
| Ikon | Status |  
|--------|-------------|  
| ![Klar körs statusikon](./media/container-insights-analyze/containers-ready-icon.png) | Kör (klar)|  
| ![Väntar på eller pausat statusikon](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar på eller pausats|  
| ![Senast rapporterat kör statusikon](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterat körs men har inte svarat under mer än 30 minuterna|  
| ![Avslutade statusikon](./media/container-insights-analyze/containers-terminated-icon.png) | Har stoppats eller gick inte att stoppa|  
| ![Misslyckade statusikon](./media/container-insights-analyze/containers-failed-icon.png) | Felaktigt tillstånd |  

## <a name="workbooks"></a>Arbetsböcker

Arbets böcker kombinerar text, [logg frågor](../log-query/query-language.md), [mått](../platform/data-platform-metrics.md)och parametrar till omfattande interaktiva rapporter. Arbets böcker kan redige ras av andra team medlemmar som har åtkomst till samma Azure-resurser.

Azure Monitor för behållare innehåller fyra arbets böcker för att komma igång:

- **Disk kapacitet**: Visar interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk-% användning för alla diskar
    - Ledigt disk utrymme för alla diskar
    - Ett rutnät som visar för varje nod disk, dess% använda utrymme, trend av använt utrymme, ledigt disk utrymme (GiB) och trend för ledigt disk utrymme (GiB). När en rad markeras i tabellen visas% använt utrymme och ledigt disk utrymme (GiB) nedan 

- **Disk-i/o**: Visar interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk-I/O sammanfattas över alla diskar efter lästa byte/s, skrivna byte/s och Läs-och skriv byte/s trender 
    - Åtta prestanda diagram som visar viktiga prestanda indikatorer för att mäta och identifiera disk-I/O-Flask halsar.

- **Kubelet**: Innehåller två rutnät som visar drift statistik över viktiga noder:

    - Översikt per Node-rutnät sammanfattar total åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend för varje nod.
    - Översikt per åtgärds typ sammanfattar för varje åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend.

- **Nätverk**: Visar interaktiva diagram för nätverks användning för varje noders nätverkskort och ett rutnät som presenterar nyckeltal för att mäta prestanda för nätverkskorten.  

Du kommer åt dessa arbets böcker genom att välja var och en i list rutan **Visa arbets böcker** .  

![Visa List rutan för arbets böcker](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Nästa steg

- I avsnittet [skapa prestanda varningar med Azure Monitor för behållare](container-insights-alerts.md) får du lära dig hur du skapar aviseringar för hög processor-och minnes användning för att stödja DevOps eller operativa processer och procedurer. 

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.
