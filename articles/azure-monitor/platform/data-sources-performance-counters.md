---
title: Samla in och analysera prestandaräknare i Azure Monitor | Microsoft Docs
description: Prestandaräknare samlas in av Azure Monitor för att analysera prestanda på Windows och Linux-agenter.  Den här artikeln beskriver hur du konfigurerar insamling av prestandaräknare för både Windows och Linux-agenter, information om de lagras i arbetsytan och hur du analyserar dem i Azure-portalen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 93f47529e3be44ff1db4e089bdcdca3eb1b4dea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363519"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows- och Linux prestanda datakällor i Azure Monitor
Prestandaräknare i Windows och Linux ger information om prestanda för maskinvarukomponenter, operativsystem och program.  Azure Monitor kan samla in prestandaräknare med återkommande intervall för analys i nära realtid (NRT) utöver sammanställa prestandadata för längre sikt analys och rapportering.

![Prestandaräknare](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurera prestandaräknare
Konfigurerar prestandaräknare från den [Data-menyn i avancerade inställningar](agent-data-sources.md#configuring-data-sources).

När du först konfigurerar Windows eller Linux-prestanda räknare för en ny arbetsyta har möjlighet att snabbt skapa flera vanliga räknare.  De listas med en kryssruta bredvid varje.  Se till att alla räknare som du vill skapa från början är markerade och klickar sedan på **Lägg till valda prestandaräknare**.

Du kan välja en specifik instans för varje prestandaräknare för Windows-prestandaräknare. Instansen av varje räknare som du väljer gäller för alla underordnade räknare för den överordnade räknaren för prestandaräknare för Linux. I följande tabell visar de vanliga instanserna som är tillgängliga för både Linux och Windows prestandaräknare.

| Instansnamn | Beskrivning |
| --- | --- |
| \_Totalt |Summan av alla instanser |
| \* |Alla instanser |
| (/&#124;/var) |Matchar instanser med namnet: / eller /var |

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

![Konfigurera Windows-prestandaräknare](media/data-sources-performance-counters/configure-windows.png)

Följ den här proceduren för att lägga till en ny Windows-prestandaräknare för att samla in.

1. Skriv namnet på räknaren i textrutan i formatet *objekt (förekomst) \counter*.  När du börjar skriva, visas med en matchande lista över vanliga räknare.  Du kan välja en räknare från listan eller skriver in en egen.  Du kan också returnera alla instanser för en särskild räknare genom att ange *objekt räknare*.  

    När SQL Server-prestandaräknare har samlats in från namngivna instanser, alla namnet instans räknare börjar med *MSSQL$* och följt av namnet på instansen.  Ange till exempel för att samla in räknaren Log Cache träffar förhållande för alla databaser från databasobjekt för prestanda för namngivna SQL-instansen INST2, `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Klicka på **+** eller tryck på **RETUR** att lägga till räknaren i listan.
3. När du lägger till en räknare, används standardvärdet 10 sekunder tills dess **provintervall**.  Du kan ändra detta till ett högre värde för upp till 1 800 sekunder (30 minuter) om du vill minska utrymmeskraven av insamlade prestandadata.
4. När du är klar att lägga till räknare klickar du på den **spara** längst upp på skärmen för att spara konfigurationen.

### <a name="linux-performance-counters"></a>Prestandaräknare för Linux

![Konfigurera Linux-prestandaräknare](media/data-sources-performance-counters/configure-linux.png)

Följ den här proceduren för att lägga till en ny Linux-prestandaräknare för att samla in.

1. Som standard skickas automatiskt alla konfigurationsändringar till alla agenter.  För Linux-agenter skickas en konfigurationsfil till Fluentd datainsamlaren.  Om du vill ändra den här filen manuellt på varje Linux-agenten och avmarkerar sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer* och följ anvisningarna nedan.
2. Skriv namnet på räknaren i textrutan i formatet *objekt (förekomst) \counter*.  När du börjar skriva, visas med en matchande lista över vanliga räknare.  Du kan välja en räknare från listan eller skriver in en egen.  
3. Klicka på **+** eller tryck på **RETUR** att lägga till räknaren i listan över andra räknare för objektet.
4. Alla räknare för ett objekt som använder samma **provintervall**.  Standardvärdet är 10 sekunder.  Du ändra det till ett högre värde för upp till 1 800 sekunder (30 minuter) om du vill minska utrymmeskraven av insamlade prestandadata.
5. När du är klar att lägga till räknare klickar du på den **spara** längst upp på skärmen för att spara konfigurationen.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurera Linux-prestandaräknare i konfigurationsfilen
I stället för att konfigurera prestandaräknare för Linux med Azure portal, har du möjlighet att redigera konfigurationsfiler på Linux-agenten.  Prestandamått att samla in styrs av konfigurationen i **/etc/opt/microsoft/omsagent/\<arbetsyte-id\>/conf/omsagent.conf**.

Varje objekt eller en kategori, prestandamått att samla in för ska definieras i konfigurationsfilen som en enda `<source>` element. Syntaxen följer mönstret nedan.

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
| instance\_regex |  En *reguljärt uttryck* definierar vilka instanser för att samla in. Värdet: `.*` anger alla instanser. Samla in mått för processor för endast den \_totala instans kan du ange `_Total`. Om du vill samla in processen mått för endast crond eller sshd-instanser, kan du ange: `(crond\|sshd)`. |
| räknaren\_namn\_regex | En *reguljärt uttryck* definierar vilka prestandaräknare (för objektet) att samla in. Om du vill samla in alla räknare för objektet, ange: `.*`. Om du vill samla in endast växling utrymme räknare för i minnet, t.ex, kan du ange: `.+Swap.+` |
| interval | Frekvens vid vilken objektets prestandaräknare som samlas in. |


I följande tabell visas de objekt och räknare som du kan ange i konfigurationsfilen.  Det finns ytterligare räknare för vissa program enligt beskrivningen i [samla in prestandaräknare för Linux-program i Azure Monitor](data-sources-linux-applications.md).

| Objektnamn | Räknarnamn |
|:--|:--|
| Logisk Disk | % Ledigai-noder |
| Logisk Disk | Ledigt utrymme i procent |
| Logisk Disk | % Användai-noder |
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
| Minne | Använt minne Mbyte |
| Nätverk | Totalt antal överförda byte |
| Nätverk | Totalt antal byte mottaget |
| Nätverk | Totalt antal byte |
| Nätverk | Totalt antal skickade paket |
| Nätverk | Totalt antal mottagna paket |
| Nätverk | Totalt antal Rx-fel |
| Nätverk | Totalt antal Tx-fel |
| Nätverk | Totalt antal kollisioner |
| Fysisk Disk | Genomsn. S/diskläsning |
| Fysisk Disk | Genomsn. S/disköverföring |
| Fysisk Disk | Genomsn. S/diskskrivning |
| Fysisk Disk | Fysisk Disk byte/sek |
| Process | PCT privilegierad tid |
| Process | PCT användartid |
| Process | Använt minne kB |
| Process | Virtuella delat minne |
| Processor | % DPC-tid |
| Processor | Inaktivitetstid i procent |
| Processor | Avbrottstid i procent |
| Processor | Väntetid i procent i/o |
| Processor | Nice Time |
| Processor | Privilegierad tid i procent |
| Processor | Tid i procent för processor |
| Processor | Användartid i procent |
| System | Ledigt fysiskt minne |
| System | Ledigt utrymme i växlingsfiler |
| System | Ledigt virtuellt minne |
| System | Processer |
| System | Storlek lagrad i växlingsfiler |
| System | Drifttid |
| System | Användare |


Följande är standardkonfigurationen för prestandamått.

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
Azure Monitor samlar in alla angivna prestandaräknare på sina angivna provintervall på alla agenter som har som antalet installerade.  Informationen sammanställs inte och rådata är tillgänglig i alla log frågevyer för den tid som anges av din prenumeration.

## <a name="performance-record-properties"></a>Egenskaper för prestanda-post
Prestandaposter har en typ av **Perf** och har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Dator |Datorn där händelsen har samlats in från. |
| CounterName |Namnet på prestandaräknaren |
| CounterPath |Fullständig sökväg för räknaren i formuläret \\ \\ \<dator >\\objekt(instans)\\räknaren. |
| CounterValue |Numeriskt värde för räknaren. |
| Instansnamn |Namnet på den händelse-instansen.  Tomt om ingen instans. |
| ObjectName |Namnet på ett objekt |
| SourceSystem |Typ av data har samlats in från agenten. <br><br>Ansluta OpsManager – Windows-agenten, antingen direkt eller SCOM <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum och tid för data har provtagning. |

## <a name="sizing-estimates"></a>Beräknar storlek
 En grov uppskattning för insamling av en särskild räknare med 10 sekunder intervall är ungefär 1 MB per dag per instans.  Du kan beräkna en särskild räknare med följande formel lagringskrav.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Loggfrågor med prestandaposter
I följande tabell innehåller olika exempel på loggfrågor som hämtar prestandaposter.

| Fråga | Beskrivning |
|:--- |:--- |
| Perf |Alla prestandadata |
| Perf &#124; där dator == ”den här datorn” |Alla prestandadata från en viss dator |
| Perf &#124; där CounterName == ”den Aktuell diskkölängd” |Alla prestandadata för en särskild räknare |
| Perf &#124; där ObjectName == ”Processor” och CounterName == ”% processortid” och InstanceName == ”_Total” &#124; sammanfatta AVGCPU = avg(Average) per dator |Genomsnittlig CPU-användningen över alla datorer |
| Perf &#124; där CounterName == ”% processortid” &#124; sammanfatta AggregatedValue = max(Max) per dator |Högsta CPU-användningen över alla datorer |
| Perf &#124; där ObjectName == ”logisk disk” och CounterName == ”den Aktuell diskkölängd” och dator == ”MyComputerName” &#124; sammanfatta AggregatedValue = avg(Average) av instansnamn |Genomsnittlig Kölängd för aktuella Disk i alla instanser av en viss dator |
| Perf &#124; där CounterName == ”DiskTransfers/sek” &#124; sammanfatta AggregatedValue =: e percentilen (Genomsnittlig, 95) per dator |95: e percentilen för disköverföringar/sek på alla datorer |
| Perf &#124; där CounterName == ”% processortid” och InstanceName == ”_Total” &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), dator |Per timme, genomsnitt CPU-användning på alla datorer |
| Perf &#124; där dator == ”den här datorn” och CounterName startswith_cs ”%” och InstanceName == ”_Total” &#124; sammanfatta AggregatedValue =: e percentilen (CounterValue 70) efter bin (TimeGenerated, 1 timme), CounterName | Per timme 70: e percentilen för varje procent räknaren % för en viss dator |
| Perf &#124; där CounterName == ”% processortid” och InstanceName == ”_Total” och dator == ”den här datorn” &#124; sammanfatta [”min(CounterValue)”] = min(CounterValue), [”avg(CounterValue)”] = avg(CounterValue), [”percentile75(CounterValue)”] =: e percentilen (CounterValue, 75), [”max(CounterValue)”] = max(CounterValue) efter bin (TimeGenerated, 1 timme), dator |Per timme average, lägsta, högsta och 75: e percentilen CPU-användning för en specifik dator |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Alla prestandadata från databasobjekt för prestanda för master-databasen från den namngivna SQL Server-instansen INST2.  




## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare från Linux-program](data-sources-linux-applications.md) inklusive MySQL och Apache HTTP Server.
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.  
* Exportera insamlade data [Power BI](powerbi.md) för fler visualiseringar och analys.
