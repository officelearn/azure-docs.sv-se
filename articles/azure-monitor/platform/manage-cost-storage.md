---
title: Hantera användning och kostnader för Azure Log Analytics | Microsoft Docs
description: Lär dig hur du ändrar prisplanen och hanterar data volym och kvarhållning för Log Analytics-arbetsytan i Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 290f0426befa322b20882682bf4555f80af016d1
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660399"
---
# <a name="manage-usage-and-costs-for-log-analytics"></a>Hantera användning och kostnader för Log Analytics

> [!NOTE]
> Den här artikeln beskriver hur du kan kontrollera dina kostnader i Log Analytics genom att ange kvarhållningsperioden för data.  Finns i följande artiklar för relaterad information.
> - [Analysera dataanvändning i Log Analytics](manage-cost-storage.md) beskriver hur du analyserar och Avisera om din dataanvändning.
> - [Övervaka användning och uppskattade kostnader](usage-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

Log Analytics har utformats för skalning och stöd för insamling av, indexering och lagra stora mängder data per dag från vilken källa som helst i ditt företag eller distribueras i Azure.  Detta kan vara en primära drivande faktorn för din organisation, är kostnadseffektivitet i slutändan underliggande drivrutinen. Det är också beroende av den valda planen därför det är viktigt att förstå att kostnaden för en Log Analytics-arbetsytan inte är bara baserat på mängden data som samlas in, och hur länge du har valt att lagra data som genereras från dina anslutna källor.  

I den här artikeln granskar vi hur du proaktivt övervakar tillväxt för volymen och lagring av data, och definiera gränser för att kontrollera de associerade kostnaderna. 

Kostnaden för data kan vara betydande beroende på följande faktorer: 

- Mängden data som genereras och samlas in till arbetsytan 
    - Antal hanteringslösningarna aktiverat
    - Antalet system som övervakas
    - Typ av data som samlas in från varje övervakad resurs 
- Hur lång tid som du vill behålla dina data 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Förstå din arbetsyta användning och uppskattade kostnader
Log Analytics gör det lätt att förstå vad kostnaderna förmodligen baseras på de senaste användningsmönster.  Gör detta genom att använda **Log Analytics-användning och uppskattade kostnader** att granska och analysera dataanvändning. Visar hur mycket data som samlas in av varje lösning, hur mycket data bevaras och en uppskattning av dina kostnader utifrån mängden data som samlas in och alla kvarhållning utöver mängden som ingår.

![Användning och uppskattade kostnader](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Om du vill utforska dina data i mer detalj klickar du på ikonen längst upp höger på något av diagrammen i den **användning och uppskattade kostnader** sidan. Nu kan du arbeta med den här frågan att utforska mer information om din användning.  

![Visa loggar](media/manage-cost-storage/logs.png)

Från den **användning och uppskattade kostnader** sidan som du kan granska din datavolym för månaden. Detta omfattar alla data tas emot och bevaras i Log Analytics-arbetsytan.  Klicka på **användningsinformation** högst upp på sidan för att visa instrumentpanelen för användning med information på datatrender volym av källa, datorer och erbjudande. Visa och ange en daglig högsta gräns eller ändra kvarhållningsperioden klickar du på **Datavolymhantering**.
 
Log Analytics avgifter läggs till i Azure-fakturan. Du kan se information om din Azure fakturerar under avsnittet fakturering i Azure Portal eller i den [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Dagligt tak
Du kan konfigurera en daglig högsta gräns och begränsa den dagliga datainmatningen för arbetsytan, men var försiktig eftersom målet inte får vara att träffa den dagliga gränsen.  Annars kan förlora du data under resten av den dagen, vilket kan påverka andra Azure-tjänster och lösningar som vars funktioner kan vara beroende uppdaterad information är tillgänglig i arbetsytan.  Därför kan aviseringar din förmåga att Observera och ta emot när hälsovillkoren av resurser som stödjer IT-tjänster som påverkas.  Den dagliga gränsen är avsedd att användas som ett sätt att hantera den oväntat ökningen av datavolymen från dina hanterade resurser och hålla dig inom dina gränser, eller när du vill att begränsa oplanerade avgifter för arbetsytan.  

När den dagliga gränsen har uppnåtts, stoppar insamlingen av fakturerbara datatyper för resten av dagen. En varning banderoll överst på sidan för den valda Log Analytics-arbetsytan och en åtgärd händelse skickas till den *åtgärden* tabellen **LogManagement** kategori. Insamling av data återupptar när återställningstiden definierats *dagliga gränsen ställs in på*. Vi rekommenderar att definiera en aviseringsregel baserat på den här åtgärden-händelser som konfigurerats för att meddela när den dagliga data gränsen har uppnåtts. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilka dagliga datagräns definiera 
Granska [Log Analytics-användning och uppskattade kostnader](usage-estimated-costs.md) att förstå till trenden för inmatning av data och vad är den dagliga Volymgränsen definiera. Det bör ses med försiktighet, eftersom du inte längre att övervaka dina resurser när gränsen har nåtts. 

### <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximal daglig datavolym 
Följande steg beskriver hur du konfigurerar en gräns för att hantera mängden data som Log Analytics att mata in per dag.  

1. Välj **Användning och beräknade kostnader** i det vänstra fönstret på arbetsytan.
2. På den **användning och uppskattade kostnader** för den valda arbetsytan och klicka på **Datavolymhantering** högst upp på sidan. 
3. Dagligt tak är **OFF** som standard – klickar du på **på** att aktivera den och ange sedan datavolymen i GB/dag.<br><br> ![Log Analytics konfigurera datagräns](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Avisera när dagliga gränsen har nåtts
Medan Vi presenterar en visuell ledtråd i Azure-portalen när tröskeln för ditt data gränsen är uppfyllt, justera det här beteendet inte nödvändigtvis som du hanterar operativa problem som kräver omedelbar uppmärksamhet.  För att få en avisering, kan du skapa en ny aviseringsregel i Azure Monitor.  Mer information finns i [hur du skapar, visa och hantera aviseringar](alerts-metric.md).      

Här följer de rekommenderade inställningarna för aviseringen för att komma igång:

* Mål: Välj din Log Analytics-resurs
* Villkor: 
   * Signalnamn: Anpassade loggsökning
   * Sökfråga: Åtgärden | där detalj har ”se”
   * Baserat på: Antal resultat
   * Villkor: Större än
   * Tröskelvärde: 0
   * Period: 5 (minuter)
   * Frekvens: 5 (minuter)
* Namn på aviseringsregel: Dagliga data nådd
* Allvarlighetsgrad: Varning (Sev 1)

När aviseringen har definierats och gränsen har nåtts kan en avisering har utlösts och utför svaret som definierats i åtgärdsgruppen. Det kan meddela ditt team via e-post och textmeddelanden eller automatisera åtgärder med webhooks, Automation-runbooks eller [integrera med en extern ITSM-lösning](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data 
Följande steg beskriver hur du konfigurerar hur länge log data bevaras av i din arbetsyta.
 
1. Välj **Användning och beräknade kostnader** i det vänstra fönstret på arbetsytan.
2. På sidan **Användning och beräknade kostnader** klickar du på **Datavolymhantering** högst upp på sidan.
5. Flytta skjutreglaget för att öka eller minska antalet dagar och klickar sedan på i fönstret **OK**.  Om du använder den *kostnadsfria* nivå, du kommer inte att kunna ändra kvarhållningsperioden för data och du måste uppgradera till betald nivå om du vill styra den här inställningen.<br><br> ![Ändra inställningen för kvarhållning av arbetsyta data](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Äldre prisnivåer

Kunder med ett Enterprise-avtal som signerats före den 1 juli 2018 eller som redan har skapat en Log Analytics-arbetsyta i en prenumeration kan du fortfarande har åtkomst till den *kostnadsfri* plan. Om din prenumeration inte är kopplat till en befintlig EA-registrering i *kostnadsfri* nivån är inte tillgänglig när du skapar en arbetsyta i en ny prenumeration efter 2 April 2018.  Data är begränsad till 7 dagars kvarhållning för den *kostnadsfri* nivå.  För äldre *fristående* eller *Per nod* nivåer, samt den aktuella 2018 enda prisnivån, data som samlas in är tillgängligt under de senaste 31 dagarna. Den *kostnadsfri* nivån har en gräns på 500 MB dagliga datainmatning och om du upptäcker att konsekvent överstiger de mängder som tillåts volym, kan du ändra din arbetsyta till en annan plan att samla in data utöver den här gränsen. 

> [!NOTE]
> Välj Log Analytics för att använda rättigheter som kommer från inköp av OMS E1 Suite, OMS E2 Suite eller OMS-tillägget för System Center, *Per nod* prisnivå.

## <a name="changing-pricing-tier"></a>Ändra prisnivå

Om Log Analytics-arbetsytan har tillgång till äldre prisnivåer för att ändra mellan äldre prisnivåer.

1. Välj en arbetsyta från fönstret Log Analytics-prenumerationer i Azure-portalen.

2. Från fönstret med arbetsytans under **Allmänt**väljer **prisnivå**.  

3. Under **prisnivå**, Välj en prisnivå och klickar sedan på **Välj**.  
    ![Valt prisplanen](media/manage-cost-storage/workspace-pricing-tier-info.png)

Om du vill flytta din arbetsyta till aktuell prisnivå kan du behöva [ändra prismodellen i Azure Monitor för övervakning av din prenumeration](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#moving-to-the-new-pricing-model) som kommer att ändras prisnivån för alla arbetsytor i prenumerationen.

> [!NOTE]
> Om arbetsytan är länkad till ett Automation-konto måste du ta bort alla **Automation and Control**-lösningar och ta bort länken för Automation-kontot innan du kan välja prisnivån *Fristående (per GB)*. I arbetsytebladet klickar du på **Lösningar** under **Allmänt** för att visa och ta bort lösningar. Du tar bort länken för Automation-kontot genom att klicka på namnet på Automation-kontot på bladet **Prisnivå**.


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Felsökning varför Log Analytics inte längre att samla in data
Om du är på den äldre kostnadsfria prisnivån och skicka fler än 500 MB data under en dag, stoppar insamling av data under resten av dagen. Når den dagliga gränsen är en vanlig orsak som Log Analytics slutar att samla in data eller data verkar sakna.  Log Analytics skapar en händelse av typen igen när datainsamlingen startar och stoppar. Kör följande fråga i sökningen för att kontrollera om du når den dagliga gränsen och saknade data: 

`Operation | where OperationCategory == 'Data Collection Status' `

När datainsamlingen slutar OperationStatus är en varning. När datainsamlingen startar är OperationStatus slutfört. I följande tabell beskrivs skäl som stoppar insamling av data och en rekommenderad åtgärd för att återuppta insamling av data:  

|Stoppar orsak samling| Lösning| 
|-----------------------|---------|
|Dagliga gränsen på äldre kostnadsfria prisnivån har nåtts |Vänta tills nästa dag för samlingen att starta om automatiskt eller ändra till en betald prisnivå.|
|Dagligt tak för din arbetsyta har uppnåtts|Vänta tills samling att starta om automatiskt eller öka den dagliga datavolymen som beskrivs i [hantera den maximala dagliga datavolymen](#manage-the-maximum-daily-volume). Dagligt tak återställningstiden är visas på den **Datavolymhantering** sidan. |
|Azure-prenumerationen är i ett pausat tillstånd på grund av:<br> Kostnadsfri utvärderingsversion avslutades<br> Azure-pass har upphört att gälla<br> Varje månad utgiftsgränsen har nåtts (till exempel på en MSDN eller Visual Studio-prenumeration)|Konvertera till en betald prenumeration<br> Ta bort gränsen, eller vänta tills begränsningen återställs|

Om du vill meddelas när datainsamlingen slutar, använder du stegen som beskrivs i *skapa dagliga data gräns* avisering du vill meddelas när datainsamlingen slutar och följ stegen använder stegen som beskrivs i Lägg till åtgärder för att Avisera regler konfigurera ett e-postmeddelande webhook eller runbook-åtgärden för regeln. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat
Högre användning orsakas av en eller båda:
- Mer data än förväntat skickas till Log Analytics
- Mer noder än förväntat som skickar data till Log Analytics

### <a name="data-volume"></a>Datavolym 
På den **användning och uppskattade kostnader** kan den *datainmatning per lösning* diagrammet visar den totala mängden data som skickas och hur mycket som skickas av varje lösning. På så sätt kan du fastställa trender, till exempel om den övergripande dataanvändning (eller användning av en viss lösning) ökar, förblir oförändrad eller minskar. Frågan används för att generera detta är

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Observera att i satsen ”där IsBillable = true” filtrerar ut datatyper från vissa lösningar som är gratis inmatning. 

Du kan öka detaljnivån ytterligare till Se datatrender för specifika datatyper, till exempel om du vill undersöka data på grund av IIS-loggar:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Noder som skickar data

För att förstå hur många datorer (noder) och rapporterar data varje dag under den senaste månaden, använda

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Hämta en lista över datorer som skickar **faktureras datatyper** (vissa datatyper är kostnadsfria), utnyttja den [_IsBillable](log-standard-properties.md#isbillable) egenskapen:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Använd de här `union withsource = tt *` frågar sparsamt eftersom sökningar över datatyper är dyrt att köra. 

Detta kan utökas för att returnera antalet datorer per timme som skickar faktureras datatyper:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

Se den **storlek** faktureringsbara händelser matas in per dator, använder den `_BilledSize` egenskapen som tillhandahåller storlek i byte:

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Den här frågan ersätter det gamla sättet att läsa detta med datatypen användning. 

Se den **antal** händelser matas in per dator, använda

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Om du vill se antalet faktureringsbara händelser matas in per dator 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Om du vill se antalet för fakturerbar datatyper skickar data till en specifik dator Använd:

`union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

> [!NOTE]
> Vissa fält av datatypen användning medan fortfarande i schemat har gjorts inaktuell och kommer deras värden fylls inte längre. Det här är **datorn** samt relaterade fält till inmatning (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** och **AverageProcessingTimeMs**.

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

### <a name="getting-node-counts"></a>Hämta noden räknar 

Om du är på ”Per nod (OMS)” prisnivå så debiteras du utifrån antal noder och lösningar som du använder, hur många insikter och analys noder som du faktureras kommer att visas i tabellen på den **användning och uppskattade kostnader**sidan.  

Du kan använda frågan om du vill se antalet separata noder som säkerhet:

`union
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
| count`

Använd fråga om du vill se antalet distinkta Automation-noder:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Skapa en avisering när datainsamlingen är högre än väntat
I det här avsnittet beskrivs hur du skapar en avisering om:
- Datavolymen överskrider en angiven mängd.
- Datavolymen förväntas överskrida en angiven mängd.

Azure-aviseringar har stöd för [loggaviseringar](alerts-unified-log.md) som använder sökfrågor. 

Följande fråga har ett resultat när det finns fler än 100 GB data som har samlats in under de senaste 24 timmarna:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Följande fråga använder en enkel formel för att förutsäga när mer än 100 GB data skickas under en dag: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Ändra 100 i frågan till antalet GB som du vill ange som gräns för att skicka en datavolymavisering.

Använd stegen som beskrivs i [skapa en ny loggavisering](alerts-metric.md) om du vill meddelas när datainsamlingen är högre än förväntat.

När du skapar aviseringen för den första frågan--när det finns fler än 100 GB data på 24 timmar, ange:  

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signalnamn** välj **Anpassad loggsökning**
   - **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
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
   - **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tidsperiod** på *180* minuter och **Aviseringsfrekvens** var *60*:e minut eftersom användningsdata bara uppdateras en gång i timmen.
- **Definiera aviseringsinformation** ange följande:
   - **Namnet** till *Datavolym förväntas vara större än 100 GB på 24 timmar*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

När du får en avisering kan du använda stegen i följande avsnitt för att felsöka varför användningen är högre än förväntat.

## <a name="next-steps"></a>Nästa steg
* Se [Loggsökningar i Log analytics](../log-query/log-query-overview.md) för information om hur du använder sökspråket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
* Använd stegen som beskrivs i [Skapa en ny loggavisering](alerts-metric.md) om du vill meddelas när ett sökvillkor har uppfyllts.
* Använd [lösningsriktning](../insights/solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer.
* Om du vill konfigurera en princip för insamling av effektiva händelse, granska [Azure Security Center filtreringsprincipen för](../../security-center/security-center-enable-data-collection.md).
* Ändra [prestandaräknarens konfiguration](data-sources-performance-counters.md).
* Om du vill ändra inställningarna för insamling av händelser kan du läsa [händelseloggens konfiguration](data-sources-windows-events.md).
* Om du vill ändra inställningarna för insamling av systemlogg kan du läsa [ systemloggens konfiguration](data-sources-syslog.md).


