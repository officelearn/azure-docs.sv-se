---
title: Kubernetes övervakning med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan visa och analysera prestanda för ett Kubernetes-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298379"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Övervaka kubernetes-klusterprestanda med Azure Monitor för behållare

Med Azure Monitor för behållare kan du använda prestandadiagram och hälsostatus för att övervaka arbetsbelastningen för Kubernetes-kluster som finns på Azure Kubernetes Service (AKS), Azure Stack eller annan miljö från två perspektiv. Du kan övervaka direkt från klustret eller visa alla kluster i en prenumeration från Azure Monitor. Det är också möjligt att visa Azure Container Instances när du övervakar ett specifikt AKS-kluster.

Den här artikeln hjälper dig att förstå de två perspektiven och hur Azure Monitor hjälper dig att snabbt bedöma, undersöka och lösa identifierade problem.

Information om hur du aktiverar Azure Monitor för behållare finns [i Onboard Azure Monitor för behållare](container-insights-onboard.md).

Azure Monitor innehåller en vy med flera kluster som visar hälsostatusen för alla övervakade Kubernetes-kluster som kör Linux och Windows Server 2019 som distribueras över resursgrupper i dina prenumerationer. Den visar kluster som identifierats i alla miljöer som inte övervakas av lösningen. Du kan omedelbart förstå klusterhälsan, och härifrån kan du öka detaljnivån till prestandasidan för noden och styrenheten eller navigera för att se prestandadiagram för klustret. För AKS-kluster som upptäcktes och identifierades som oövervakade kan du aktivera övervakning för dem när som helst. 

De största skillnaderna i övervakningen av ett Windows Server-kluster med Azure Monitor för behållare jämfört med ett Linux-kluster beskrivs [här](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) i översiktsartikeln.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Flerklustervy från Azure Monitor

Om du vill visa hälsotillståndet för alla Kubernetes-kluster som distribueras väljer du **Övervaka** från den vänstra rutan i Azure-portalen. Under avsnittet **Insikter** väljer du **Behållare**. 

![Azure Monitor exempel på instrumentpanel med flera kluster](./media/container-insights-analyze/azmon-containers-multiview.png)

Du kan begränsa resultaten som presenteras i rutnätet för att visa kluster som är:

* **Azure** - AKS- och AKS-Engine-kluster som finns i Azure Kubernetes Service
* **Azure Stack (förhandsversion)** – AKS-Engine-kluster som finns på Azure Stack
* **Icke-Azure (förhandsversion)** - Kubernetes kluster som finns lokalt
* **Alla** – Visa alla Kubernetes-kluster som finns i Azure-, Azure Stack- och lokala miljöer som finns med i Azure Monitor för behållare

Om du vill visa kluster från en viss miljö väljer du **det** från miljöpiller i det övre vänstra hörnet på sidan.

![Exempel på miljöpiller](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

På fliken **Övervakade kluster** får du lära dig följande:

- Hur många kluster som är i ett kritiskt eller felfritt tillstånd, jämfört med hur många som är felfria eller inte rapporterar (kallas ett okänt tillstånd).
- Om alla [AKS-motordistributioner (Azure Kubernetes Engine)](https://github.com/Azure/aks-engine) är felfria.
- Hur många noder och användar- och systemkapslar distribueras per kluster.
- Hur mycket diskutrymme som är tillgängligt och om det finns ett kapacitetsproblem.

De hälsotillstånd som ingår är: 

* **Felfritt:** Inga problem identifieras för den virtuella datorn och den fungerar efter behov. 
* **Kritisk**: Ett eller flera kritiska problem identifieras som måste åtgärdas för att återställa normalt drifttillstånd som förväntat.
* **Varning**: Ett eller flera problem upptäcks som måste åtgärdas eller hälsotillståndet kan bli kritiskt.
* **Okänd**: Om tjänsten inte kunde upprätta en anslutning till noden eller podden ändras statusen till ett okänt tillstånd.
* **Hittades inte**: Antingen togs arbetsytan, resursgruppen eller prenumerationen som innehåller arbetsytan för den här lösningen bort.
* **Obehörig:** Användaren har inte behörighet att läsa data på arbetsytan.
* **Fel:** Ett fel uppstod när data skulle läsas från arbetsytan.
* **Felkonfigurerad**: Azure Monitor för behållare har inte konfigurerats korrekt på den angivna arbetsytan.
* **Inga data**: Data har inte rapporterats till arbetsytan under de senaste 30 minuterna.

Hälsotillstånd beräknar övergripande klusterstatus som det *värsta av* de tre tillstånden med ett undantag. Om något av de tre tillstånden är Okänt visas **Okänd**i det övergripande klustertillståndet . 

Följande tabell innehåller en uppdelning av beräkningen som styr hälsotillstånden för ett övervakat kluster i flerklustervyn.

| |Status |Tillgänglighet |  
|-------|-------|-----------------|  
|**Användarpodden**| | |  
| |Felfri |100 % |  
| |Varning |90 - 99% |  
| |Kritisk |<90% |  
| |Okänt |Om inte rapporterats under de senaste 30 minuterna |  
|**Systemkapsel**| | |  
| |Felfri |100 % |
| |Varning |Ej tillämpligt |
| |Kritisk |<100% |
| |Okänt |Om inte rapporterats under de senaste 30 minuterna |
|**Node** | | |
| |Felfri |>85% |
| |Varning |60 - 84% |
| |Kritisk |<60% |
| |Okänt |Om inte rapporterats under de senaste 30 minuterna |

I listan över kluster kan du öka detaljnivån till **klustersidan** genom att välja namnet på klustret. Gå sedan till resultatsidan **för noder** genom att välja sammanslagningen av noder i kolumnen **Noder** för det specifika klustret. Du kan också öka detaljnivån till resultatsidan **för Controllers** genom att välja sammanslagningen av kolumnen **Användarpoddar** eller **Systempoddar.**

## <a name="view-performance-directly-from-a-cluster"></a>Visa prestanda direkt från ett kluster

Åtkomst till Azure Monitor för behållare är tillgänglig direkt från ett AKS-kluster genom att välja **Insights** > **Cluster** från den vänstra rutan eller när du har valt ett kluster från flerklustervyn. Information om klustret är ordnad i fyra perspektiv:

- Kluster
- Noder 
- Kontrollanter 
- Containrar

>[!NOTE]
>Den upplevelse som beskrivs i resten av den här artikeln gäller även för att visa prestanda och hälsostatus för kubernetes-kluster som finns på Azure Stack eller annan miljö när de väljs från multi-cluster-vyn. 

Standardsidan öppnas och visar fyra linjeprestandadiagram som visar viktiga prestandamått för klustret. 

![Exempel på prestandadiagram på fliken Kluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Prestandadiagrammen visar fyra prestandamått:

- **Nod CPU-användning:&nbsp;** Ett aggregerat perspektiv på CPU-användning för hela klustret. Om du vill filtrera resultaten för tidsintervallet väljer du **Medel**, **Min**, **50,** **90,** **95:e**eller **Max** i percentilväljaren ovanför diagrammet. Filtren kan användas antingen individuellt eller i kombination. 
- **Nodminnesanvändning&nbsp;**: Ett aggregerat perspektiv på minnesanvändning för hela klustret. Om du vill filtrera resultaten för tidsintervallet väljer du **Medel**, **Min**, **50,** **90,** **95:e**eller **Max** i percentilväljaren ovanför diagrammet. Filtren kan användas antingen individuellt eller i kombination. 
- **Antal nod:** Antal noder och status från Kubernetes. Status för klusternoderna som representeras är Summa, Klar och Inte redo. De kan filtreras individuellt eller kombineras i väljaren ovanför diagrammet. 
- **Antal aktiva pod:** Antal pod och status från Kubernetes. Status för de poddar som representeras är Summa, Väntande, Kör, Okänd, Lyckades eller Misslyckades. De kan filtreras individuellt eller kombineras i väljaren ovanför diagrammet. 

Använd vänster- och högerpiltangenterna för att växla mellan varje datapunkt i diagrammet. Använd upp- och nedpiltangenterna för att växla mellan percentillinjerna. Markera pin-ikonen i det övre högra hörnet i något av diagrammen för att fästa det markerade diagrammet på den senaste Azure-instrumentpanelen som du visade. Från instrumentpanelen kan du ändra storlek på och flytta diagrammet. Om du väljer diagrammet från instrumentpanelen omdirigeras du till Azure Monitor för behållare och läser in rätt omfattning och vy.

Azure Monitor for containers stöder också Azure [Monitor-statistikutforskaren](../platform/metrics-getting-started.md), där du kan skapa egna ritdiagram, korrelera och undersöka trender och fästa på instrumentpaneler. Från statistikutforskaren kan du också använda de kriterier som du anger för att visualisera dina mått som grund för en [måttbaserad varningsregel](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Visa behållarmått i statistikutforskaren

I statistikutforskaren kan du visa aggregerade nod- och poddanvändningsmått från Azure Monitor för behållare. I följande tabell sammanfattas informationen för att hjälpa dig att förstå hur du använder måttdiagrammen för att visualisera behållarmått.

|Namnområde | Mått | Beskrivning | 
|----------|--------|-------------|
| insights.container/noder | |
| | cpuUsageMillicores | Aggregerad mätning av CPU-användning i klustret. Det är en CPU-kärna uppdelad i 1000 enheter (milli = 1000). Används för att bestämma användningen av kärnor i en behållare där många program kan använda en kärna.| 
| | cpuUsagePercentage | Aggregerat genomsnittligt CPU-utnyttjande mätt i procent i klustret.|
| | memoryRssBytes | Behållare RSS-minne som används i byte.| 
| | minneRssPercentage | Behållare RSS-minne som används i procent.|
| | memoryWorkingSetBytes | Behållarens arbetsminne används.| 
| | minneArbetsningSetPercentage | Behållarens arbetsminne används i procent. | 
| | nodesCount | En nodräkning från Kubernetes.|
| insights.container/pods | |
| | PodCount (Olikartade) | En pod räknas från Kubernetes.|

Du kan [dela ett](../platform/metrics-charts.md#apply-splitting-to-a-chart) mått för att visa det efter dimension och visualisera hur olika segment i det står sig i jämförelse med varandra. För en nod kan du segmentera diagrammet efter *värddimensionen.* Från en pod kan du segmentera den efter följande dimensioner:

* Controller
* Namnområde Kubernetes
* Node
* Fas

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysera noder, styrenheter och behållarens hälsotillstånd

När du växlar till flikarna **Noder**, **Controllers**och **Behållare** visas automatiskt ett egenskapsfönster till höger på sidan. Den visar egenskaperna för det markerade objektet, som innehåller de etiketter som du har definierat för att ordna Kubernetes-objekt. När en Linux-nod har valts visar avsnittet **Lokal diskkapacitet** också tillgängligt diskutrymme och procentandel som används för varje disk som presenteras för noden. Markera **>>** länken i fönstret om du vill visa eller dölja fönstret.

När du expanderar objekten i hierarkin uppdateras egenskapsfönstret baserat på det markerade objektet. I fönstret kan du också visa Kubernetes-behållarloggar (stdout/stderror), händelser och pod-mått genom att välja länken **Visa livedata (förhandsversion)** högst upp i fönstret. Mer information om den konfiguration som krävs för att bevilja och kontrollera åtkomst för att visa dessa data finns [i Konfigurera livedata (förhandsgranskning)](container-insights-livedata-setup.md). När du granskar klusterresurser kan du se dessa data från behållaren i realtid. Mer information om den här funktionen finns [i Så här visar du Kubernetes loggar, händelser och pod-mått i realtid](container-insights-livedata-overview.md). Om du vill visa Kubernetes loggdata som lagras på arbetsytan baserat på fördefinierade loggsökningar väljer du **Visa behållarloggar** i listrutan **Visa i analys.** Mer information om det här avsnittet finns i [Sökloggar för att analysera data](container-insights-log-search.md#search-logs-to-analyze-data).

Använd alternativet **+ Lägg till filter** högst upp på sidan om du vill filtrera resultaten för vyn efter **tjänst,** **nod**, **namnområde**eller **nodpool**. När du har valt filteromfattningen markerar du ett av de värden som visas i fältet Välj värde.After you select the filter scope, select one of the values shown in the **Select value(s)** field. När filtret har konfigurerats tillämpas det globalt när du visar alla perspektiv i AKS-klustret. Formeln stöder bara likhetstecknet. Du kan lägga till ytterligare filter ovanpå den första för att ytterligare begränsa dina resultat. Om du till exempel anger ett filter efter **nod**kan du bara välja **Tjänst** eller **Namnområde** för det andra filtret.

Ange ett filter på en flik fortsätter att tillämpas när du väljer en annan. Den tas bort när **x** du har valt x-symbolen bredvid det angivna filtret. 

Växla till fliken **Noder** och radhierarkin följer kubernetes-objektmodellen, som börjar med en nod i klustret. Expandera noden om du vill visa en eller flera poddar som körs på noden. Om mer än en behållare är grupperad i en pod visas de som den sista raden i hierarkin. Du kan också visa hur många icke-pod-relaterade arbetsbelastningar som körs på värden om värden har processor- eller minnestryck.

![Exempel på kubernetesnodhierarkin i prestandavyn](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-behållare som kör Windows Server 2019 OS visas efter alla Linux-baserade noder i listan. När du expanderar en Windows Server-nod kan du visa en eller flera poddar och behållare som körs på noden. När en nod har markerats visar egenskapsfönstret versionsinformation. Agentinformation är utesluten eftersom Windows Server-noder inte har en agent installerad. 

![Exempelnodhierarki med Windows Server-noder listade](./media/container-insights-analyze/nodes-view-windows.png) 

Virtuella noder för Azure-behållarinstanser som kör Linux OS visas efter den senaste AKS-klusternoden i listan. När du expanderar en virtuell containerinstans nod kan du visa en eller flera containerinstanser poddar och behållare som körs på noden. Mått samlas inte in och rapporteras för noder, bara för poddar.

![Exempelnodhierarki med behållarinstanser listade](./media/container-insights-analyze/nodes-view-aci.png)

Från en expanderad nod kan du öka detaljnivån från podden eller behållaren som körs på noden till styrenheten för att visa prestandadata filtrerade för den styrenheten. Välj värdet under kolumnen **Controller** för den specifika noden.
 
![Exempel detaljgranskning från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-node-controller.png)

Välj styrenheter eller behållare högst upp på sidan om du vill granska status- och resursanvändningen för dessa objekt. Om du vill granska minnesanvändning väljer du **Minnes-RSS** eller **Minnesbearbetningsuppsättning**i listrutan **Mått** . **Minnes-RSS** stöds endast för Kubernetes version 1.8 och senare. Annars visar du värden för **Min&nbsp; ** som *NaN&nbsp;*, vilket är ett numeriskt datatypsvärde som representerar ett odefinierat eller orepresenterbart värde.

![Prestandavy för behållanoder](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Minnesbearbetningsuppsättningen** visar både det inhemska minnet och det virtuella minnet (cache) som ingår och är totalt vad programmet använder. **Minne RSS** visar bara huvudminne (vilket är inget annat än bosatt minne med andra ord). Det här måttet visar den faktiska kapaciteten för tillgängligt minne. Vad är skillnaden mellan residentt minne och virtuellt minne?

- Resident minne eller huvudminne, är den faktiska mängden datorminne som är tillgänglig för noderna i klustret.

- Virtuellt minne är reserverat hårddiskutrymme (cache) som används av operativsystemet för att byta data från minne till disk när under minnestryck och sedan hämta tillbaka det till minnet när det behövs.

Som standard baseras prestandadata på de senaste sex timmarna, men du kan ändra fönstret med alternativet **TimeRange** längst upp till vänster. Du kan också filtrera resultaten inom tidsintervallet genom att välja **Min,** **Medel,** **50,** **90,** **95:e**och **Max** i percentilväljaren. 

![Percentilval för datafiltrering](./media/container-insights-analyze/containers-metric-percentile-filter.png)

När du hovrar över stapeldiagrammet under kolumnen **Trend** visar varje stapel antingen CPU- eller minnesanvändning, beroende på vilket mått som väljs, inom en exempelperiod på 15 minuter. När du har valt trenddiagrammet via ett tangentbord använder du Alt+Page up-tangenten eller Alt+Page down-tangenten för att bläddra igenom varje stapel individuellt. Du får samma information som du skulle om du svävade över baren.

![Exempel på hovringsdiagram i trendraddiagram](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

I nästa exempel är värdet för **Behållare** *aks-nodepool1-* 9 för den första noden i listan. Det här värdet är en sammanslagning av det totala antalet distribuerade behållare.

![Exempel på sammanslagning av behållare per nod](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Den här informationen kan hjälpa dig att snabbt identifiera om du har ett korrekt saldo för behållare mellan noder i klustret. 

Informationen som visas när du visar fliken **Noder** beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden. |
| Status | Kubernetes vy av nodstatus. |
| Min&nbsp;%,&nbsp;Medel %, 50:e&nbsp;%, 90:e&nbsp;%, 95:e&nbsp;%, Max&nbsp;%  | Genomsnittlig nodprocent baserat på percentil under den valda varaktigheten. |
| Min, Medel, 50: e, 90: e, 95: e, Max | Genomsnittligt noders faktiska värde baserat på percentil under den valda tidslängden. Medelvärdet mäts från den CPU/minnesgräns som angetts för en nod. För poddar och behållare är det det genomsnittliga värdet som rapporteras av värden. |
| Containrar | Antal behållare. |
| Upptid | Representerar tiden sedan en nod startades eller startades om. |
| Controller | Endast för containrar och kapslar. Den visar vilken styrenhet den finns i. Alla poddar finns inte i en handkontroll, så vissa kan visa **N/A**. | 
| Trend&nbsp;Min %,&nbsp;Medel %,&nbsp;50:e&nbsp;%, 90:e %, 95:e&nbsp;%, Max&nbsp;% | Stapeldiagramtrenden representerar den genomsnittliga percentilmåttprocenten för styrenheten. |

Du kan märka en arbetsbelastning efter att ha expanderat en nod med namnet **Annan process**. Den representerar processer som inte är behållare som körs på noden och innehåller:

* Självhanterade eller hanterade Kubernetes processer som inte är behållare

* Körningsprocesser för behållare  

* Kubelet  

* Systemprocesser som körs på noden

* Andra icke-Kubernetes-arbetsbelastningar som körs på nodmaskinvara eller virtuell dator

Den beräknas av: *Total användning från CAdvisor-användning* - *från containerprocess*.  

Välj **Controllers**i väljaren .

![Välj kontrollanter vyn](./media/container-insights-analyze/containers-controllers-tab.png)

Här kan du visa prestandahälsa för dina styrenheter och behållarinstanser virtuella nodkontroller eller virtuella nodenheter som inte är anslutna till en styrenhet.

![\<Namn> styrenheters prestandavy](./media/container-insights-analyze/containers-controllers-view.png)

Radhierarkin börjar med en styrenhet. När du expanderar en handkontroll visar du en eller flera poddar. Expandera en pod och den sista raden visar behållaren grupperad till podden. Från en utökad styrenhet kan du öka detaljnivån till den nod som körs på för att visa prestandadata filtrerade för den noden. Container instances pods som inte är anslutna till en styrenhet visas sist i listan.

![Exempel på kontrollanter hierarki med containerinstanser poddar listade](./media/container-insights-analyze/controllers-view-aci.png)

Välj värdet under kolumnen **Nod** för den specifika styrenheten.

![Exempel detaljgranskning från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

Den information som visas när du visar styrenheter beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Sammanslagningsstatusen för behållarna när den har körts klart med status som *OK*, *Avslutad*, *Stoppad*, *Stoppad*eller *Pausad*. Om behållaren körs men statusen antingen inte visades korrekt eller inte plockades upp av agenten och har inte svarat på mer än 30 minuter, är statusen *Okänd*. Ytterligare information om statusikonen finns i följande tabell.|
| Min&nbsp;%,&nbsp;Medel %, 50:e&nbsp;%, 90:e&nbsp;%, 95:e&nbsp;%, Max&nbsp;%| Sammanslagningsgenomsnittet för den genomsnittliga procentandelen för varje entitet för det valda måttet och percentilen. |
| Min, Medel, 50: e, 90: e, 95: e, Max  | Sammanslagning av den genomsnittliga CPU-millicore- eller minnesprestandan för behållaren för den valda percentilen. Medelvärdet mäts från den CPU/Minnesgräns som angetts för en pod. |
| Containrar | Totalt antal behållare för styrenheten eller podden. |
| Startar | Sammanslagning av antalet omstarter från behållare. |
| Upptid | Representerar tiden sedan en behållare startades. |
| Node | Endast för containrar och kapslar. Den visar vilken styrenhet den finns i. | 
| Trend&nbsp;Min %,&nbsp;Medel %,&nbsp;50:e&nbsp;%, 90:e %, 95:e&nbsp;%, Max&nbsp;% | Stapeldiagramtrenden representerar styrenhetens genomsnittliga percentilmått. |

Ikonerna i statusfältet anger behållarnas onlinestatus.
 
| Ikon | Status | 
|--------|-------------|
| ![Statusikon för klar körning](./media/container-insights-analyze/containers-ready-icon.png) | Köra (klar)|
| ![Ikon för vänte- eller pausad status](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar eller pausas|
| ![Statusikon för senast rapporterad körning](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterade körs men har inte svarat på mer än 30 minuter|
| ![Ikon för lyckad status](./media/container-insights-analyze/containers-green-icon.png) | Har stoppats eller misslyckats med att stoppa|

Statusikonen visar ett antal baserat på vad podden tillhandahåller. Den visar de värsta två lägena, och när du hovrar över statusen visas en sammanslagningsstatus från alla poddar i behållaren. Om det inte finns ett färdigt tillstånd visas statusvärdet **(0)**.

Välj **Behållare**i väljaren .

![Välj behållare vyn](./media/container-insights-analyze/containers-containers-tab.png)

Här kan du visa prestandahälsan för dina Azure Kubernetes- och Azure Container Instances-behållare. 

![\<Namn> behållares prestandavy](./media/container-insights-analyze/containers-containers-view.png)

Från en behållare kan du öka detaljnivån till en pod eller nod för att visa prestandadata som filtrerats för det objektet. Välj värdet under kolumnen **Pod** eller **Nod** för den specifika behållaren.

![Exempel detaljgranskning från nod till behållare i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

Informationen som visas när du visar behållare beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Eventuella behållares status. Ytterligare information om statusikonen finns i nästa tabell.|
| Min&nbsp;%,&nbsp;Medel %, 50:e&nbsp;%, 90:e&nbsp;%, 95:e&nbsp;%, Max&nbsp;% | Sammanslagningen av den genomsnittliga procentandelen för varje entitet för det valda måttet och percentilen. |
| Min, Medel, 50: e, 90: e, 95: e, Max | Sammanslagningen av den genomsnittliga CPU-millicore- eller minnesprestandan för behållaren för den valda percentilen. Medelvärdet mäts från den CPU/Minnesgräns som angetts för en pod. |
| Pod | Behållare där podden finns.| 
| Node |  Nod där behållaren finns. | 
| Startar | Representerar tiden sedan en behållare startades. |
| Upptid | Representerar tiden sedan en behållare startades eller startades om. |
| Trend&nbsp;Min %,&nbsp;Medel %,&nbsp;50:e&nbsp;%, 90:e %, 95:e&nbsp;%, Max&nbsp;% | Stapeldiagramtrenden representerar den genomsnittliga percentilmåttprocenten för behållaren. |

Ikonerna i statusfältet anger poddars onlinestatus, enligt beskrivningen i följande tabell.
 
| Ikon | Status |  
|--------|-------------|  
| ![Statusikon för klar körning](./media/container-insights-analyze/containers-ready-icon.png) | Köra (klar)|  
| ![Ikon för vänte- eller pausad status](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar eller pausas|  
| ![Statusikon för senast rapporterad körning](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterade igång men har inte svarat på mer än 30 minuter|  
| ![Ikon för avslutad status](./media/container-insights-analyze/containers-terminated-icon.png) | Har stoppats eller misslyckats med att stoppa|  
| ![Ikon för misslyckad status](./media/container-insights-analyze/containers-failed-icon.png) | Tillståndet misslyckades |  

## <a name="workbooks"></a>Arbetsböcker

Arbetsböcker kombinerar text, [loggfrågor,](../log-query/query-language.md) [mått](../platform/data-platform-metrics.md)och parametrar i avancerade interaktiva rapporter. Arbetsböcker kan redigeras av andra teammedlemmar som har åtkomst till samma Azure-resurser.

Azure Monitor för behållare innehåller fyra arbetsböcker för att komma igång:

- **Diskkapacitet:** Visar interaktiva diskanvändningsdiagram för varje disk som presenteras för noden i en behållare med följande perspektiv:

    - Disk procentanvändning för alla diskar.
    - Gratis diskutrymme för alla diskar.
    - Ett rutnät som visar varje nods disk, dess procentandel av använt utrymme, trend av procentandel av använt utrymme, ledigt diskutrymme (GiB) och trenden för ledigt diskutrymme (GiB). När en rad är markerad i tabellen visas procentandelen använt utrymme och ledigt diskutrymme (GiB) under raden. 

- **Disk-I/O:** Presenterar interaktiva diskanvändningsdiagram för varje disk som presenteras för noden i en behållare med följande perspektiv:

    - Disk I/O sammanfattas på alla diskar med läs byte/sek, skriver bytes/sek och läser och skriver byte/sek-trender.
    - Åtta resultatdiagram visar nyckeltal för att mäta och identifiera disk-I/O-flaskhalsar.

- **Kubelet**: Innehåller två rutnät som visar nyckelnoddriftsstatistik:

    - Översikt per nod rutnät sammanfattar totala åtgärder, totala fel och framgångsrika operationer efter procent och trend för varje nod.
    - Översikt efter operationstyp sammanfattar för varje operation den totala åtgärden, totala fel och lyckade operationer efter procent och trend.

- **Nätverk**: Visar interaktiva nätverksanvändningsdiagram för varje nods nätverkskort och ett rutnät visar de viktigaste prestandaindikatorerna för att mäta nätverkskortens prestanda.

Du öppnar dessa arbetsböcker genom att välja var och en i listrutan **Visa arbetsböcker.**

![Lista över arbetsböcker](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Nästa steg

- Granska [Skapa prestandaaviseringar med Azure Monitor för behållare för](container-insights-alerts.md) att lära dig hur du skapar aviseringar för hög CPU- och minnesanvändning för att stödja dina DevOps eller operativa processer och procedurer.

- Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill visa fördefinierade frågor och exempel som du kan utvärdera eller anpassa för att avisera, visualisera eller analysera kluster.

- Visa [övervakarkhälsa](container-insights-health.md) om du vill veta mer om hur du visar hälsostatusen för Kubernetes-klustret.
