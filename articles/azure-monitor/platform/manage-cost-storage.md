---
title: Hantera användning och kostnader för Azure Monitor loggar | Microsoft Docs
description: Lär dig hur du ändrar pris planen och hanterar data volymer och bevarande principer för Log Analytics arbets ytan i Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 43c9ba4ff21f32ca321a62c7f11430d82dfc4ec0
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045182"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Hantera användning och kostnader med Azure Monitor loggar

> [!NOTE]
> Den här artikeln beskriver hur du förstår och styr dina kostnader för Azure Monitor loggar. En relaterad artikel, [övervaknings användning och uppskattade kostnader](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) beskriver hur du visar användning och uppskattade kostnader i flera Azure-övervakningsfunktioner för olika pris modeller.

> [!NOTE]
> Alla priser och kostnader som visas i den här artikeln är till exempel endast avsedda. 

Azure Monitor loggar har utformats för att skala och stödja insamling, indexering och lagring av stora mängder data per dag från vilken källa som helst i företaget eller som distribueras i Azure.  Detta kan vara en primära drivande faktorn för din organisation, är kostnadseffektivitet i slutändan underliggande drivrutinen. Därför är det viktigt att förstå att kostnaden för en Log Analytics-arbetsyta inte enbart baseras på mängden insamlade data, den är också beroende av den valda planen och hur länge du väljer att lagra data som genererats från dina anslutna källor.  

I den här artikeln går vi igenom hur du kan övervaka inmatade data volymer och lagrings tillväxten på ett proaktivt sätt och definiera gränser för att kontrol lera de associerade kostnaderna. 

## <a name="pricing-model"></a>Prismodell

Standard priset för Log Analytics är en modell där **du betalar per** användning baserat på data volym som matas in och eventuellt för längre data kvarhållning. Data volymen mäts som storleken på de data som ska lagras. Varje Log Analytics arbets yta debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration. Mängden data inmatning kan vara avsevärd beroende på följande faktorer: 

  - Antal hanterings lösningar som är aktiverade och deras konfiguration (t. ex. 
  - Antal övervakade virtuella datorer
  - Typ av data som samlas in från varje övervakad virtuell dator 
  
Förutom modellen betala per användning har Log Analytics **kapacitets reservations** nivåer som gör att du kan spara så mycket som 25% jämfört med priset för betala per användning. Med kapacitets reservations priset kan du köpa en reservation som börjar på 100 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning. Kapacitets reservationens nivåer har en period på 31 dagar. Under åtagande perioden kan du ändra till en kapacitets reservations nivå på högre nivå (som startar om perioden på den 31: a), men du kan inte gå tillbaka till betala per användning eller till en reservations nivå med lägre kapacitet förrän efter åtagande perioden installerats. 
[Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om hur du Log Analytics priser för betala per användning och kapacitets reservationer. 

På alla pris nivåer beräknas data volymen från en sträng representation av data som den är för beredd för att lagras. Flera [egenskaper som är gemensamma för alla data typer](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) ingår inte i beräkningen av händelse storleken, inklusive `_ResourceId`, `_ItemId`, `_IsBillable` och `_BilledSize`.

Observera också att vissa lösningar, till exempel [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) och [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), har sin egen pris modell. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Beräkna kostnaderna för att hantera din miljö 

Om du inte använder Azure Monitor loggar än kan du använda [pris Kalkylatorn för Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att beräkna kostnaden för att använda Log Analytics. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor panelen. Bläddra nedåt på sidan för att Azure Monitor och välj Log Analytics i list rutan typ.  Här kan du ange antalet virtuella datorer och de GB data GB som du förväntar dig att samla in från varje virtuell dator. Normalt används 1 till 3 GB data månad från en typisk virtuell Azure-dator. Om du redan utvärderar Azure Monitor loggar redan kan du använda din data statistik från din egen miljö. Nedan finns information om hur du fastställer [antalet övervakade virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) och [mängden data som din arbets yta](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)tar med. 

## <a name="understand-your-usage-and-estimate-costs"></a>Förstå din användning och beräkna kostnader

Om du använder Azure Monitor loggar nu är det enkelt att förstå vad kostnaderna beror på de senaste användnings mönstren. Det gör du genom att använda **Log Analytics användning och beräknade kostnader** för att granska och analysera data användningen. Detta visar hur mycket data som samlas in av varje lösning, hur mycket data som behålls och en uppskattning av dina kostnader baserat på mängden data som matas in och eventuell ytterligare kvarhållning utöver den mängd som ingår.

![Användning och uppskattade kostnader](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Om du vill utforska dina data i mer detalj klickar du på ikonen längst upp höger på något av diagrammen i den **användning och uppskattade kostnader** sidan. Nu kan du arbeta med den här frågan att utforska mer information om din användning.  

![Visa loggar](media/manage-cost-storage/logs.png)

På sidan **användning och uppskattade kostnader** kan du granska din data volym för månaden. Detta omfattar alla data tas emot och bevaras i Log Analytics-arbetsytan.  Klicka på **användnings information** överst på sidan för att Visa användnings instrument panelen med information om data volym trender efter källa, datorer och erbjudande. Visa och ange en daglig högsta gräns eller ändra kvarhållningsperioden klickar du på **Datavolymhantering**.
 
Log Analytics avgifter läggs till i Azure-fakturan. Du kan se information om din Azure fakturerar under avsnittet fakturering i Azure Portal eller i den [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visa Log Analytics användning på din Azure-faktura 

Azure ger en fantastisk mängd användbara funktioner i [Azure Cost Management + fakturerings](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hubben. Med funktionen "cost Analysis" kan du till exempel Visa dina utgifter för Azure-resurser. Genom att lägga till ett filter efter resurs typ (till Microsoft. operationalinsights/arbets yta för Log Analytics) kan du spåra dina utgifter.

Du kan få mer förståelse för användningen genom att [Ladda ned din användning från Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). I det hämtade kalkyl bladet kan du se användning per Azure-resurs (t. ex. Log Analytics arbets yta) per dag. I det här Excel-kalkylbladet hittar du användning från dina Log Analytics-arbetsytor genom att först filtrera fram kolumnen "mätar kategori" för att Visa insikter och analyser (som används av några av de äldre pris nivåerna) och "Log Analytics" och sedan lägga till ett filter på instansen ID "-kolumnen som innehåller arbets ytan". Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "enhets mått".  Mer information finns för att hjälpa dig att [förstå din Microsoft Azure faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Ändra pris nivå

Om du vill ändra Log Analytics pris nivå för arbets ytan, 

1. I Azure Portal öppnar du **användning och uppskattade kostnader** från arbets ytan där du ser en lista över de pris nivåer som är tillgängliga för den här arbets ytan.

2. Granska de beräknade kostnaderna för var och en av pris nivåerna. Den här uppskattningen baseras på de senaste 31 användnings dagarna, så den här kostnads uppskattningen är beroende av de senaste 31 dagarna som är representativ för din typiska användning. I exemplet nedan kan du se hur, baserat på data mönstren från de senaste 31 dagarna, att den här arbets ytan kostar mindre i nivån betala per användning (#1) jämfört med kapacitets nivån 100 GB/dag för kapacitet (#2).  

    ![Prisnivåer](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. När du har granskat de uppskattade kostnaderna baserat på de senaste 31 dagarna av användningen klickar du på **Välj**för att ändra pris nivån.  

Du kan också [ställa in pris nivån via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) med hjälp av `sku`-parametern (`pricingTier` i Azure Resource Manager-mallen). 

## <a name="legacy-pricing-tiers"></a>Äldre pris nivåer

Prenumerationer som hade en Log Analytics arbets yta eller Application Insights resurs i den 2 april 2018, eller som är kopplade till en Enterprise-avtal som startades före den 1 februari 2019, fortsätter att ha åtkomst till de äldre pris nivåerna: **kostnads fri**, **fristående (per GB)** och **per nod (OMS)** .  Arbets ytor i den kostnads fria pris nivån har en daglig data inmatning som är begränsad till 500 MB (förutom säkerhets data typer som samlas in av Azure Security Center) och datakvarhållning är begränsad till 7 dagar. Den kostnads fria pris nivån är endast avsedd för utvärderings ändamål. Arbets ytorna på de fristående eller per-nodens pris nivåer har användar konfigurerbar kvarhållning på upp till två år. 

Arbets ytor som skapats före april 2016 kan också komma åt de ursprungliga pris nivåerna **standard** och **Premium** som har en fast data lagring på 30 respektive 365 dagar. Det går inte att skapa nya arbets ytor på pris nivåerna **standard** eller **Premium** , och om en arbets yta flyttas ut från dessa nivåer går det inte att flytta tillbaka den. 

Mer information om begränsningar för pris nivån finns [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Om du vill använda rättigheterna som kommer från inköp av OMS E1 Suite, OMS E2 Suite eller OMS-tillägg för System Center väljer du pris nivån Log Analytics *per nod* .

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

Följande steg beskriver hur du konfigurerar hur länge log data bevaras av i din arbetsyta.

### <a name="default-retention"></a>Standard kvarhållning

Ange standard kvarhållning för din arbets yta genom att 
 
1. I Azure Portal, från din arbets yta, väljer du **användning och uppskattade kostnader** i det vänstra fönstret.
2. På sidan **Användning och uppskattade kostnader** klickar du på **Datavolymhantering** högst upp på sidan.
3. Flytta skjutreglaget för att öka eller minska antalet dagar och klickar sedan på i fönstret **OK**.  Om du använder den *kostnadsfria* nivå, du kommer inte att kunna ändra kvarhållningsperioden för data och du måste uppgradera till betald nivå om du vill styra den här inställningen.

    ![Ändra inställning för kvarhållning av data arbets yta](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Kvarhållning kan också [ställas in via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) med hjälp av `retentionInDays`-parametern. Om du ställer in data kvarhållning på 30 dagar kan du dessutom utlösa en omedelbar rensning av äldre data med hjälp av parametern `immediatePurgeDataOn30Days`, vilket kan vara användbart för scenarier som rör kompatibilitet. Den här funktionen exponeras bara via Azure Resource Manager. 

Två data typer – `Usage` och `AzureActivity`--bevaras i 90 dagar som standard och det kostar inget att debitera för denna 90-dagars kvarhållning. Dessa data typer är också kostnads fria från data inmatnings kostnader. 

### <a name="retention-by-data-type"></a>Kvarhållning efter datatyp

Det är också möjligt att ange olika bevarande inställningar för enskilda data typer. Varje datatyp är en under resurs till arbets ytan. Till exempel kan SecurityEvent-tabellen åtgärdas i [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Observera att data typen (tabell) är Skift läges känslig.  Använd följande för att hämta de aktuella inställningarna för kvarhållning av data typer för en viss datatyp (i det här exemplet SecurityEvent):

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Om du vill hämta de aktuella inställningarna för kvarhållning per datatyp för alla data typer i din arbets yta, utelämnar du bara den specifika data typen, till exempel:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Om du vill ange kvarhållning av en viss datatyp (i det här exemplet SecurityEvent) till 730 dagar gör du

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Det går inte att ange data typerna `Usage` och `AzureActivity` med anpassad kvarhållning. De kommer att ta på max värdet för kvarhållning av standard arbets yta eller 90 dagar. 

Ett bra verktyg för att ansluta direkt till Azure Resource Manager för att ställa in kvarhållning enligt datatyp är OSS-verktyget [ARMclient](https://github.com/projectkudu/ARMClient).  Lär dig mer om ARMclient från artiklar av [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) och [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Här är en exemplet som använder ARMClient och ställer in SecurityEvent data till en 730-dagars kvarhållning:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Inställning av kvarhållning för enskilda data typer kan användas för att minska kostnaderna för datakvarhållning.  För data som samlas in från och med oktober 2019 (när den här funktionen släpptes) kan en minskning av kvarhållning av vissa data typer minska din bevarande kostnad över tid.  För data som samlas in tidigare, påverkar inte en lägre kvarhållning för en enskild typ dina bevarande kostnader.  

## <a name="manage-your-maximum-daily-data-volume"></a>Hantera din maximala dagliga data volym

Du kan konfigurera en daglig högsta gräns och begränsa den dagliga datainmatningen för arbetsytan, men var försiktig eftersom målet inte får vara att träffa den dagliga gränsen.  Annars kan förlora du data under resten av den dagen, vilket kan påverka andra Azure-tjänster och lösningar som vars funktioner kan vara beroende uppdaterad information är tillgänglig i arbetsytan.  Därför kan aviseringar din förmåga att Observera och ta emot när hälsovillkoren av resurser som stödjer IT-tjänster som påverkas.  Den dagliga gränsen är avsedd att användas som ett sätt att hantera den oväntade ökningen av data volymen från dina hanterade resurser och hålla dig inom gränsen, eller när du vill begränsa oplanerade kostnader för din arbets yta.  

När den dagliga gränsen har uppnåtts, stoppar insamlingen av fakturerbara datatyper för resten av dagen. En varning banderoll överst på sidan för den valda Log Analytics-arbetsytan och en åtgärd händelse skickas till den *åtgärden* tabellen **LogManagement** kategori. Insamling av data återupptar när återställningstiden definierats *dagliga gränsen ställs in på*. Vi rekommenderar att definiera en aviseringsregel baserat på den här åtgärden-händelser som konfigurerats för att meddela när den dagliga data gränsen har uppnåtts. 

> [!NOTE]
> Den dagliga begränsningen stoppar inte data insamlingen från Azure Security Center, förutom för arbets ytor där Azure Security Center installerades före den 19 juni 2017. 

> [!NOTE]
> Svars tiden som används för att tillämpa den dagliga begränsningen kan betyda att höljet inte används så exakt som den angivna nivån för dagligt tak. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilka dagliga datagräns definiera

Granska [Log Analytics-användning och uppskattade kostnader](usage-estimated-costs.md) att förstå till trenden för inmatning av data och vad är den dagliga Volymgränsen definiera. Det bör ses med försiktighet, eftersom du inte längre att övervaka dina resurser när gränsen har nåtts. 

### <a name="set-the-daily-cap"></a>Ange dagligt tak

Följande steg beskriver hur du konfigurerar en gräns för att hantera den data volym som Log Analytics arbets ytan kommer att ta in per dag.  

1. Välj **Användning och uppskattade kostnader** i det vänstra fönstret på arbetsytan.
2. På den **användning och uppskattade kostnader** för den valda arbetsytan och klicka på **Datavolymhantering** högst upp på sidan. 
3. Dagligt tak är **OFF** som standard – klickar du på **på** att aktivera den och ange sedan datavolymen i GB/dag.

    ![Log Analytics konfigurera data gräns](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Avisering när dagligt hölje uppnås

Medan Vi presenterar en visuell ledtråd i Azure-portalen när tröskeln för ditt data gränsen är uppfyllt, justera det här beteendet inte nödvändigtvis som du hanterar operativa problem som kräver omedelbar uppmärksamhet.  För att få en avisering, kan du skapa en ny aviseringsregel i Azure Monitor.  Mer information finns i [skapa, Visa och hantera aviseringar](alerts-metric.md).

Här följer de rekommenderade inställningarna för aviseringen för att komma igång:

- Mål: Välj din Log Analytics-resurs
- Villkor: 
   - Signalnamn: anpassad loggsökning
   - Sökfråga: åtgärden | där detalj har ”se”
   - Baserat på: antal resultat
   - Villkor: Är större än
   - Tröskelvärde: 0
   - Period: 5 (minuter)
   - Frekvens: 5 (minuter)
- Namn på aviseringsregel: dagliga data nådd
- Allvarlighetsgrad: Varning (Sev 1)

När aviseringen har definierats och gränsen har nåtts kan en avisering har utlösts och utför svaret som definierats i åtgärdsgruppen. Det kan meddela ditt team via e-post och textmeddelanden eller automatisera åtgärder med webhooks, Automation-runbooks eller [integrera med en extern ITSM-lösning](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat

Högre användning orsakas av en eller båda:
- Fler noder än förväntat skicka data till Log Analytics-arbetsyta
- Fler data än vad som förväntas skickas till Log Analytics arbets yta

## <a name="understanding-nodes-sending-data"></a>Förstå noder som skickar data

Om du vill förstå antalet datorer som rapporterar pulsslag varje dag under den senaste månaden använder du

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Om du vill hämta en lista över datorer som kommer att faktureras som noder om arbets ytan är i pris nivån bakåtkompatibelt per nod, letar du efter noder som skickar **fakturerings data typer** (vissa data typer är kostnads fria). Det gör du genom att använda `_IsBillable` [egenskap](log-standard-properties.md#_isbillable) och använda fältet längst till vänster i det fullständigt kvalificerade domän namnet. Detta returnerar listan över datorer med fakturerade data:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Antalet fakturerbara noder som visas kan beräknas som: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName)
```

> [!NOTE]
> Använd dessa `union withsource = tt *` frågor sparsamt eftersom det är dyrt att köra genomsökningar över data typer. Den här frågan ersätter det gamla sättet att fråga information per dator med data typen användning.  

En mer exakt beräkning av vad som faktiskt faktureras är att hämta antalet datorer per timme som skickar fakturerings data typer. (För arbets ytor på den äldre pris nivån per nod beräknar Log Analytics antalet noder som måste faktureras per timme.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Förstå inmatad data volym

På den **användning och uppskattade kostnader** kan den *datainmatning per lösning* diagrammet visar den totala mängden data som skickas och hur mycket som skickas av varje lösning. På så sätt kan du fastställa trender, till exempel om den övergripande dataanvändning (eller användning av en viss lösning) ökar, förblir oförändrad eller minskar. Frågan används för att generera detta är

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

Observera att i satsen ”där IsBillable = true” filtrerar ut datatyper från vissa lösningar som är gratis inmatning. 

Du kan öka detaljnivån ytterligare till Se datatrender för specifika datatyper, till exempel om du vill undersöka data på grund av IIS-loggar:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Data volym per dator

Om du vill se **storleken** på fakturerbara händelser per dator använder du [egenskapen](log-standard-properties.md#_billedsize)`_BilledSize` som anger storleken i byte:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

[Egenskapen](log-standard-properties.md#_isbillable) `_IsBillable` anger om inmatade data kommer att debiteras.

Om du vill se antalet inmatade **fakturerbara** händelser per dator använder du 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

Om du vill se antalet för fakturerbar datatyper skickar data till en specifik dator Använd:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Data volym per Azure-resurs, resurs grupp eller prenumeration

För data från noder som finns i Azure kan du få **storleken** på fakturerbara händelser __per dator__, använda _ResourceId [egenskap](log-standard-properties.md#_resourceid), som ger den fullständiga sökvägen till resursen:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

För data från noder som finns i Azure kan du få **storleken** på fakturerbara händelser __per Azure-prenumeration__, parsa `_ResourceId`-egenskapen som:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Om du ändrar `subscriptionId` till `resourceGroup` visas resurs gruppen fakturerbart data volym av Azure. 


> [!NOTE]
> Några av fälten i användnings data typen, men fortfarande i schemat, är inaktuella och de kommer inte längre att fyllas i. Det här är **datorn** samt relaterade fält till inmatning (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** och **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Fråga efter vanliga data typer

Om du vill gå på djupet datakällan för en viss typ, är här några användbara exempelfrågor:

+ **Security**-lösningen
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Log Management**-lösningen
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf**-datatypen
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event**-datatypen
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog**-datatypen
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datatypen **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tips för att minska datavolym

Några förslag för att minska mängden insamlade loggar är:

| Källan för hög datavolym | Hur du minskar datavolym |
| -------------------------- | ------------------------- |
| Säkerhetshändelser            | Välj [vanliga eller minimala säkerhetshändelser](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Ändra principen för säkerhetsgranskning för att endast samla in händelser som behövs. Du kan särskilt se över behovet att samla in händelser för att <br> - [granska filtreringplattform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [granska register](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [granska filsystem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [granska kernelobjekt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [granska hantering av manipulering](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Granska flyttbara lagringsmedia |
| Prestandaräknare       | Ändra [prestandaräknarens konfiguration](data-sources-performance-counters.md) för att: <br> - Minska insamlingsfrekvensen <br> - Minska antalet prestandaräknare |
| Händelseloggar                 | Ändra [händelseloggens konfiguration](data-sources-windows-events.md) för att: <br> - Minska antalet händelseloggar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informationsnivå* |
| Syslog                     | Ändra [systemloggkonfigurationen](data-sources-syslog.md) för att: <br> - Minska antalet anläggningar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informations-* eller *Felsökningsnivå* |
| AzureDiagnostics           | Ändra logginsamlingen för resurser för att: <br> – Minska antalet resursloggar som skickas till Log Analytics <br> – Endast samla in nödvändiga loggar |
| Lösningsdata från datorer som inte behöver lösningen | Använd [lösningsriktning](../insights/solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer. |

### <a name="getting-security-and-automation-node-counts"></a>Antalet säkerhets-och automation-noder hämtas

Om du är på ”Per nod (OMS)” prisnivå så debiteras du utifrån antal noder och lösningar som du använder, hur många insikter och analys noder som du faktureras kommer att visas i tabellen på den **användning och uppskattade kostnader**sidan.  

Du kan använda frågan om du vill se antalet separata noder som säkerhet:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Använd fråga om du vill se antalet distinkta Automation-noder:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Skapa en avisering när data insamlingen är hög

I det här avsnittet beskrivs hur du skapar en avisering om:
- Datavolymen överskrider en angiven mängd.
- Datavolymen förväntas överskrida en angiven mängd.

Azure-aviseringar har stöd för [loggaviseringar](alerts-unified-log.md) som använder sökfrågor. 

Följande fråga har ett resultat när det finns fler än 100 GB data som har samlats in under de senaste 24 timmarna:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

Följande fråga använder en enkel formel för att förutsäga när mer än 100 GB data skickas under en dag: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Ändra 100 i frågan till antalet GB som du vill ange som gräns för att skicka en datavolymavisering.

Använd stegen som beskrivs i [skapa en ny loggavisering](alerts-metric.md) om du vill meddelas när datainsamlingen är högre än förväntat.

När du skapar aviseringen för den första frågan--när det finns fler än 100 GB data på 24 timmar, ange:  

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signalnamn** välj **Anpassad loggsökning**
   - **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tidsperiod** på *1440* minuter och **Aviseringsfrekvens** var *60*:e minut eftersom användningsdata bara uppdateras en gång i timmen.
- **Definiera aviseringsinformation** ange följande:
   - **Namnet** till *Datavolym är större än 100 GB på 24 timmar*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

När du skapar aviseringen för den andra frågan--när mer än 100 GB data på 24 timmar förväntas, ange:

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signalnamn** välj **Anpassad loggsökning**
   - **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tidsperiod** på *180* minuter och **Aviseringsfrekvens** var *60*:e minut eftersom användningsdata bara uppdateras en gång i timmen.
- **Definiera aviseringsinformation** ange följande:
   - **Namnet** till *Datavolym förväntas vara större än 100 GB på 24 timmar*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

När du får en avisering kan du använda stegen i följande avsnitt för att felsöka varför användningen är högre än förväntat.

## <a name="data-transfer-charges-using-log-analytics"></a>Avgifter för data överföring med Log Analytics

Att skicka data till Log Analytics kan medföra avgifter för data bandbredd. Som det beskrivs i [prissättnings sidan för Azure bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)är data överföringen mellan Azure-tjänster som finns i två regioner debiterad som utgående data överföring enligt normal taxa. Inkommande data överföring är kostnads fri. Den här avgiften är dock väldigt liten (några%) jämfört med kostnaderna för Log Analytics data inmatning. Därför bör du kontrol lera kostnaderna för Log Analytics behöver fokusera på din inmatade data volym och vi har vägledning som hjälper dig att förstå den [här](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Felsöka varför Log Analytics inte längre samla in data

Om du befinner dig på den äldre kostnads fria pris nivån och har skickat mer än 500 MB data under en dag stannar data insamlingen under resten av dagen. Når den dagliga gränsen är en vanlig orsak som Log Analytics slutar att samla in data eller data verkar sakna.  Log Analytics skapar en händelse av typen igen när datainsamlingen startar och stoppar. Kör följande fråga i sökningen för att kontrol lera om du når den dagliga gränsen och data som saknas: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

När data insamlingen stoppas, är OperationStatus **Varning**. När data insamlingen startar har OperationStatus **slutförts**. I följande tabell beskrivs skäl som stoppar insamling av data och en rekommenderad åtgärd för att återuppta insamling av data:  

|Stoppar orsak samling| Lösning| 
|-----------------------|---------|
|Den dagliga gränsen för den äldre kostnads fria pris nivån har uppnåtts |Vänta tills nästa dag för samlingen att starta om automatiskt eller ändra till en betald prisnivå.|
|Arbets ytans dagliga tak har uppnåtts|Vänta tills insamlingen startar om automatiskt eller öka den dagliga data volym gränsen som beskrivs i hantera den maximala dagliga data volymen. Den dagliga återställnings tiden visas på sidan **data volym hantering** . |
|Azure-prenumerationen är i ett pausat tillstånd på grund av:<br> Kostnadsfri utvärderingsversion avslutades<br> Azure-pass har upphört att gälla<br> Varje månad utgiftsgränsen har nåtts (till exempel på en MSDN eller Visual Studio-prenumeration)|Konvertera till en betald prenumeration<br> Ta bort gränsen, eller vänta tills begränsningen återställs|

Om du vill få ett meddelande när data insamlingen stoppas, använder du stegen som beskrivs i *skapa daglig data Kap* -avisering för att bli informerad när data insamlingen stoppa Använd stegen som beskrivs i [skapa en åtgärds grupp](action-groups.md) för att konfigurera en e-post, webhook eller Runbook-åtgärd för varnings regeln. 

## <a name="limits-summary"></a>Sammanfattning av gränser

Det finns ytterligare Log Analytics gränser, varav vissa är beroende av Log Analytics pris nivå. Dessa dokumenteras [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Nästa steg

- Se [loggs ökningar i Azure Monitor loggar](../log-query/log-query-overview.md) för att lära dig hur du använder Sök språket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
- Använd stegen som beskrivs i [Skapa en ny loggavisering](alerts-metric.md) om du vill meddelas när ett sökvillkor har uppfyllts.
- Använd [lösningsriktning](../insights/solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer.
- Om du vill konfigurera en princip för en effektiv händelse insamling granskar [Azure Security Center filtrerings princip](../../security-center/security-center-enable-data-collection.md).
- Ändra [prestandaräknarens konfiguration](data-sources-performance-counters.md).
- Om du vill ändra inställningarna för insamling av händelser kan du läsa [händelseloggens konfiguration](data-sources-windows-events.md).
- Om du vill ändra inställningarna för insamling av systemlogg kan du läsa [ systemloggens konfiguration](data-sources-syslog.md).
