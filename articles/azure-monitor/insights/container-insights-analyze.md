---
title: Övervaka AKS kluster prestanda med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du kan visa och analysera prestanda-och loggdata med Azure Monitor för behållare.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/17/2019
ms.openlocfilehash: a65951a90767acce5570244af8a9250845e12def
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554242"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Förstå AKS kluster prestanda med Azure Monitor för behållare
Med Azure Monitor för behållare kan du använda prestanda diagram och hälso status för att övervaka arbets belastningen för dina Azure Kubernetes service (AKS)-kluster från två perspektiv. Du kan övervaka direkt från ett AKS-kluster, eller så kan du övervaka alla AKS-kluster i en prenumeration från Azure Monitor. Det är också möjligt att Visa Azure Container Instances när du övervakar ett speciellt AKS-kluster.

Den här artikeln hjälper dig att förstå de två perspektiven och hur Azure Monitor hjälper dig att snabbt utvärdera, undersöka och lösa identifierade problem.

Information om hur du aktiverar Azure Monitor för behållare finns i [Onboard Azure Monitor for containers](container-insights-onboard.md).

Azure Monitor ger en vy med flera kluster som visar hälso status för alla övervakade AKS-kluster som kör Linux och Windows Server 2019 som har distribuerats över resurs grupper i dina prenumerationer. Det visar AKS-kluster som identifieras som inte övervakas av lösningen. Du kan omedelbart förstå kluster hälsa och härifrån kan du gå nedåt till sidan prestanda för nod och kontrollant eller gå till Visa prestanda diagram för klustret. För AKS-kluster som har identifierats och identifierats som oövervakade kan du aktivera övervakning när som helst. 

De största skillnaderna vid övervakning av ett Windows Server-kluster med Azure Monitor för behållare jämfört med ett Linux-kluster är följande:

- Minnets RSS-mått är inte tillgängligt för Windows-noden och behållare.
- Information om disk lagrings kapacitet är inte tillgänglig för Windows-noder.
- Det finns stöd för Live-loggar med undantag för Windows container-loggar.
- Endast Pod-miljöer övervakas, inte Docker-miljöer.
- I för hands versionen stöds maximalt 30 Windows Server-behållare. Den här begränsningen gäller inte för Linux-behållare. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vy över flera kluster från Azure Monitor

Om du vill visa hälso status för alla AKS-kluster som distribueras väljer du **övervaka** i det vänstra fönstret i Azure Portal. Under avsnittet **insikter** väljer du **behållare**. 

![Exempel på instrument panel för flera kluster Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

På fliken **övervakade kluster** kan du lära dig följande:

- Hur många kluster som är i ett kritiskt eller ohälsosamt tillstånd, jämfört med hur många som är felfria eller inte rapporterar (kallas okänt tillstånd).
- Om alla [Azure Kubernetes Engine-distributioner (AKS-Engine)](https://github.com/Azure/aks-engine) är felfria.
- Hur många noder och användar-och system-poddar som distribueras per kluster.
- Hur mycket disk utrymme som är tillgängligt och om det finns ett kapacitets problem.

De hälso status värden som ingår: 

* **Felfri**: inga problem har identifierats för den virtuella datorn och fungerar som obligatoriskt. 
* **Kritiskt**: ett eller flera kritiska problem har identifierats som måste åtgärdas för att återställa det normala operativa läget som förväntat.
* **Varning**! ett eller flera problem har identifierats som måste åtgärdas eller så kan hälso tillståndet bli kritiskt.
* **Okänd**: om tjänsten inte kunde upprätta en anslutning till noden eller Pod ändras statusen till ett okänt tillstånd.
* **Hittades inte**: arbets ytan, resurs gruppen eller prenumerationen som innehåller arbets ytan för den här lösningen har tagits bort.
* **Tillåts**inte: användaren har inte behörighet att läsa data i arbets ytan.
* **Fel**: ett fel uppstod vid försök att läsa data från arbets ytan.
* **Felkonfigurerad**: Azure Monitor för behållare har inte kon figurer ATS korrekt på den angivna arbets ytan.
* **Inga data**: data har inte rapporter ATS till arbets ytan under de senaste 30 minuterna.

Hälso tillstånd beräknar övergripande kluster status som *sämsta av* de tre tillstånden med ett undantag. Om något av de tre tillstånden är okänt, visar det övergripande kluster tillståndet **Okänt**. 

Följande tabell innehåller en analys av beräkningen som styr hälso tillståndet för ett övervakat kluster i vyn över flera kluster.

| |Status |Tillgänglighet |  
|-------|-------|-----------------|  
|**Användarens Pod**| | |  
| |Felfri |100 % |  
| |Varning |90 – 99% |  
| |Kritiskt |< 90% |  
| |Okänt |Om de inte har rapporter ATS under de senaste 30 minuterna |  
|**Systemets Pod**| | |  
| |Felfri |100 % |
| |Varning |Gäller inte |
| |Kritiskt |< 100% |
| |Okänt |Om de inte har rapporter ATS under de senaste 30 minuterna |
|**Node** | | |
| |Felfri |> 85% |
| |Varning |60 – 84% |
| |Kritiskt |< 60% |
| |Okänt |Om de inte har rapporter ATS under de senaste 30 minuterna |

I listan över kluster kan du öka detalj nivån till **kluster** sidan genom att välja namnet på klustret. Gå sedan till prestanda sidan **noder** genom att välja sammanslagning av noder i kolumnen **noder** för det aktuella klustret. Du kan också öka detalj nivån till sidan **kontrollanter** genom att välja sammanslagning av **användaren poddar** eller **system poddar** kolumnen.  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Visa prestanda direkt från ett AKS-kluster

Åtkomst till Azure Monitor för behållare är tillgänglig direkt från ett AKS-kluster genom att välja **insikter** i den vänstra rutan. Information om ditt AKS-kluster är indelat i fyra perspektiv:

- Kluster
- Noder 
- Domänkontrollanter 
- Containers

Standard sidan öppnas när du väljer **insikter**  > -**kluster**. Fyra linje prestanda diagram visar viktiga prestanda mått för klustret. 

![Exempel på prestanda diagram på fliken kluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Prestanda diagrammen visar fyra prestanda mått:

- **Nodens CPU-användning &nbsp; %** : ett sammanställt perspektiv av processor belastning för hela klustret. Om du vill filtrera resultaten för tidsintervallet väljer du **AVG**, **min**, **50**, **nittionde**, **95**eller **Max** i percentils väljaren ovanför diagrammet. Filtren kan antingen användas individuellt eller tillsammans. 
- **Nod-minnes användning &nbsp; %** : ett sammanlagt perspektiv minnes användning för hela klustret. Om du vill filtrera resultaten för tidsintervallet väljer du **AVG**, **min**, **50**, **nittionde**, **95**eller **Max** i percentils väljaren ovanför diagrammet. Filtren kan antingen användas individuellt eller tillsammans. 
- **Antal noder**: antal noder och status från Kubernetes. Status för de klusternoder som visas är total, klar och inte redo. De kan filtreras separat eller kombineras i väljaren ovanför diagrammet. 
- **Antal aktiva Pod**: ett Pod-antal och status från Kubernetes. Status för poddar som visas är total, väntar, körs, okänd, lyckades eller misslyckades. De kan filtreras separat eller kombineras i väljaren ovanför diagrammet. 

Använd vänster och höger pilknappar för att bläddra igenom varje data punkt i diagrammet. Använd upp-och nedpilen för att gå igenom percentils linjerna. Välj PIN-ikonen i det övre högra hörnet i något av diagrammen för att fästa det valda diagrammet på den sista Azure-instrumentpanelen som du visade. På instrument panelen kan du ändra storlek på och flytta diagrammet. Genom att välja diagrammet på instrument panelen omdirigeras du till Azure Monitor för behållare och läsa in rätt omfång och vy.

Azure Monitor för behållare stöder också Azure Monitor [Metrics Explorer](../platform/metrics-getting-started.md), där du kan skapa egna rit diagram, korrelera och undersöka trender och fästa på instrument paneler. Från metrics Explorer kan du också använda de kriterier som du anger för att visualisera måtten som grund för en [Metric-baserad aviserings regel](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Visa behållar mått i Metrics Explorer

I Metrics Explorer kan du visa aggregerade noder och Pod användnings mått från Azure Monitor för behållare. I följande tabell sammanfattas information som hjälper dig att förstå hur du använder mått diagram för att visualisera container mått.

|Namnområde | Mått | Beskrivning | 
|----------|--------|-------------|
| Insights. container/Nodes | |
| | cpuUsageMillicores | Aggregerad mätning av CPU-belastning i klustret. Det är en processor kärna som delas upp i 1000 enheter (Milli = 1000). Används för att fastställa användningen av kärnor i en behållare där många program kan använda en kärna.| 
| | cpuUsagePercentage | Aggregerad genomsnittlig processor användning mätt i procent över klustret.|
| | memoryRssBytes | RSS-minne för behållare som används i byte.| 
| | memoryRssPercentage | RSS-minne för behållare som används i procent.|
| | memoryWorkingSetBytes | Minne för arbets minne för behållare som används.| 
| | memoryWorkingSetPercentage | Arbets minne för behållare som används i procent. | 
| | nodesCount | Antalet noder från Kubernetes.|
| Insights. container/poddar | |
| | podCount | Ett Pod-antal från Kubernetes.|

Du kan [dela upp](../platform/metrics-charts.md#apply-splitting-to-a-chart) ett mått för att visa det efter dimension och visualisera hur olika segment jämförs med varandra. För en nod kan du segmentera diagrammet efter *värd* dimension. Från en POD kan du segmentera den med följande dimensioner:

* Domänkontrollant
* Kubernetes-namnrymd
* Nod
* Fas

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysera noder, styrenheter och hälso tillstånd för behållare

När du växlar till flikarna **noder**, **kontrollanter**och **behållare** visas ett egenskaps fönster automatiskt till höger på sidan. Den visar egenskaperna för det valda objektet, som innehåller de etiketter som du har definierat för att organisera Kubernetes-objekt. När en Linux-nod väljs visas även det tillgängliga disk utrymmet i avsnittet **lokal disk kapacitet** och den procent andel som används för varje disk som visas för noden. Välj länken **>>** i fönstret om du vill visa eller dölja fönstret.

![Exempel egenskaps fönster för Kubernetes-perspektiv](./media/container-insights-analyze/perspectives-preview-pane-01.png)

När du expanderar objekten i hierarkin uppdateras egenskaps fönstret baserat på det valda objektet. I fönstret kan du också Visa Kubernetes-händelser med fördefinierade loggs ökningar genom att välja länken **Visa Kubernetes händelse loggar** överst i fönstret. Mer information om hur du visar Kubernetes-loggdata finns i [Sök efter loggar för att analysera data](container-insights-log-search.md). När du granskar kluster resurser kan du se behållar loggar och händelser i real tid. Mer information om den här funktionen och den konfiguration som krävs för att bevilja och kontrol lera åtkomst finns i [Visa loggar i real tid med Azure Monitor för behållare](container-insights-live-logs.md). 

Använd alternativet **+ Lägg till filter** överst på sidan för att filtrera resultaten för vyn efter **tjänst**, **nod**, **namnrymd**eller **Node-pool**. När du har valt filter omfånget väljer du ett av värdena som visas i fältet **Välj värde (n)** . När filtret har kon figurer ATS tillämpas det globalt samtidigt som du tittar på AKS-klustrets perspektiv. Formeln stöder bara likhets tecknet. Du kan lägga till ytterligare filter ovanpå den första för att ytterligare begränsa resultaten. Om du till exempel anger ett filter per **nod**kan du bara välja **tjänst** eller **namnrymd** för det andra filtret.

![Exempel med hjälp av filtret för att begränsa resultaten](./media/container-insights-analyze/add-filter-option-01.png)

Att ange ett filter på en flik fortsätter att gälla när du väljer en annan. Den tas bort när du har markerat **x** -symbolen bredvid det angivna filtret. 

Växla till fliken **noder** och-hierarkin följer Kubernetes objekt modell, som börjar med en nod i klustret. Expandera noden om du vill visa en eller flera poddar som körs på noden. Om fler än en behållare är grupperade till en POD visas de som den sista raden i hierarkin. Du kan också se hur många icke-Pod arbets belastningar som körs på värden om värden har processor-eller minnes belastning.

![Exempel på Kubernetes-hierarkin i vyn prestanda](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-behållare som kör Windows Server 2019 OS visas efter alla Linux-baserade noder i listan. När du expanderar en Windows Server-nod kan du Visa en eller flera poddar och behållare som körs på noden. När en nod har marker ATS visas versions information i fönstret Egenskaper. Agent informationen är exkluderad eftersom Windows Server-noder inte har någon agent installerad. 

![Exempel på Node-hierarki med Windows Server-noder listade](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances virtuella noder som kör Linux-OS visas efter den sista AKS i listan. När du expanderar en Container Instances virtuell nod kan du Visa en eller flera Container Instances-poddar och behållare som körs på noden. Mått samlas inte in och rapporteras inte för noder, endast för poddar.

![Exempel på en Node-hierarki med Container Instances listad](./media/container-insights-analyze/nodes-view-aci.png)

Från en utökad nod kan du öka detalj nivån från POD eller containern som körs på noden till kontrollanten för att visa prestanda data som filtrerats för kontrollanten. Välj värdet i kolumnen **kontrollant** för den angivna noden.
 
![Exempel gå nedåt från noden till kontrollanten i vyn prestanda](./media/container-insights-analyze/drill-down-node-controller.png)

Välj kontrollanter eller behållare högst upp på sidan för att granska status och resursutnyttjande för dessa objekt. Om du vill granska minnes användningen väljer du **minnes-RSS** eller **minnes arbets minne**i list rutan **mått** . **RSS-minnen** stöds bara för Kubernetes version 1,8 och senare. Annars kan du Visa värden för **Min &nbsp; %** som *Nan &nbsp; %* , vilket är ett numeriskt värde för data typen som representerar ett odefinierat eller värde som inte kan återges.

![Prestanda vy för container noder](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Minnes arbets minnet** visar både det residenta minnet och det virtuella minnet (cache) som ingår och är det totala antalet som programmet använder. **Minne RSS** visar bara huvud minnet (vilket är inget men det residenta minnet med andra ord). Det här måttet visar den faktiska kapaciteten för tillgängligt minne. Vad är skillnaden mellan Resident minne och virtuellt minne?

- Inbyggt minne eller primärt minne är den faktiska mängden maskin minne som är tillgängligt för noderna i klustret.

- Det virtuella minnet är ett reserverat hårddisk utrymme (cache) som används av operativ systemet för att växla data från minne till disk när de är under minnes belastning och sedan hämta tillbaka dem till minnet när det behövs.

Som standard baseras prestanda data på de senaste sex timmarna, men du kan ändra fönstret genom att använda alternativet **TimeRange** längst upp till vänster. Du kan också filtrera resultaten inom tidsintervallet genom att välja **min**, **AVG**, **50**, **nittionde**, **95**och **Max** i percentils väljaren. 

![Val av percentil för data filtrering](./media/container-insights-analyze/containers-metric-percentile-filter.png)

När du hovrar över stapeldiagrammet under kolumnen **trend** visar varje STAPEL antingen processor-eller minnes användning, beroende på vilket mått som valts, inom en exempel period på 15 minuter. När du har valt trend diagrammet i ett tangent bord, använder du tangenterna ALT + PGUP eller ALT + PGDN för att gå igenom varje stapel individuellt. Du får samma information som om du hovrar över fältet.

![Trend linje diagram hov ring – över exempel](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

I nästa exempel, för den första noden i listan, *AKS-nodepool1*, är värdet för **containers** 9. Det här värdet är en sammanfattning av det totala antalet distribuerade behållare.

![Samla in behållare per nod-exempel](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Den här informationen kan hjälpa dig att snabbt identifiera om du har en korrekt balans för behållare mellan noder i klustret. 

Den information som visas när du visar fliken **noder** beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden. |
| Status | Kubernetes visar nodens status. |
| Minsta &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; %  | Genomsnittlig nod i procent baserat på percentil under den valda varaktigheten. |
| Min, AVG, 50, nittionde, 95, max | Genomsnittligt antal noders faktiska värde baserat på percentil under den valda tids perioden. Det genomsnittliga värdet mäts från PROCESSORns/minnes gränsen som angetts för en nod. För poddar och behållare är det det genomsnittliga värdet som rapporteras av värden. |
| Containers | Antal behållare. |
| Drift tid | Visar tiden sedan en nod startades eller startades om. |
| Domänkontrollant | Endast för behållare och poddar. Den visar vilken kontrollant den finns i. Alla poddar finns inte i en kontrollant, så vissa kan visa **ej tillämpligt**. | 
| Trend för min &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; % | Stapeldiagrams trend representerar den genomsnittliga percentilvärdet i procent av styrenheten. |

I väljaren väljer du **kontrollanter**.

![Välj kontrollanter-vy](./media/container-insights-analyze/containers-controllers-tab.png)

Här kan du Visa hälso tillståndet för dina styrenheter och Container Instances virtuella poddar eller virtuella noder som inte är anslutna till en kontrollant.

![prestanda vy för \<Name > controllers](./media/container-insights-analyze/containers-controllers-view.png)

Row-hierarkin börjar med en kontroll enhet. När du expanderar en kontrollant visar du en eller flera poddar. Expandera en POD och den sista raden visar behållaren som är grupperad mot pod. Från en utökad kontroll enhet kan du öka detalj nivån till den nod som körs på för att visa prestanda data som filtrerats för noden. Container Instances poddar som inte är anslutna till en kontrollant visas sist i listan.

![Exempel på Controller-hierarki med Container Instances poddar listad](./media/container-insights-analyze/controllers-view-aci.png)

Välj värdet i kolumnen **nod** för den aktuella kontrollanten.

![Exempel gå nedåt från noden till kontrollanten i vyn prestanda](./media/container-insights-analyze/drill-down-controller-node.png)

Den information som visas när du visar kontrollanter beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Sammanslagnings statusen för behållarna när den har slutförts med status, till exempel *OK*, *avslutad*, *misslyckad*, *stoppad*eller *pausad*. Om behållaren körs men status antingen inte visas korrekt eller inte har hämtats av agenten och inte har svarat i mer än 30 minuter, är statusen *okänd*. Ytterligare information om status ikonen finns i följande tabell.|
| Minsta &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; %| Beräknat medelvärde för den genomsnittliga procent andelen av varje enhet för det valda måttet och percentilen. |
| Min, AVG, 50, nittionde, 95, max  | Sammanslagning av genomsnittlig CPU-Millicore eller minnes prestanda för den valda percentilen. Det genomsnittliga värdet mäts från PROCESSORns/minnes gränsen som angetts för en pod. |
| Containers | Totalt antal behållare för styrenhets-eller pod. |
| Startar om | Sammanslagning av antalet omstarter från behållare. |
| Drift tid | Representerar tiden sedan en container startades. |
| Nod | Endast för behållare och poddar. Den visar vilken kontrollant den finns i. | 
| Trend för min &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; % | Stapeldiagrams trend representerar den genomsnittliga percentilvärdet för styrenheten. |

Ikonerna i fältet status anger behållar statusen för behållarna.
 
| Icon | Status | 
|--------|-------------|
| ![Status ikon för redo körning](./media/container-insights-analyze/containers-ready-icon.png) | Körs (klar)|
| ![Ikon för väntande eller pausad status](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar eller pausas|
| ![Ikon för senast rapporterad körnings status](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterat, men har inte svarat i mer än 30 minuter|
| ![Status ikon för lyckad status](./media/container-insights-analyze/containers-green-icon.png) | Stoppades eller kunde inte stoppas|

Status ikonen visar ett antal baserat på vad Pod tillhandahåller. Det visar de två sämsta tillstånden och när du hovrar över statusen visas en sammanslagnings status från alla poddar i behållaren. Om det inte finns ett klart status värde visas **(0)** .

I väljar väljer du **behållare**.

![Välj behållare-vy](./media/container-insights-analyze/containers-containers-tab.png)

Här kan du Visa prestanda hälsan för dina Azure-Kubernetes och Azure Container Instances behållare. 

![prestanda vy för \<Name > behållare](./media/container-insights-analyze/containers-containers-view.png)

Från en behållare kan du öka detalj nivån till en POD eller nod för att visa prestanda data som filtrerats för objektet. Välj värdet i kolumnen **Pod** eller **Node** för den angivna behållaren.

![Exempel på att öka detalj nivån från noden till behållare i vyn prestanda](./media/container-insights-analyze/drill-down-controller-node.png)

Den information som visas när du visar behållare beskrivs i följande tabell.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Status för behållarna, om det finns några. Ytterligare information om status ikonen finns i nästa tabell.|
| Minsta &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; % | Sammanställning av den genomsnittliga procent andelen av varje enhet för det valda måttet och percentilen. |
| Min, AVG, 50, nittionde, 95, max | Sammanslagning av genomsnittlig CPU-Millicore eller minnes prestanda för behållaren för den valda percentilen. Det genomsnittliga värdet mäts från PROCESSORns/minnes gränsen som angetts för en pod. |
| Pod | Behållare där Pod finns.| 
| Nod |  Noden där behållaren finns. | 
| Startar om | Representerar tiden sedan en container startades. |
| Drift tid | Visar tiden sedan en behållare startades eller startades om. |
| Trend för min &nbsp;%, AVG &nbsp;%, 50 &nbsp;%, nittionde &nbsp;%, 95 &nbsp;%, Max &nbsp; % | Stapeldiagrams trend representerar den genomsnittliga percentilvärdet i procent av behållaren. |

Ikonerna i fältet status anger online-status för poddar, enligt beskrivningen i följande tabell.
 
| Icon | Status |  
|--------|-------------|  
| ![Status ikon för redo körning](./media/container-insights-analyze/containers-ready-icon.png) | Körs (klar)|  
| ![Ikon för väntande eller pausad status](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar eller pausas|  
| ![Ikon för senast rapporterad körnings status](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterad, men har inte svarat på mer än 30 minuter|  
| ![Ikon för avslutat status](./media/container-insights-analyze/containers-terminated-icon.png) | Stoppades eller kunde inte stoppas|  
| ![Ikon för misslyckad status](./media/container-insights-analyze/containers-failed-icon.png) | Felaktigt tillstånd |  

## <a name="workbooks"></a>Arbetsböcker

Arbets böcker kombinerar text, [logg frågor](../log-query/query-language.md), [mått](../platform/data-platform-metrics.md)och parametrar till omfattande interaktiva rapporter. Arbets böcker kan redige ras av andra team medlemmar som har åtkomst till samma Azure-resurser.

Azure Monitor för behållare innehåller fyra arbets böcker för att komma igång:

- **Disk kapacitet**: visar interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk procents användning för alla diskar.
    - Ledigt disk utrymme för alla diskar.
    - Ett rutnät som visar varje nods disk, dess procent andel av använt utrymme, trenden i procent av använt utrymme, ledigt disk utrymme (GiB) och trenden för ledigt disk utrymme (GiB). När en rad väljs i tabellen visas procent andelen använt utrymme och ledigt disk utrymme (GiB) under raden. 

- **Disk-i/o**: visar interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk-I/O sammanfattas över alla diskar med lästa byte/s, skrivna byte/s och Läs-och skriv byte/s-trender.
    - Åtta prestanda diagram visar nyckeltal för att hjälpa till att mäta och identifiera disk-I/O-Flask halsar.

- **Kubelet**: innehåller två rutnät som visar drift statistik över viktiga noder:

    - Översikt per Node-rutnät sammanfattar total åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend för varje nod.
    - Översikt per åtgärds typ sammanfattar för varje åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend.

- **Nätverk**: visar interaktiva nätverks användnings diagram för varje nods nätverkskort och ett rutnät presenterar nyckeltalet för att mäta prestanda för nätverkskorten.

Du kommer åt dessa arbets böcker genom att välja var och en i list rutan **Visa arbets böcker** .

![Visa List rutan för arbets böcker](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Nästa steg

- Granska [skapa prestanda varningar med Azure Monitor för behållare](container-insights-alerts.md) för att lära dig hur du skapar aviseringar för hög processor-och minnes användning för att stödja DevOps eller operativa processer och procedurer.
- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa till avisering, visualisera eller analysera dina kluster.
