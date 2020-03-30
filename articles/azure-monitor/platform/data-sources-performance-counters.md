---
title: Samla in och analysera prestandaräknare i Azure Monitor | Microsoft-dokument
description: Prestandaräknare samlas in av Azure Monitor för att analysera prestanda på Windows- och Linux-agenter.  I den här artikeln beskrivs hur du konfigurerar insamling av prestandaräknare för både Windows- och Linux-agenter, information om dem lagras på arbetsytan och hur du analyserar dem i Azure-portalen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274767"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Prestandadatakällor för Windows och Linux i Azure Monitor
Prestandaräknare i Windows och Linux ger insikt i prestanda för maskinvarukomponenter, operativsystem och program.  Azure Monitor kan samla in prestandaräknare med täta intervall för NRT-analys (Near Real Time) förutom att samla in prestandadata för långsiktig analys och rapportering.

![Prestandaräknare](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurera prestandaräknare
Konfigurera prestandaräknare från [datamenyn i Avancerade inställningar](agent-data-sources.md#configuring-data-sources).

När du först konfigurerar Windows- eller Linux-prestandaräknare för en ny arbetsyta får du möjlighet att snabbt skapa flera vanliga räknare.  De listas med en kryssruta bredvid varje.  Kontrollera att alla räknare som du vill skapa först är markerade och klicka sedan på **Lägg till de valda prestandaräknarna**.

För Windows-prestandaräknare kan du välja en specifik instans för varje prestandaräknare. För Linux-prestandaräknare gäller förekomsten av varje räknare som du väljer för alla underordnade räknare för den överordnade räknaren. I följande tabell visas de vanliga instanser som är tillgängliga för både Linux- och Windows-prestandaräknare.

| Instansnamn | Beskrivning |
| --- | --- |
| \_Totalt |Totalt antal instanser |
| \* |Alla instanser |
| (/&#124;/var) |Matchar förekomster med namnet: / eller /var |

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

![Konfigurera prestandaräknare för Windows](media/data-sources-performance-counters/configure-windows.png)

Följ den här proceduren om du vill lägga till en ny prestandaräknare för Windows att samla in.

1. Skriv namnet på räknaren i textrutan i *formatobjekt(instans)\counter*.  När du börjar skriva visas en matchande lista med vanliga räknare.  Du kan antingen välja en räknare i listan eller skriva in en av dina egna.  Du kan också returnera alla instanser för en viss räknare genom att ange *object\counter*.  

    När du samlar in SQL Server-prestandaräknare från namngivna instanser börjar alla namngivna instansräknare med *MSSQL$* och följt av namnet på instansen.  Om du till exempel vill samla in räknaren Loggcachehit ratio för alla databaser `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`från databasprestandaobjektet för namngivna SQL-instansEN INST2 anger du .

2. Klicka **+** eller tryck på **Retur** för att lägga till räknaren i listan.
3. När du lägger till en räknare används standardvärdet på 10 sekunder för **samplingsintervallet**.  Du kan ändra detta till ett högre värde på upp till 1800 sekunder (30 minuter) om du vill minska lagringskraven för de insamlade prestandadata.
4. När du är klar med att lägga till räknare klickar du på **knappen Spara** högst upp på skärmen för att spara konfigurationen.

### <a name="linux-performance-counters"></a>Prestandaräknare för Linux

![Konfigurera Linux-prestandaräknare](media/data-sources-performance-counters/configure-linux.png)

Följ den här proceduren för att lägga till en ny Linux-prestandaräknare att samla in.

1. Som standard skjuts alla konfigurationsändringar automatiskt till alla agenter.  För Linux-agenter skickas en konfigurationsfil till Datainsamlaren för Fluentd.  Om du vill ändra den här filen manuellt på varje Linux-agent, avmarkera sedan rutan *Använd nedan konfiguration till mina Linux-maskiner* och följ vägledningen nedan.
2. Skriv namnet på räknaren i textrutan i *formatobjekt(instans)\counter*.  När du börjar skriva visas en matchande lista med vanliga räknare.  Du kan antingen välja en räknare i listan eller skriva in en av dina egna.  
3. Klicka **+** eller tryck på **Retur** om du vill lägga till räknaren i listan över andra räknare för objektet.
4. Alla räknare för ett objekt använder samma **exempelintervall**.  Standardvärdet är 10 sekunder.  Du ändrar detta till ett högre värde på upp till 1800 sekunder (30 minuter) om du vill minska lagringskraven för de insamlade prestandadata.
5. När du är klar med att lägga till räknare klickar du på **knappen Spara** högst upp på skärmen för att spara konfigurationen.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurera Linux-prestandaräknare i konfigurationsfilen
I stället för att konfigurera Linux-prestandaräknare med Azure-portalen har du möjlighet att redigera konfigurationsfiler på Linux-agenten.  Prestandamått som ska samlas in styrs av konfigurationen i **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**.

Varje objekt, eller kategori, av prestandamått som ska samlas in `<source>` bör definieras i konfigurationsfilen som ett enda element. Syntaxen följer mönstret nedan.

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
| objektnamn\_ | Objektnamn för samlingen. |
| instans\_regex |  Ett *reguljärt uttryck* som definierar vilka instanser som ska samlas in. Värdet: `.*` anger alla instanser. Om du bara vill \_samla in processormått `_Total`för den totala instansen kan du ange . Om du bara vill samla in processmått för crond- `(crond\|sshd)`eller sshd-instanser kan du ange: . |
| \_motnamn\_regex | Ett *reguljärt uttryck* som definierar vilka räknare (för objektet) som ska samlas in. Om du vill samla in alla `.*`räknare för objektet anger du: . Om du till exempel bara vill samla in växlingsutrymmesräknare för minnesobjektet kan du ange:`.+Swap.+` |
| interval | Frekvens vid vilken objektets räknare samlas in. |


I följande tabell visas de objekt och räknare som du kan ange i konfigurationsfilen.  Det finns ytterligare räknare tillgängliga för vissa program enligt beskrivningen i [Samla in prestandaräknare för Linux-program i Azure Monitor](data-sources-linux-applications.md).

| Objektets namn | Motnamn |
|:--|:--|
| Logisk disk | % gratis inoder |
| Logisk disk | % ledigt utrymme |
| Logisk disk | % använda inoder |
| Logisk disk | Använt utrymme i % |
| Logisk disk | Diskläs byte per sekund |
| Logisk disk | Diskläsningar/sek |
| Logisk disk | Disköverföringar per sekund |
| Logisk disk | Byte för diskskrivning/sek |
| Logisk disk | Diskskrivningar per sekund |
| Logisk disk | Gratis Megabyte |
| Logisk disk | Logiska diskbyten per sekund |
| Minne | % tillgängligt minne |
| Minne | % tillgängligt växlingsutrymme |
| Minne | % använt minne |
| Minne | % använt växlingsutrymme |
| Minne | Tillgängligt MBytes-minne |
| Minne | Tillgänglig MBytes Swap |
| Minne | Sidan läser/sek |
| Minne | Sidan skriver per sekund |
| Minne | Sidor per sekund |
| Minne | Använt MBytes-växlingsutrymme |
| Minne | Använt minne MBytes |
| Nätverk | Totalt antal överförda byte |
| Nätverk | Totalt antal mottagna byte |
| Nätverk | Totalt antal byte |
| Nätverk | Totalt antal överförda paket |
| Nätverk | Totalt antal mottagna paket |
| Nätverk | Totalt Rx-fel |
| Nätverk | Totalt antal Tx-fel |
| Nätverk | Totalt kollisioner |
| Fysisk disk | Genomsnittlig disk sek/läs |
| Fysisk disk | Genomsnittlig disk sek/överföring |
| Fysisk disk | Genomsnittlig disk sek/skrivning |
| Fysisk disk | Fysiska diskbyten per sekund |
| Process | Pct privilegierad tid |
| Process | Pct-användartid |
| Process | Använt minne kBytes |
| Process | Virtuellt delat minne |
| Processor | % DPC-tid |
| Processor | Inaktivitetstid i procent |
| Processor | % avbrottstid |
| Processor | % väntetid i i/o |
| Processor | % trevlig tid |
| Processor | % privilegierad tid |
| Processor | % processortid |
| Processor | % användartid |
| System | Gratis fysiskt minne |
| System | Ledigt utrymme i växlingsfiler |
| System | Gratis virtuellt minne |
| System | Processer |
| System | Storlek lagras i växlingsfiler |
| System | Upptid |
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
Azure Monitor samlar in alla angivna prestandaräknare med deras angivna exempelintervall på alla agenter som har den räknaren installerad.  Data aggregeras inte och rådata är tillgängliga i alla loggfrågevyer under den tid som anges av din prenumeration.

## <a name="performance-record-properties"></a>Egenskaper för prestandapost
Prestandaposter har en typ av **Perf** och har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Dator som händelsen samlades in från. |
| CounterName (Motnamn) |Prestandaräknarens namn |
| Motväg |Fullständig sökväg för räknaren i \\ \\ \<formuläret \\Dator>\\objekt(instans) räknaren. |
| Motvärdering |Numeriskt värde för räknaren. |
| Instansnamn |Namn på händelseinstansen.  Tom om ingen instans. |
| ObjectName |Prestandaobjektets namn |
| SourceSystem |Typ av agent som data samlades in från. <br><br>OpsManager – Windows-agent, antingen direktanslutning eller SCOM <br> Linux – Alla Linux-agenter  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum och tid då data togs prov. |

## <a name="sizing-estimates"></a>Storleksuppskattningar
 En grov uppskattning för insamling av en viss räknare med 10-sekundersintervall är cirka 1 MB per dag per instans.  Du kan uppskatta lagringskraven för en viss räknare med följande formel.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Logga frågor med prestandaposter
Följande tabell innehåller olika exempel på loggfrågor som hämtar prestandaposter.

| Söka i data | Beskrivning |
|:--- |:--- |
| Prest |Alla prestandadata |
| Perf &#124; där dator == "MyComputer" |Alla prestandadata från en viss dator |
| Perf &#124; där CounterName == "Aktuell diskkölängd" |Alla prestandadata för en viss räknare |
| Perf &#124; där ObjectName == "Processor" och CounterName == "% ProcessorTid" och InstanceName == "_Total" &#124; sammanfatta AVGCPU = avg(CounterValue) efter dator |Genomsnittlig CPU-användning på alla datorer |
| Perf &#124; där CounterName == "% processortid" &#124; sammanfatta AggregatedValue = max(CounterValue) med dator |Maximal CPU-användning på alla datorer |
| Perf &#124; där ObjectName == "LogicalDisk" och CounterName == "Aktuell diskkölängd" och dator == "MyComputerName" &#124; sammanfattaggmängdsvärde = avg(CounterValue) efter InstanceName |Genomsnittlig aktuell diskkölängd för alla instanser av en viss dator |
| Perf &#124; där CounterName == "Disköverföringar/sek" &#124; sammanfatta AggregatedValue = percentil(CounterValue, 95) efter dator |95.e percentilen för disköverföringar/sek på alla datorer |
| Perf &#124; där CounterName == "% Processortid" och InstanceName == "_Total" &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats (TimeGenerated, 1h), Dator |Timgenomsnitt av CPU-användning på alla datorer |
| Perf &#124; där Dator == "MyComputer" och CounterName startswith_cs "%" och InstanceName == "_Total" &#124; sammanfatta AggregatedValue = percentil(CounterValue, 70) efter bin(TimeGenerated, 1h), CounterName | Timlön 70 percentil per % procenträknare för en viss dator |
| Perf &#124; där CounterName == "% processortid" och InstanceName == "_Total" och Dator == "MyComputer" &#124; sammanfatta ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentil75(CounterValue)"] = percentil(CounterValue, 75), ["max(CounterValue)"] = max (CounterValue) efter lager (TimeGenerated, 1h), Dator |Timgenomsnitt, minimum, maximum och 75-percentil CPU-användning för en viss dator |
| Perf &#124; där ObjectName == "MSSQL$INST2:Databases" och InstanceName == "master" | Alla prestandadata från databasprestandaobjektet för huvuddatabasen från den namngivna SQL Server-instansen INST2.  




## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare från Linux-program,](data-sources-linux-applications.md) inklusive MySQL och Apache HTTP Server.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.  
* Exportera insamlade data till [Power BI](powerbi.md) för ytterligare visualiseringar och analys.
