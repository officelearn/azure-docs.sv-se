---
title: Analysera dataanvändning i Log Analytics| Microsoft Docs
description: Med instrumentpanelen för användning och beräknad kostnad i Log Analytics kan du utvärdera hur mycket data som skickas till Log Analytics och identifiera vad som kan orsaka oförutsedda ökningar.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 843271901b8d58c2c5a6c4cf495997498b8278b6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848858"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analysera dataanvändning i Log Analytics

> [!NOTE]
> Den här artikeln beskriver hur du analysera dataanvändning i Log Analytics.  Finns i följande artiklar för relaterad information.
> - [Hantera kostnader genom att kontrollera datavolymer och kvarhållning i Log Analytics](log-analytics-manage-cost-storage.md) beskrivs hur du kontrollerar dina kostnader genom att ändra kvarhållningsperioden för data.
> - [Övervaka användning och uppskattade kostnader](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

## <a name="understand-usage"></a>Förstå användningen

Använd **Log Analytics-användning och uppskattade kostnader** att granska och analysera dataanvändning. Visar hur mycket data som samlas in av varje lösning, hur mycket data bevaras och en uppskattning av dina kostnader utifrån mängden data som samlas in och alla kvarhållning utöver mängden som ingår.

![Användning och uppskattade kostnader](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Om du vill utforska dina data i mer detalj klickar du på ikonen längst upp höger på något av diagrammen i den **användning och uppskattade kostnader** sidan. Nu kan du arbeta med den här frågan att utforska mer information om din användning.  

![Visa loggar](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat
Högre användning orsakas av en eller båda:
- Mer data än förväntat skickas till Log Analytics
- Fler noder än förväntat skicka data till Log Analytics eller vissa noder skickar mer data än vanligt

Låt oss ta en titt på hur vi kan läsa mer om båda dessa orsaker. 

> [!NOTE]
> Vissa fält av datatypen användning, medan fortfarande i schemat har ersatts och deras värden fylls inte längre. Det här är **datorn** samt relaterade fält till inmatning (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** och **AverageProcessingTimeMs**.

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

För att förstå antalet noder som rapporterar data för den senaste månaden, använda

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Om du vill se antalet händelser som matas in per dator

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Använd den här frågan sparsamt eftersom det är dyrt att köra. Om du vill se antalet faktureringsbara händelser matas in per dator 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Om du vill se vilka datatyper som fakturerbara skickar data till en specifik dator Använd:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

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
| Säkerhetshändelser            | Välj [vanliga eller minimala säkerhetshändelser](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Ändra principen för säkerhetsgranskning för att endast samla in händelser som behövs. Du kan särskilt se över behovet att samla in händelser för att <br> - [granska filtreringplattform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [granska register](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [granska filsystem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [granska kernelobjekt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [granska hantering av manipulering](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Granska flyttbara lagringsmedia |
| Prestandaräknare       | Ändra [prestandaräknarens konfiguration](log-analytics-data-sources-performance-counters.md) för att: <br> - Minska insamlingsfrekvensen <br> - Minska antalet prestandaräknare |
| Händelseloggar                 | Ändra [händelseloggens konfiguration](log-analytics-data-sources-windows-events.md) för att: <br> - Minska antalet händelseloggar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informationsnivå* |
| Syslog                     | Ändra [systemloggkonfigurationen](log-analytics-data-sources-syslog.md) för att: <br> - Minska antalet anläggningar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informations-* eller *Felsökningsnivå* |
| AzureDiagnostics           | Ändra logginsamlingen för resurser för att: <br> – Minska antalet resursloggar som skickas till Log Analytics <br> – Endast samla in nödvändiga loggar |
| Lösningsdata från datorer som inte behöver lösningen | Använd [lösningsriktning](../azure-monitor/insights/solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer. |

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

Azure-aviseringar har stöd för [loggaviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) som använder sökfrågor. 

Följande fråga har ett resultat när det finns fler än 100 GB data som har samlats in under de senaste 24 timmarna:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Följande fråga använder en enkel formel för att förutsäga när mer än 100 GB data skickas under en dag: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Ändra 100 i frågan till antalet GB som du vill ange som gräns för att skicka en datavolymavisering.

Använd stegen som beskrivs i [skapa en ny loggavisering](../monitoring-and-diagnostics/alert-metric.md) om du vill meddelas när datainsamlingen är högre än förväntat.

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

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](../monitoring-and-diagnostics/monitoring-action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

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

Ange en befintlig eller skapa en ny [Åtgärdsgrupp](../monitoring-and-diagnostics/monitoring-action-groups.md) så att när loggaviseringen matchar kriterierna, får du ett meddelande.

När du får en avisering kan du använda stegen i följande avsnitt för att felsöka varför användningen är högre än förväntat.

## <a name="next-steps"></a>Nästa steg
* Se [Loggsökningar i Log analytics](../azure-monitor/log-query/log-query-overview.md) för information om hur du använder sökspråket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
* Använd stegen som beskrivs i [Skapa en ny loggavisering](../monitoring-and-diagnostics/alert-metric.md) om du vill meddelas när ett sökvillkor har uppfyllts.
* Använd [lösningsriktning](../azure-monitor/insights/solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer.
* Om du vill konfigurera en effektiv princip för insamling av säkerhetshändelse kan du läsa [filtreringsprincipen för Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Ändra [prestandaräknarens konfiguration](log-analytics-data-sources-performance-counters.md).
* Om du vill ändra inställningarna för insamling av händelser kan du läsa [händelseloggens konfiguration](log-analytics-data-sources-windows-events.md).
* Om du vill ändra inställningarna för insamling av systemlogg kan du läsa [ systemloggens konfiguration](log-analytics-data-sources-syslog.md).
