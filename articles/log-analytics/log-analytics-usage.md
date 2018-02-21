---
title: "Analysera dataanvändning i Log Analytics| Microsoft Docs"
description: "Använd instrumentpanelen i Log Analytics för att visa hur mycket data som skickas till tjänsten Log Analytics och felsöka anledningen till att stora mängder data skickas."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2018
ms.author: magoedte
ms.openlocfilehash: 9125f3db8929a41f49ff3ae53de9f3a71f5bf051
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analysera dataanvändning i Log Analytics
Log Analytics innehåller information om hur mycket data som samlas in, vilka system som skickade data och vilka typer av data som skickas.  Använd instrumentpanelen för **Log Analytics-användning** för att se hur mycket data som skickas till Log Analytics-tjänsten. Instrumentpanelen visar hur mycket data som samlas in av varje lösning och hur mycket data som skickas av dina datorer.

## <a name="understand-the-usage-dashboard"></a>Förstå instrumentpanelen för användning
Instrumentpanelen **Log Analytics usage** (Log Analytics-användning) innehåller följande information:

- Datavolym
    - Datavolymen över tid (baserat på ditt aktuella tidsomfång)
    - Datavolym per lösning
    - Data som inte är associerade med en dator
- Datorer
    - Datorer som skickar data
    - Datorer utan data de senaste 24 timmarna
- Erbjudanden
    - Insikts- och analysnoder
    - Automatiserings- och styrningsnoder
    - Säkerhetsnoder  
- Prestanda
    - Hur lång tid det tar att samla in och indexera data  
- Lista med frågor

![instrumentpanelen användning](./media/log-analytics-usage/usage-dashboard01.png)

### <a name="to-work-with-usage-data"></a>Att arbeta med användningsdata
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.<br><br> ![Azure-portalen](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Välj en arbetsyta som du skapade tidigare i listan med Log Analytics-arbetsytor.
4. Välj **Logganalys-användning** på listan i det vänstra fönstret.
5. På instrumentpanelen **Log Analytics Usage** (Log Analytics-användning) klickar du på **Tid: Senaste 24 timmarna** om du vill ändra tidsintervallet.<br><br> ![tidsintervall](./media/log-analytics-usage/time.png)<br><br>
6. Öppna de blad för användningskategori som visar de områden som du är intresserad av. Välj ett blad och klicka sedan på ett objekt på det om du vill visa mer information i [Loggsökning](log-analytics-log-searches.md).<br><br> ![användningsblad med exempeldata](./media/log-analytics-usage/blade.png)<br><br>
7. Granska resultaten som returneras från sökningen på instrumentpanelen Loggsökning.<br><br> ![exempel på loggsökning för användning](./media/log-analytics-usage/usage-log-search.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Skapa en avisering när datainsamlingen är högre än väntat
I det här avsnittet beskrivs hur du skapar en avisering om:
- Datavolymen överskrider en angiven mängd.
- Datavolymen förväntas överskrida en angiven mängd.

Log Analytics-[aviseringar](log-analytics-alerts-creating.md) använder sökfrågor. Följande fråga har ett resultat när det finns fler än 100 GB data som har samlats in under de senaste 24 timmarna:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Följande fråga använder en enkel formel för att förutsäga när mer än 100 GB data skickas under en dag: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Ändra 100 i frågan till antalet GB som du vill ange som gräns för att skicka en datavolymavisering.

Använd stegen som beskrivs i [skapa en aviseringsregel](log-analytics-alerts-creating.md#create-an-alert-rule) om du vill meddelas när datainsamlingen är högre än förväntat.

När du skapar aviseringen för den första frågan--när det finns fler än 100 GB data på 24 timmar, ange:  
- **Namnet** till *Datavolym är större än 100 GB på 24 timmar*  
- **Allvarlighetsgrad** till *varning*  
- **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`   
- **Tidsfönster** till *24 timmar*.
- **Varningsfrekvens** till en timme, eftersom användningsdata uppdateras en gång i timmen.
- **Skapa en avisering baserat på** som *antal resultat*
- **Antalet resultat** som *större än 0*

Använd stegen som beskrivs i [Lägga till åtgärder i varningsregler](log-analytics-alerts-actions.md) för att konfigurera e-post, webhook eller runbook-åtgärd för regeln.

När du skapar aviseringen för den andra frågan--när mer än 100 GB data på 24 timmar förväntas, ange:
- **Namnet** till *Datavolym förväntas vara större än 100 GB på 24 timmar*
- **Allvarlighetsgrad** till *varning*
- **Sökfråga** till`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
- **Tidsfönster** till *3 timmar*.
- **Varningsfrekvens** till en timme, eftersom användningsdata uppdateras en gång i timmen.
- **Skapa en avisering baserat på** som *antal resultat*
- **Antalet resultat** som *större än 0*

När du får en avisering kan du använda stegen i följande avsnitt för att felsöka varför användningen är högre än förväntat.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Felsökning varför användningen är större än förväntat
På användningsinstrumentpanelen kan du identifiera varför användningen (och därmed kostnaden) är högre än vad du förväntat dig.

Högre användning orsakas av en eller båda:
- Mer data än förväntat skickas till Log Analytics
- Mer noder än förväntat som skickar data till Log Analytics

### <a name="check-if-there-is-more-data-than-expected"></a>Kontrollera om datamängden är större än förväntat 
Det finns två viktiga avsnitt på sidan för användning som hjälper dig att identifiera vad som orsakar den största datavolymen.

Diagrammet *Datavolym över tid* visar den totala mängden data som skickas och de datorer som skickar mest data. Diagrammet överst visar om din övergripande dataanvändning ökar, förblir oförändrad eller minskar. Listan över datorer som innehåller de 10 datorerna som skickar mest data.

Diagrammet *datavolym per lösning* visar mängden data som skickas av varje lösning och lösningar som skickar mest data. Diagrammet längst upp visar den totala mängden data som skickas av varje lösning över tid. Med den här informationen kan du identifiera om en lösning skickar mer data, samma datamängd eller mindre data över en längre tid. Listan med lösningar visar de 10 lösningar som skickar mest data. 

I de här två diagrammen visas alla data. Vissa data är fakturerbara och andra är kostnadsfria. Om du vill fokusera endast på data som fakturerbara ändrar du frågan på söksidan och tar med `IsBillable=true`.  

![datavolymdiagram](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Titta på diagrammet *Datavolym över tid*. Klicka på namnet på datorn för att visa de lösningar och datatyper som skickar de mest data för en specifik dator. Klicka på namnet på den första datorn i listan.

I följande skärmbild skickar datatypen *Log Management/Perf* mest data för datorn.<br><br> ![datavolymen för en dator](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Gå sedan tillbaka till instrumentpanelen *Användning* och titta på diagrammet *Datavolym per lösning*. Klicka på namnet på lösningen i listan om du vill se de datorer som skickar mest data för en lösning. Klicka på namnet på den första lösningen i listan. 

I följande skärmbild bekräftas att datorn *acmetomcat* skickar mest data för Log Management-lösningen.<br><br> ![datavolymen för en lösning](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Vid behov kan du utföra ytterligare analyser för att identifiera stora volymer inom en lösning eller datatyp. Exempelfrågor omfattar:

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

Använd följande steg för att minska mängden på de loggar som samlats in:

| Källan för hög datavolym | Hur du minskar datavolym |
| -------------------------- | ------------------------- |
| Säkerhetshändelser            | Välj [vanliga eller minimala säkerhetshändelser](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Ändra principen för säkerhetsgranskning för att endast samla in händelser som behövs. Du kan särskilt se över behovet att samla in händelser för att <br> - [granska filtreringplattform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [granska register](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [granska filsystem](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [granska kernelobjekt](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [granska hantering av manipulering](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [granska flyttbara lagringsmedia](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| Prestandaräknare       | Ändra [prestandaräknarens konfiguration](log-analytics-data-sources-performance-counters.md) för att: <br> - Minska insamlingsfrekvensen <br> - Minska antalet prestandaräknare |
| Händelseloggar                 | Ändra [händelseloggens konfiguration](log-analytics-data-sources-windows-events.md) för att: <br> - Minska antalet händelseloggar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informationsnivå* |
| Syslog                     | Ändra [systemloggkonfigurationen](log-analytics-data-sources-syslog.md) för att: <br> - Minska antalet anläggningar som samlas in <br> - Endast samla in obligatoriska händelsenivåer. Till exempel, samla inte in händelser på *Informations-* eller *Felsökningsnivå* |
| AzureDiagnostics           | Ändra logginsamlingen för resurser för att: <br> – Minska antalet resursloggar som skickas till Log Analytics <br> – Endast samla in nödvändiga loggar |
| Lösningsdata från datorer som inte behöver lösningen | Använd [lösningsriktning](../operations-management-suite/operations-management-suite-solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Kontrollera om det finns fler noder än förväntat
Om du har prisnivån *per nod (OMS)* debiteras du därefter baserat på antalet noder och lösningar som du använder. Du kan se hur många noder i varje erbjudande som används i avsnittet *erbjudanden* på instrumentpanelen för användning.<br><br> ![instrumentpanelen användning](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Klicka på **Visa alla...**  för att visa en fullständig lista över datorer som skickar data för det valda erbjudandet.

Använd [lösningsriktning](../operations-management-suite/operations-management-suite-solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer.


## <a name="next-steps"></a>Nästa steg
* Se [Loggsökningar i Log analytics](log-analytics-log-searches.md) för information om hur du använder sökspråket. Du kan använda sökfrågor för att utföra ytterligare analys på användningsdata.
* Använd stegen som beskrivs i [Skapa en aviseringsregel](log-analytics-alerts-creating.md#create-an-alert-rule) om du vill meddelas när ett sökvillkor har uppfyllts
* Använd [lösningsmål](../operations-management-suite/operations-management-suite-solution-targeting.md) för att endast samla in data från obligatoriska grupper med datorer
* Om du vill konfigurera en effektiv princip för insamling av säkerhetshändelse kan du läsa [filtreringsprincipen för Azure Security Center](../security-center/security-center-enable-data-collection.md)
* Ändra [prestandaräknarens konfiguration](log-analytics-data-sources-performance-counters.md)
* Om du vill ändra inställningarna för insamling av händelser kan du läsa [händelseloggens konfiguration](log-analytics-data-sources-windows-events.md)
* Om du vill ändra inställningarna för insamling av systemlogg kan du läsa [ systemloggens konfiguration](log-analytics-data-sources-syslog.md)
