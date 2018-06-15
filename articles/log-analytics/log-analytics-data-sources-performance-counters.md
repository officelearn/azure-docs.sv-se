---
title: Samla in och analysera prestandaräknare i Azure Log Analytics | Microsoft Docs
description: Prestandaräknarna samlas in av logganalys att analysera prestanda på Windows och Linux-agenter.  Den här artikeln beskriver hur du konfigurerar insamling av prestandaräknare för både Windows och Linux-agenter, information om de lagras i arbetsytan och analysera dem i Azure-portalen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.openlocfilehash: 0f7119f280f2eb51222ade2ea7984b560a02f667
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783170"
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Windows- och Linux prestanda datakällor i logganalys
Prestandaräknare i Windows och Linux ger kunskaper om prestandan för maskinvarukomponenter, operativsystem och program.  Logganalys kan samla in prestandaräknare med återkommande intervall för analys i nära realtid (NRT) utöver sammanställa prestandadata för längre sikt analys och rapportering.

![Prestandaräknare](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurera prestandaräknare
Konfigurera prestandaräknare från den [Data-menyn i logganalys-inställningar](log-analytics-data-sources.md#configuring-data-sources).

När du först konfigurera räknare för Windows eller Linux-prestanda för en ny logganalys-arbetsyta möjlighet du att snabbt skapa flera vanliga räknare.  De listas med en kryssruta bredvid varje.  Se till att räknare du vill skapa inledningsvis är markerade och klickar sedan på **Lägg till valda prestandaräknare**.

Du kan välja en specifik instans för varje prestandaräknare för Windows-prestandaräknare. För Linux-prestandaräknare gäller instansen av varje räknare som du väljer alla underordnade räknare för den överordnade räknaren. I följande tabell visas vanliga instanser som är tillgängliga för både Linux och Windows prestandaräknare.

| Instansnamn | Beskrivning |
| --- | --- |
| \_Totalt |Summan av alla instanser |
| \* |Alla instanser |
| (/ &#124; / var) |Matchar instanser med namnet: / eller /var |

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

![Konfigurera Windows-prestandaräknare](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Gör så här för att lägga till en ny Windows prestandaräknare för att samla in.

1. Skriv namnet på räknaren i textrutan i formatet *objekt (förekomst) \counter*.  När du börjar skriva visas med en matchande lista över vanliga räknare.  Du kan välja en räknare från listan eller Skriv en egen.  Du kan också returnera alla instanser för räknaren genom att ange *objekt räknare*.  

    När SQL Server-prestandaräknare har samlats in från namngivna instanser, alla namngivna instansen räknare starta med *MSSQL$* och följt av namnet på instansen.  Till exempel för att samla in räknaren loggen Cache nått kvoten för alla databaser från databasen prestandaobjektet för namngiven SQL-instansen INST2, ange `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Klicka på  **+**  eller tryck på **RETUR** att lägga till räknaren i listan.
3. När du lägger till en räknare, används standardvärdet 10 sekunder för dess **provintervallet**.  Du kan ändra det till ett högre värde av upp till 1 800 sekunder (30 minuter) om du vill minska utrymmeskraven för prestandadata som samlats in.
4. När du är klar att lägga till räknare klickar du på den **spara** längst upp på skärmen för att spara konfigurationen.

### <a name="linux-performance-counters"></a>Linux-prestandaräknare

![Konfigurera Linux-prestandaräknare](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Gör så här för att lägga till en ny Linux prestandaräknare för att samla in.

1. Som standard pushas alla konfigurationsändringar automatiskt till alla agenter.  Linux-agenter skickas en konfigurationsfil till Fluentd datainsamlaren.  Om du vill ändra den här filen manuellt på varje Linux-agenten och avmarkera sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer* och följer du anvisningarna nedan.
2. Skriv namnet på räknaren i textrutan i formatet *objekt (förekomst) \counter*.  När du börjar skriva visas med en matchande lista över vanliga räknare.  Du kan välja en räknare från listan eller Skriv en egen.  
3. Klicka på  **+**  eller tryck på **RETUR** lägga till räknaren i listan över andra räknare för objektet.
4. Alla räknare för ett objekt som använder samma **provintervallet**.  Standardvärdet är 10 sekunder.  Du ändra det till ett högre värde av upp till 1 800 sekunder (30 minuter) om du vill minska utrymmeskraven för prestandadata som samlats in.
5. När du är klar att lägga till räknare klickar du på den **spara** längst upp på skärmen för att spara konfigurationen.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurera Linux prestandaräknare i konfigurationsfilen
I stället för att konfigurera Linux prestandaräknare i Azure Portal har du möjlighet att redigera konfigurationsfiler på Linux-agenten.  Prestandamått för att samla in styrs av konfigurationen i **/etc/opt/microsoft/omsagent/\<arbetsyte-id\>/conf/omsagent.conf**.

Varje objekt eller kategori för prestandavärden att samla in ska vara definierat i konfigurationsfilen som en enda `<source>` element. Syntaxen följer mönstret nedan.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Parametrarna i det här elementet beskrivs i följande tabell.

| Parametrar | Beskrivning |
|:--|:--|
| objektet\_namn | Objektnamn för samlingen. |
| instansen\_regex |  En *reguljärt uttryck* definierar vilka instanser som ska samla in. Värdet: `.*` anger alla instanser. Att samla in processor mätvärden för endast den \_totala instans kan du ange `_Total`. Du kan ange om du vill samla in Processmätvärden för endast crond eller sshd instanser: ' (crond\|sshd)'. |
| räknaren\_namn\_regex | En *reguljärt uttryck* definierar vilket räknare (för objektet) för att samla in. Om du vill samla in alla räknare för objektet, ange: `.*`. Om du vill samla in endast växlingen utrymme räknare för i minnet, t.ex, kan du ange:`.+Swap.+` |
| interval | Frekvensen då objektets räknare samlas in. |


I följande tabell visas de objekt och räknare som du kan ange i konfigurationsfilen.  Det finns ytterligare räknare för vissa program som beskrivs i [samla in prestandaräknare för Linux-program i logganalys](log-analytics-data-sources-linux-applications.md).

| Objektnamn | Räknarens namn |
|:--|:--|
| Logisk Disk | % Ledigai |
| Logisk Disk | Ledigt utrymme i procent |
| Logisk Disk | % Användai |
| Logisk Disk | Använt utrymme i procent |
| Logisk Disk | Disk – lästa byte/sek |
| Logisk Disk | Diskläsningar/sek |
| Logisk Disk | Disköverföringar/sek |
| Logisk Disk | Disk – skrivna byte/sek |
| Logisk Disk | Diskskrivningar/sek |
| Logisk Disk | Ledigt utrymme i MB |
| Logisk Disk | Logisk Disk byte/sek |
| Minne | Tillgängligt minne i procent |
| Minne | Tillgängligt växlingsutrymme i procent |
| Minne | Använt minne i procent |
| Minne | Använt växlingsutrymme i procent |
| Minne | Tillgängligt minne i megabyte |
| Minne | Tillgängliga megabyte växlingsutrymme |
| Minne | Sidläsningar/sek |
| Minne | Sidskrivningar/sek |
| Minne | Sidor/sek |
| Minne | Använt växlingsutrymme i megabyte |
| Minne | Använt minne MB |
| Nätverk | Sammanlagt antal överförda byte |
| Nätverk | Totalt antal mottagna byte |
| Nätverk | Totalt antal byte |
| Nätverk | Totalt antal skickade paket |
| Nätverk | Totalt antal mottagna paket |
| Nätverk | Totalt antal Rx fel |
| Nätverk | Totalt antal Tx-fel |
| Nätverk | Totalt antal kollisioner |
| Fysisk Disk | Genomsn. Disk sek/läsning |
| Fysisk Disk | Genomsn. Disk sek/disköverföring |
| Fysisk Disk | Genomsn. Disk sek/skrivning |
| Fysisk Disk | Fysisk Disk byte/sek |
| Process | PCT privilegierad tid |
| Process | PCT användartid |
| Process | Använt minne Kbyte |
| Process | Virtuella delat minne |
| Processor | % DPC-tid |
| Processor | Inaktivitetstid i procent |
| Processor | Avbrottstid i procent |
| Processor | %-I/o-väntetid |
| Processor | Nice Time i procent |
| Processor | Privilegierad tid i procent |
| Processor | % Processortid |
| Processor | Användartid i procent |
| System | Fysiskt minne |
| System | Ledigt utrymme i växlingsfiler |
| System | Ledigt virtuellt minne |
| System | Processer |
| System | Storlek lagrad i växlingsfiler |
| System | Drifttid |
| System | Användare |


Följande är standardkonfigurationen för prestandavärden.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Datainsamling
Logganalys samlar in alla angivna prestandaräknare på sina angivna provintervallet i alla agenter som har som räknaren installerad.  Informationen sammanställs inte och rådata är tillgänglig i alla loggen Sök vyer för den tid som anges av din prenumeration.

## <a name="performance-record-properties"></a>Prestanda post egenskaper
Prestanda poster har en typ av **Perf** och ha egenskaper i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Dator som händelsen samlats in från. |
| CounterName |Namnet på prestandaräknaren |
| Räknarsökväg |Fullständig sökväg för räknaren i formuläret \\ \\ \<dator >\\objekt(instans)\\räknaren. |
| CounterValue |Numeriskt värde för räknaren. |
| Instansnamn |Namnet på instansen för händelsen.  Tomt om ingen instans. |
| Objektnamn |Namnet på ett objekt |
| SourceSystem |Typ av agenten informationen som samlats in från. <br><br>Ansluta OpsManager – Windows-agenten, antingen direkt eller SCOM <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure-diagnostik |
| TimeGenerated |Datum och tid för data var provtagning. |

## <a name="sizing-estimates"></a>Beräknar storlek
 En grov uppskattning för samling för räknaren vid intervall om 10 sekunder är cirka 1 MB per dag per instans.  Du kan beräkna lagringskraven för räknaren med följande formel.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Loggen sökningar med uppgifter
Följande tabell innehåller olika exempel på loggen sökningar som hämtar uppgifter.

| Fråga | Beskrivning |
|:--- |:--- |
| Perf |Alla prestandadata |
| Perf &#124; Om datorn == ”den här datorn” |Alla data från en viss dator |
| Perf &#124; Om CounterName == ”den Aktuell diskkölängd” |Alla prestandadata för räknaren |
| Perf &#124; där ObjectName == ”-Processor” och CounterName == ”% processortid” och InstanceName == ”_Total” &#124; Sammanfatta AVGCPU = avg(Average) per dator |Genomsnittlig CPU-användning på alla datorer |
| Perf &#124; Om CounterName == ”% processortid” &#124; Sammanfatta AggregatedValue = max(Max) per dator |Högsta CPU-användning på alla datorer |
| Perf &#124; där ObjectName == ”logisk disk” och CounterName == ”den Aktuell diskkölängd” och datorn == ”MyComputerName” &#124; Sammanfatta AggregatedValue = avg(Average) av instansnamn |Genomsnittlig Kölängd för aktuella Disk i alla instanser av en viss dator |
| Perf &#124; Om CounterName == ”DiskTransfers/sek” &#124; Sammanfatta AggregatedValue = percentil (medel, 95) per dator |95: e percentilen för disköverföringar/sek på alla datorer |
| Perf &#124; Om CounterName == ”% processortid” och InstanceName == ”_Total” &#124; Sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), datorn |Varje timme medelvärdet av CPU-användning på alla datorer |
| Perf &#124; Om datorn == ”den här datorn” och CounterName startswith_cs ”%” och InstanceName == ”_Total” &#124; Sammanfatta AggregatedValue = percentil (CounterValue 70) som bin (TimeGenerated 1 tim), CounterName | Varje timme 70: e percentilen för varje procent räknaren % för en viss dator |
| Perf &#124; Om CounterName == ”% processortid” och InstanceName == ”_Total” och datorn == ”den här datorn” &#124; Sammanfatta [”min(CounterValue)”] = min(CounterValue), [”avg(CounterValue)”] = avg(CounterValue), [”percentile75(CounterValue)”] = percentil (CounterValue 75), [”max(CounterValue)”] = max(CounterValue) av bin (TimeGenerated 1 tim), datorn |Varje timme medelvärde, lägsta, högsta och 75 percentil CPU-användning för en specifik dator |
| Perf &#124; där ObjectName == ”MSSQL$ INST2: databaser” och InstanceName == ”master” | Alla prestandadata från databasen prestandaobjekt för master-databasen från den namngivna SQL Server-instansen INST2.  




## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare från Linux-program](log-analytics-data-sources-linux-applications.md) inklusive MySQL och Apache HTTP-servern.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  
* Exportera insamlade data till [Power BI](log-analytics-powerbi.md) för ytterligare grafik och analys.
