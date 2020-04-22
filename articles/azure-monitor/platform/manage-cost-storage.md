---
title: Hantera användning och kostnader för Azure Monitor Logs | Microsoft-dokument
description: Lär dig hur du ändrar prisplanen och hanterar datavolym och lagringsprincip för din Log Analytics-arbetsyta i Azure Monitor.
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
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686564"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Hantera användning och kostnader med Azure Monitor Logs

> [!NOTE]
> I den här artikeln beskrivs hur du förstår och styr dina kostnader för Azure Monitor Logs. En relaterad artikel, [Övervakning av användning och uppskattade kostnader](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) beskriver hur du visar användning och uppskattade kostnader över flera Azure-övervakningsfunktioner för olika prismodeller. Alla priser och kostnader som visas i den här artikeln är till exempel endast avsedda som exempel. 

Azure Monitor Logs är utformad för att skala och stödja insamling, indexering och lagring av stora mängder data per dag från valfri källa i ditt företag eller distribueras i Azure.  Även om detta kan vara en primär drivkraft för din organisation, är kostnadseffektivitet i slutändan den underliggande drivrutinen. Därför är det viktigt att förstå att kostnaden för en Log Analytics-arbetsyta inte bara baseras på mängden data som samlas in, att den också är beroende av den valda planen och hur länge du väljer att lagra data som genereras från dina anslutna källor.  

I den här artikeln granskar vi hur du proaktivt kan övervaka inmatad datavolym och lagringstillväxt och definiera gränser för att styra dessa tillhörande kostnader. 

## <a name="pricing-model"></a>Prismodell

Standardprissättningen för Log Analytics är en **Pay-As-You-Go-modell** som baseras på datavolym som intas och eventuellt för längre datalagring. Datavolymen mäts som storleken på de data som ska lagras. Varje Log Analytics-arbetsyta debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration. Mängden datainmatning kan vara betydande beroende på följande faktorer: 

  - Antal aktiverade hanteringslösningar och deras konfiguration (t.ex. 
  - Antal datorer som övervakas
  - Typ av data som samlas in från varje övervakad virtuell dator 
  
Förutom pay-as-you-go-modellen har Log Analytics **kapacitetsreserveringsnivåer** som gör att du kan spara så mycket som 25 % jämfört med priset för betala och spara med dig. Med prissättningen för kapacitetsreservation kan du köpa en bokning som börjar på 100 GB/dag. All användning över bokningsnivån kommer att debiteras till priset betala per användning. Kapacitetsreserveringsnivåerna har en åtagandeperiod på 31 dagar. Under åtagandeperioden kan du ändra till en högre nivå Kapacitetsbokningsnivå (som startar om åtagandeperioden på 31 dagar), men du kan inte flytta tillbaka till Användningsbaserad betalning eller till en nivå med lägre kapacitetsreservering förrän åtagandeperioden är klar. Fakturering för kapacitetsbokningsnivåerna sker dagligen. [Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för inloggningsanalysbaserad användningsbaserad betalning och kapacitetsbokning. 

I alla prisnivåer beräknas datavolymen från en strängrepresentation av data när den är förberedd för att lagras. Flera egenskaper som är [gemensamma för alla datatyper](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) ingår `_ResourceId`inte `_ItemId` `_IsBillable` i `_BilledSize`beräkningen av händelsestorleken, inklusive , och .

Observera också att vissa lösningar, till exempel [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) och Azure [Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)har en egen prismodell. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Uppskatta kostnaderna för att hantera din miljö 

Om du ännu inte använder Azure Monitor Logs kan du använda [Azure Monitor-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att uppskatta kostnaden för att använda Log Analytics. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor-panelen. Bläddra nedåt på sidan till Azure Monitor och välj Logganalys i listrutan Typ.  Här kan du ange antalet virtuella datorer och GB för data som du förväntar dig att samla in från varje virtuell dator. Vanligtvis intas 1 till 3 GB datamånad från en vanlig Virtuell Azure-dator. Om du redan utvärderar Azure Monitor Logs kan du använda din datastatistik från din egen miljö. Se nedan för hur du bestämmer [antalet övervakade virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) och mängden data som [arbetsytan intagar](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Förstå dina användnings- och uppskattningskostnader

Om du använder Azure Monitor Logs nu är det lätt att förstå vilka kostnaderna sannolikt baseras på de senaste användningsmönstren. Det gör du genom att använda **användning av logganalys och uppskattade kostnader** för att granska och analysera dataanvändning. Detta visar hur mycket data som samlas in av varje lösning, hur mycket data som lagras och en uppskattning av dina kostnader baserat på mängden data som intas och eventuell ytterligare lagring utöver det inkluderade beloppet.

![Användning och uppskattade kostnader](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Om du vill utforska dina data mer i detalj klickar du på ikonen längst upp till höger i något av diagrammen på sidan **Användning och uppskattade kostnader.** Nu kan du arbeta med den här frågan för att utforska mer information om din användning.  

![Vyn Loggar](media/manage-cost-storage/logs.png)

På sidan **Användning och uppskattade kostnader** kan du granska din datavolym för månaden. Detta inkluderar alla data som tas emot och lagras i logganalysarbetsytan.  Klicka på **Användningsinformation** högst upp på sidan om du vill visa användningsinstrumentpanelen med information om datavolymtrender efter källa, datorer och erbjudanden. Om du vill visa och ange ett dagligt tak eller ändra kvarhållningsperioden klickar du på **Datavolymhantering**.
 
Log Analytics-avgifter läggs till i din Azure-faktura. Du kan se information om din Azure-faktura under avsnittet Fakturering i Azure-portalen eller i [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visa användning av Logganalys på din Azure-faktura 

Azure tillhandahåller en hel del användbara funktioner i [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hub. Med funktionen Kostnadsanalys kan du till exempel visa dina utgifter för Azure-resurser. Genom att lägga till ett filter efter resurstyp (i microsoft.operationalinsights/workspace for Log Analytics) kan du spåra dina utgifter.

Mer förståelse för din användning kan vinnas genom [att ladda ner din användning från Azure-portalen](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). I det hämtade kalkylbladet kan du se användning per Azure-resurs (t.ex. log analytics-arbetsyta) per dag. I det här Excel-kalkylbladet kan användning från dina Log Analytics-arbetsytor hittas genom att först filtrera i kolumnen "Mätarkategori" för att visa "Insikter och analyser" (används av några av de äldre prisnivåerna) och "Log Analytics", och sedan lägga till ett filter i kolumnen "Instans-ID" som är "innehåller arbetsyta". Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "Enhet".  Mer information finns tillgänglig för att hjälpa dig att [förstå din Microsoft Azure-faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Ändra prisnivå

Om du vill ändra prisnivån för Log Analytics på arbetsytan 

1. I Azure-portalen, öppna **användning och uppskattade kostnader** från din arbetsyta där du ser en lista över var och en av de prisnivåer som är tillgängliga för den här arbetsytan.

2. Granska de uppskattade kostnaderna för var och en av prisnivåerna. Den här uppskattningen baseras på de senaste 31 dagarnas användning, så den här kostnadsuppskattningen är beroende av att de senaste 31 dagarna är representativa för din typiska användning. I exemplet nedan kan du se hur den här arbetsytan, baserat på datamönstren från de senaste 31 dagarna, skulle kosta mindre på nivån Betala per användning (#1) jämfört med 100 GB/dagars kapacitetsreservationsnivå (#2).  

    ![Prisnivåer](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. När du har granskat de uppskattade kostnaderna baserat på de senaste 31 dagarnas användning klickar du på **Välj**om du bestämmer dig för att ändra prisnivån.  

Du kan också [ange prisnivån via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) med parametern `sku` (`pricingTier` i Azure Resource Manager-mallen). 

## <a name="legacy-pricing-tiers"></a>Äldre prisnivåer

Prenumerationer som hade en log Analytics-arbetsyta eller Application Insights-resurs i den före den 2 april 2018, eller som är länkade till ett Enterprise-avtal som startade före den 1 februari 2019, kommer att fortsätta att ha åtkomst till att använda äldre prisnivåer: **Gratis**, **Fristående (Per GB)** och **Per Node (OMS)**.  Arbetsytor på den kostnadsfria prisnivån har dagligt datainmatning begränsat till 500 MB (förutom säkerhetsdatatyper som samlas in av Azure Security Center) och datalagringen är begränsad till 7 dagar. Den kostnadsfria prisnivån är endast avsedd för utvärderingsändamål. Arbetsytor på prisnivåerna Fristående eller per nod har användarkonfigurerbar kvarhållning från 30 till 730 dagar.

Prisnivånivån per nod per övervakad virtuell dator (nod) på en timmes granularitet. För varje övervakad nod tilldelas arbetsytan 500 MB data per dag som inte faktureras. Den här allokeringen aggregeras på arbetsytasnivå. Data som intas ovanför den sammanlagda dagliga dataallokeringen faktureras per GB som dataöverpris. Observera att på din faktura kommer tjänsten att vara **Insight och Analytics** för Log Analytics-användning om arbetsytan finns på prisnivån per nod. 

> [!TIP]
> Om arbetsytan har åtkomst till prisnivån **per nod,** men du undrar om det skulle kosta mindre på en pay-as-you-go-nivå, kan du [använda frågan nedan](#evaluating-the-legacy-per-node-pricing-tier) för att enkelt få en rekommendation. 

Arbetsytor som skapats före april 2016 kan också komma åt de ursprungliga **standard-** och **premiumprisnivåerna** som har fast datalagring på 30 respektive 365 dagar. Nya arbetsytor kan inte skapas på prisnivåerna **Standard** eller **Premium,** och om en arbetsyta flyttas från dessa nivåer kan den inte flyttas tillbaka. 

Mer information om begränsningar på prisnivå finns [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Om du vill använda de rättigheter som kommer från att köpa OMS E1 Suite, OMS E2 Suite eller OMS Add-On för System Center väljer du prisnivån Log Analytics *Per Node.*

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

I följande steg beskrivs hur du konfigurerar hur länge loggdata sparas på arbetsytan. Datalagring kan konfigureras från 30 till 730 dagar (2 år) för alla arbetsytor om de inte använder den äldre kostnadsfria prisnivån. 

### <a name="default-retention"></a>Kvarhållning för standard

Om du vill ange standardlagring för arbetsytan 
 
1. I Azure-portalen väljer du **Användning och uppskattade kostnader** från den vänstra rutan från arbetsytan.
2. På sidan **Användning och uppskattade kostnader** klickar du på **Datavolymhantering** högst upp på sidan.
3. Flytta skjutreglaget i fönstret för att öka eller minska antalet dagar och klicka sedan på **OK**.  Om du är på den *kostnadsfria* nivån kan du inte ändra datalagringsperioden och du måste uppgradera till den betalda nivån för att styra den här inställningen.

    ![Ändra lagringsinställning för arbetsytedata](media/manage-cost-storage/manage-cost-change-retention-01.png)

När kvarhållningen sänks finns det en respitperiod på flera dagar innan de äldsta data tas bort. 
    
Kvarhållningen kan också [ställas in via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) med parametern. `retentionInDays` Om du ställer in datalagringen till 30 dagar kan du dessutom `immediatePurgeDataOn30Days` utlösa en omedelbar rensning av äldre data med parametern, vilket kan vara användbart för efterlevnadsrelaterade scenarier. Den här funktionen visas endast via Azure Resource Manager. 

Två datatyper `Usage` - `AzureActivity` och - behålls i 90 dagar som standard, och det finns ingen avgift för den här 90 dagars kvarhållningen. Dessa datatyper är också fria från avgifter för datainmatning. 



### <a name="retention-by-data-type"></a>Lagring efter datatyp

Det är också möjligt att ange olika lagringsinställningar för enskilda datatyper från 30 till 730 dagar (förutom arbetsytor i den äldre kostnadsfria prisnivån). Varje datatyp är en underresurs till arbetsytan. Tabellen SecurityEvent kan till exempel adresseras i [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Observera att datatypen (tabellen) är skiftlägeskänslig.  Om du vill hämta de aktuella lagringsinställningarna per datatyp för en viss datatyp (i det här exemplet SecurityEvent) använder du:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Om du vill hämta de aktuella inställningarna för lagring av datatyp för alla datatyper på arbetsytan utelämnar du bara den specifika datatypen, till exempel:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Om du vill ställa in lagringen av en viss datatyp (i det här exemplet SecurityEvent) till 730 dagar

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Giltiga värden `retentionInDays` för är från 30 till 730.

Det `Usage` `AzureActivity` går inte att ange datatyperna och datatyperna med anpassad kvarhållning. De tar på sig det maximala av standardlagringen av arbetsytan eller 90 dagar. 

Ett bra verktyg för att ansluta direkt till Azure Resource Manager för att ange lagring efter datatyp är OSS-verktyget [ARMclient](https://github.com/projectkudu/ARMClient).  Läs mer om ARMclient från artiklar av [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) och [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Här är ett exempel med ARMClient, ställa in SecurityEvent-data till en 730 dagars kvarhållning:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Att ange lagring på enskilda datatyper kan användas för att minska kostnaderna för datalagring.  För data som samlas in från och med oktober 2019 (när den här funktionen släpptes) kan det minska lagringen för vissa datatyper minska lagringskostnaden över tid.  För data som samlats in tidigare påverkar inställningen en lägre kvarhållning för en enskild typ inte dina lagringskostnader.  

## <a name="manage-your-maximum-daily-data-volume"></a>Hantera din maximala dagliga datavolym

Du kan konfigurera ett dagligt tak och begränsa det dagliga inmatningen för din arbetsyta, men var försiktig eftersom ditt mål inte ska vara att nå den dagliga gränsen.  Annars förlorar du data för resten av dagen, vilket kan påverka andra Azure-tjänster och lösningar vars funktioner kan bero på att aktuella data är tillgängliga på arbetsytan.  Som ett resultat, din förmåga att observera och ta emot varningar när hälsotillståndet för resurser som stöder IT-tjänster påverkas.  Det dagliga taket är avsett att användas som ett sätt att hantera den oväntade ökningen av datavolymen från dina hanterade resurser och hålla dig inom din gräns, eller när du vill begränsa oplanerade avgifter för din arbetsyta.  

Strax efter att den dagliga gränsen har nåtts stoppas insamlingen av fakturerbara datatyper för resten av dagen. (Latens inneboende i tillämpningen av det dagliga taket kan innebära att locket inte tillämpas som exakt den angivna dagliga taknivån.) En varningsbanderoll visas överst på sidan för den valda Log Analytics-arbetsytan och en åtgärdshändelse skickas till *tabellen Åtgärd* under kategorin **LogManagement.** Datainsamlingen återupptas efter den återställningstid som definieras under *Daglig gräns kommer att ställas in på*. Vi rekommenderar att du definierar en varningsregel baserat på den här åtgärdshändelsen, konfigurerad för att meddela när den dagliga datagränsen har uppnåtts. 

> [!WARNING]
> Det dagliga taket stoppar inte insamlingen av data från Azure Security Center, förutom arbetsytor där Azure Security Center installerades före den 19 juni 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilken daglig datagräns som ska definieras

Granska [Log Analytics-användning och uppskattade kostnader](usage-estimated-costs.md) för att förstå datainmatningstrenden och vad som är det dagliga volymtaket att definiera. Det bör övervägas med försiktighet, eftersom du inte kommer att kunna övervaka dina resurser efter att gränsen har nåtts. 

### <a name="set-the-daily-cap"></a>Ställ in den dagliga tak

I följande steg beskrivs hur du konfigurerar en gräns för att hantera mängden data som Log Analytics-arbetsytan kommer att inta per dag.  

1. Välj **Användning och uppskattade kostnader** i det vänstra fönstret på arbetsytan.
2. Klicka på **Datavolymhantering** högst upp på sidan **Användning och uppskattade kostnader** för den valda arbetsytan. 
3. Dagligt tak är **OFF** som standard klicka på **PÅ** för att aktivera den och sedan ställa in datavolymgränsen i GB/dag.

    ![Konfigurera datagräns för Logganalys](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Varning när Daily Cap nått

Vi presenterar en visuell stackern i Azure-portalen när tröskelvärdet för datagränsen är uppfyllt, men det här beteendet stämmer inte nödvändigtvis överens med hur du hanterar operativa problem som kräver omedelbar uppmärksamhet.  Om du vill få ett aviseringsmeddelande kan du skapa en ny aviseringsregel i Azure Monitor.  Mer information finns i [hur du skapar, visar och hanterar aviseringar](alerts-metric.md).

För att komma igång, här är de rekommenderade inställningarna för aviseringen:

- Mål: Välj din Log Analytics-resurs
- Kriterier: 
   - Signalnamn: Anpassad loggsökning
   - Sökfråga: Åtgärd | där Detail har "OverQuota"
   - Baserat på: Antal resultat
   - Skick: Större än
   - Tröskelvärde: 0
   - Period: 5 (minuter)
   - Frekvens: 5 (minuter)
- Namn på varningsregel: Den dagliga datagränsen har nåtts
- Svårighetsgrad: Varning (Sev 1)

När aviseringen har definierats och gränsen har uppnåtts utlöses en avisering och utför svaret som definierats i åtgärdsgruppen. Det kan meddela ditt team via e-post och textmeddelanden, eller automatisera åtgärder med webhooks, Automation runbooks eller [integrera med en extern ITSM-lösning](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat

Högre användning orsakas av en eller båda:
- Fler noder än förväntat skicka data till Log Analytics arbetsyta
- Fler data än förväntat skickas till Log Analytics arbetsyta (kanske på grund av att börja använda en ny lösning eller en konfigurationsändring till en befintlig lösning)

## <a name="understanding-nodes-sending-data"></a>Förstå noder som skickar data

Om du vill förstå hur många noder som rapporterar pulsslag från agenten varje dag under den senaste månaden använder du

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Få ett antal noder som skickar data under de senaste 24 timmarna använder frågan: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

För att få en lista över noder som skickar data (och mängden data som skickas av varje) kan följande fråga användas:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Använd `union withsource = tt *` dessa frågor sparsamt eftersom sökningar över datatyper är dyra att köra. Den här frågan ersätter det gamla sättet att fråga information per dator med datatypen Användning.  

## <a name="understanding-ingested-data-volume"></a>Förstå intas datavolym

På sidan **Användning och uppskattade kostnader** visar *datainmatningen per lösningsdiagram* den totala mängden data som skickas och hur mycket som skickas av varje lösning. På så sätt kan du bestämma trender, till exempel om den totala dataanvändningen (eller användningen av en viss lösning) växer, förblir stabil eller minskar. 

### <a name="data-volume-for-specific-events"></a>Datavolym för specifika händelser

Om du vill titta på storleken på intvalda data för en viss uppsättning `Event`händelser kan du fråga efter den specifika tabellen (i det här exemplet) och sedan begränsa frågan till händelser av intresse (i det här exemplet händelse-ID 5145 eller 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Observera att `where IsBillable = true` satsen filtrerar bort datatyper från vissa lösningar för vilka det inte finns någon inmatningsavgift för. 

### <a name="data-volume-by-solution"></a>Datavolym per lösning

Frågan som används för att visa den fakturerbara datavolymen efter lösning under den senaste månaden (exklusive den sista deldagen) är:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Satsen `TimeGenerated` med är bara för att säkerställa att frågeupplevelsen i Azure-portalen ser tillbaka bortom standard24 timmar. När du använder datatypen Användning `StartTime` och `EndTime` representerar de tidssegment som resultaten presenteras för. 

### <a name="data-volume-by-type"></a>Datavolym efter typ

Du kan öka detaljnivån ytterligare om du vill se datatrender för datatyp:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Eller om du vill visa en tabell efter lösning och typ för den senaste månaden,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Datavolym per dator

Datatypen `Usage` innehåller inte information på slutförarnivå. Om du vill se **storleken på** intvalda data per dator använder du `_BilledSize` [egenskapen](log-standard-properties.md#_billedsize), som anger storleken i byte:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Egenskapen](log-standard-properties.md#_isbillable) anger om de intjesterade data kommer att medföra avgifter.

Om du vill se **antalet** fakturerbara händelser som intas per dator använder du 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Datavolym efter Azure-resurs, resursgrupp eller prenumeration

För data från noder som finns i Azure kan du få **storleken på** intvalda data __per dator__, använd [egenskapen](log-standard-properties.md#_resourceid)_ResourceId , som ger den fullständiga sökvägen till resursen:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

För data från noder som finns i Azure kan du få **storleken på** intövda data __per Azure-prenumeration__, tolka egenskapen `_ResourceId` som:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Om `subscriptionId` `resourceGroup` du ändrar till visas den fakturerbara intjesterade datavolymen för Azure-resursgruppen. 

> [!WARNING]
> Vissa fält i datatypen Användning, medan de fortfarande är i schemat, har inaktuellt och kommer att fyllas i deras värden inte längre. Dessa är **dator-** samt fält relaterade till intag (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla,** **BatchesCapped** och **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Fråga efter vanliga datatyper

Om du vill gräva djupare i datakällan för en viss datatyp följer här några användbara exempelfrågor:

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

### <a name="tips-for-reducing-data-volume"></a>Tips för att minska datavolymen

Några förslag för att minska volymen av insamlade loggar inkluderar:

| Källan för hög datavolym | Hur du minskar datavolym |
| -------------------------- | ------------------------- |
| Säkerhetshändelser            | Välj [vanliga eller minimala säkerhetshändelser](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Ändra principen för säkerhetsgranskning för att endast samla in händelser som behövs. Du kan särskilt se över behovet att samla in händelser för att <br> - [granska filtreringplattform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [granska register](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [granska filsystem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [granska kernelobjekt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [granska hantering av manipulering](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - Granskning av flyttbar lagring |
| Prestandaräknare       | Ändra [prestandaräknarens konfiguration](data-sources-performance-counters.md) för att: <br> - Minska insamlingsfrekvensen <br> - Minska antalet prestandaräknare |
| Händelseloggar                 | Ändra [händelseloggens konfiguration](data-sources-windows-events.md) för att: <br> - Minska antalet händelseloggar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informationsnivå* |
| Syslog                     | Ändra [systemloggkonfigurationen](data-sources-syslog.md) för att: <br> - Minska antalet anläggningar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informations-* eller *Felsökningsnivå* |
| AzureDiagnostics           | Ändra logginsamlingen för resurser för att: <br> – Minska antalet resursloggar som skickas till Log Analytics <br> – Endast samla in nödvändiga loggar |
| Lösningsdata från datorer som inte behöver lösningen | Använd [lösningsinriktning](../insights/solution-targeting.md) för att samla in data från endast obligatoriska grupper av datorer. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Få noder som faktureras i prisnivån per nod

Om du vill hämta en lista över datorer som faktureras som noder om arbetsytan finns i den äldre prisnivån per nod letar du efter noder som skickar **fakturerade datatyper** (vissa datatyper är kostnadsfria). Det gör du `_IsBillable` genom att använda [egenskapen](log-standard-properties.md#_isbillable) och använda fältet längst till vänster i det fullständigt kvalificerade domännamnet. Detta returnerar antalet datorer med fakturerade data per timme (vilket är den granularitet vid vilken noder räknas och faktureras):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Antalet Ã-Ã-Ã-Ã-Ã-Ã-Ã-

Om du har prisnivån Per nod (OMS) debiteras du baserat på antalet noder och lösningar som du använder, antalet insights- och Analytics-noder som du faktureras för visas i tabellen på sidan **Användning och uppskattad kostnad.**  

Om du vill visa antalet olika säkerhetsnoder kan du använda frågan:

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

Om du vill se antalet olika Automation-noder använder du frågan:

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Utvärdera den äldre prisnivån per nod

Beslutet om huruvida arbetsytor med åtkomst till den äldre per **nodprisnivån** har det bättre på den nivån eller på en aktuell **nivå för användningsbaserad betalning** eller **kapacitetsreservation** är ofta svårt för kunder att bedöma.  Detta innebär att förstå avvägningen mellan den fasta kostnaden per övervakad nod i prisnivån per nod och dess inkluderade dataallokering av 500 MB/nod/dag och kostnaden för att bara betala för intas data i nivån Betala per gb (Per GB). 

För att underlätta den här utvärderingen kan följande fråga användas för att göra en rekommendation för den optimala prisnivån baserat på en arbetsytas användningsmönster.  Den här frågan tittar på de övervakade noderna och data som intas i en arbetsyta under de senaste 7 dagarna och utvärderar för varje dag vilken prisnivå som skulle ha varit optimal. Om du vill använda frågan måste du ange om arbetsytan `workspaceHasSecurityCenter` `true` ska `false`använda Azure Security Center genom att ange eller och sedan (eventuellt) uppdatera de priser per nod och per GB som din organizaiton tar emot. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Den här frågan är inte en exakt replikering av hur användningen beräknas, men fungerar för att tillhandahålla prisnivårekommendationer i de flesta fall.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Skapa en avisering när datainsamlingen är hög

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

## <a name="data-transfer-charges-using-log-analytics"></a>Avgifter för dataöverföring med Hjälp av Log Analytics

Att skicka data till Log Analytics kan medföra avgifter för databandbredd. Som beskrivs i [prissidan för Azure Bandwidth](https://azure.microsoft.com/pricing/details/bandwidth/), dataöverföring mellan Azure-tjänster som finns i två regioner som debiteras som utgående dataöverföring med normal hastighet. Inkommande dataöverföring är gratis. Denna avgift är dock mycket liten (få %) jämfört med kostnaderna för Log Analytics-datainmatning. Därför måste du kontrollera kostnaderna för Log Analytics fokusera på din intjesterade datavolym, och vi har vägledning för att förstå det [här](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Felsöka varför Log Analytics inte längre samlar in data

Om du använder den äldre kostnadsfria prisnivån och har skickat mer än 500 MB data på en dag stoppas datainsamlingen resten av dagen. Att nå den dagliga gränsen är en vanlig orsak till att Log Analytics slutar samla in data eller att data verkar saknas.  Log Analytics skapar en händelse av typ Operation när datainsamlingen startar och stoppas. Kör följande fråga i sökningen för att kontrollera om du når den dagliga gränsen och saknade data: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

När datainsamlingen stoppas är OperationStatus **Varning**. När datainsamlingen startar **lyckades**OperationStatus . I följande tabell beskrivs orsaker till att datainsamlingen stoppas och en föreslagen åtgärd för att återuppta datainsamlingen:  

|Orsakssamlingen stoppas| Lösning| 
|-----------------------|---------|
|Daglig gräns för äldre kostnadsfri prisnivå har nåtts |Vänta till följande dag innan samlingen automatiskt startar om eller ändras till en betald prisnivå.|
|Dagligt tak på din arbetsyta uppnåddes|Vänta tills insamlingen automatiskt startar om eller öka den dagliga datavolymgränsen som beskrivs i hantera den maximala dagliga datavolymen. Den dagliga återställningstiden för tak visas på sidan **Datavolymhantering.** |
|Azure-prenumerationen är i ett pausat tillstånd på grund av:<br> Fri rättegång avslutad<br> Azure-passet har upphört att gälla<br> Månatliga utgiftsgräns som uppnåtts (till exempel på en MSDN- eller Visual Studio-prenumeration)|Konvertera till en betald prenumeration<br> Ta bort gräns eller vänta tills gränsen har återställts|

Om du vill meddelas när datainsamlingen stoppas använder du stegen som beskrivs i *Skapa daglig datataksavisering* som ska meddelas när datainsamlingen stoppas. Använd stegen som beskrivs i [Skapa en åtgärdsgrupp](action-groups.md) för att konfigurera en e-post-, webbkrok- eller runbook-åtgärd för varningsregeln. 

## <a name="limits-summary"></a>Sammanfattning av gränser

Det finns några ytterligare log analytics-begränsningar, varav vissa är beroende av log analytics-prisnivån. Dessa dokumenteras [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Nästa steg

- Se [Logga sökningar i Azure Monitor Loggar](../log-query/log-query-overview.md) för att lära dig hur du använder sökspråket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
- Använd stegen som beskrivs i [Skapa en ny loggavisering](alerts-metric.md) om du vill meddelas när ett sökvillkor har uppfyllts.
- Använd [lösningsinriktning](../insights/solution-targeting.md) för att samla in data från endast obligatoriska grupper av datorer.
- Om du vill konfigurera en effektiv händelseinsamlingsprincip läser du [azure security center-filtreringsprincipen](../../security-center/security-center-enable-data-collection.md).
- Ändra [prestandaräknarens konfiguration](data-sources-performance-counters.md).
- Om du vill ändra inställningarna för händelsesamlingen granskar du [konfigurationen av händelseloggen](data-sources-windows-events.md).
- Om du vill ändra inställningarna för syslog-samlingen granskar du [syslog-konfigurationen](data-sources-syslog.md).