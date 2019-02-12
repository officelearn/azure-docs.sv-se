---
title: Logga tid för inmatning av data i Azure Monitor | Microsoft Docs
description: Beskriver de olika faktorer som påverkar svarstid i samla in loggdata i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: ba9a0ab775e062f21a058b537e289fe3ea2b40bb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000054"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Inloggningstid datainmatning i Azure Monitor
Azure Monitor är en hög skala tjänst som hanterar tusentals kunder skickar terabyte data varje månad i en växande takt. Det finns ofta frågor om den tid det tar för loggdata som ska bli tillgänglig när den har samlats in. Den här artikeln beskrivs de olika faktorer som påverkar den här fördröjningen.

## <a name="typical-latency"></a>Typisk svarstid
Svarstid refererar till den tid som data skapas på den övervakade datorn och den tid som det blir tillgängliga för analys i Azure Monitor. Typisk svarstid att mata in loggdata är mellan 2 och 5 minuter. Specifika svarstiden för specifika data varierar beroende på en mängd olika faktorer som beskrivs nedan.


## <a name="factors-affecting-latency"></a>Faktorer som påverkar svarstid
Totalt antal inmatning tiden för en viss uppsättning data kan delas upp i följande övergripande områden. 

- Agenttid - tiden för att upptäcka en händelse, samla in den och skicka den till Azure Monitor inläsningspunkten som en loggpost. I de flesta fall är den här processen hanteras av en agent.
- Pipeline tid - format för pipelinen att bearbeta loggposten inmatning. Detta inkluderar parsning egenskaperna för händelsen och eventuellt att lägga till beräknade information.
- Indexering tid – den tid som krävs för att mata in data i Azure Monitor stordataarkiv.

Information om olika svarstiden i den här processen beskrivs nedan.

### <a name="agent-collection-latency"></a>Agenten samling svarstid
Agenter och lösningar för hantering kan du använda olika strategier för att samla in data från en virtuell dator, vilket kan påverka svarstiden. Vissa specifika exempel är följande:

- Windows-händelser, syslog-händelser och prestandamått samlas omedelbart. Prestandaräknare för Linux avsökas med 30 sekunders mellanrum.
- IIS-loggar och egna loggar samlas in när tidsstämpel ändras. För IIS-loggar är detta påverkas av den [förnya schema som konfigurerats på IIS](data-sources-iis-logs.md). 
- Lösningen för Active Directory-replikering utför sin bedömning var femte dag, medan lösningen för Active Directory-utvärderingen utför en veckovis bedömning av Active Directory-infrastrukturen. Agenten samlar in dessa loggar endast när utvärderingen är klar.

### <a name="agent-upload-frequency"></a>Överföringsfrekvensen för agenten
För att säkerställa Log Analytics-agenten är enkel, agenten buffrar loggar och överför dem regelbundet till Azure Monitor. Ladda upp frekvens varierar mellan 30 sekunder och 2 minuter beroende på vilken typ av data. De flesta data har överförts under 1 minut. Nätverksförhållanden kan negativt påverka svarstiden för dessa data för att nå inläsningspunkten för Azure Monitor.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure-aktivitetsloggar, diagnostikloggar och mått
Azure data lägger till ytterligare tid ska bli tillgänglig i Log Analytics inläsningspunkten för bearbetning av:

- Data från diagnostikloggar tar 2 – 15 minuter, beroende på Azure-tjänsten. Se den [frågan nedan](#checking-ingestion-time) att undersöka den här fördröjningen i din miljö
- Azure-plattformen mått ta 3 minuter som ska skickas till Log Analytics inläsningspunkten.
- Aktivitetsloggdata tar cirka 10 – 15 minuter som ska skickas till Log Analytics inläsningspunkten.

När det är tillgängligt på inläsningspunkten, tar data ytterligare 2 – 5 minuter att vara tillgängliga för frågor.

### <a name="management-solutions-collection"></a>Hantering av lösningar samling
Vissa lösningar samlar inte in data från en agent och använder en samling-metod som medför en ytterligare fördröjning. Vissa lösningar samla in data med jämna mellanrum utan försök nästan i realtid samling. Specifika exempel är följande:

- Office 365-lösningen avsöker aktivitetsloggar med hjälp av Office 365 Management aktivitet API, som för närvarande inte ger någon nästan i realtid svarstidsgarantier.
- Windows Analytics-lösningar (till exempel Uppdateringsefterlevnad) data som samlas in av lösningen med en frekvens för dagliga.

I dokumentationen för varje lösning att fastställa dess insamlingsfrekvens.

### <a name="pipeline-process-time"></a>Pipeline-bearbetningstid
När loggposter är matas in i Azure Monitor-pipeline, har de skrivits till ett tillfälligt lagringsutrymme så klientisolering och se till att data inte förloras. Den här processen lägger vanligtvis 5 till 15 sekunder. Vissa lösningar implementerar tyngre algoritmer för att samla in data och slutsatser som strömmar data i. Till exempel aggregerar prestanda nätverksövervakning inkommande data över 3 minuters intervall, ett effektivt sätt att lägga till 3-minuters fördröjning. En annan process som lägger till svarstiden är den process som hanterar anpassade loggar. Den här processen kan i vissa fall kan lägga till några minuter med svarstider till loggar som samlas in från filer av agenten.

### <a name="new-custom-data-types-provisioning"></a>Den nya anpassade datatyper etablering
När en ny typ av anpassade data har skapats från en [anpassad logg](data-sources-custom-logs.md) eller [Data Collector API](data-collector-api.md), systemet skapar en dedikerad lagringsbehållare. Det här är en enstaka kostnader som uppstår bara på de första tecknen på den här datatypen.

### <a name="surge-protection"></a>Ökning skydd
Högsta prioritet för Azure Monitor är att säkerställa att ingen kunddata går förlorade, så att systemet har ett inbyggt skydd för data toppar. Detta inkluderar buffertar för att säkerställa att även under stora belastning systemet ska hålla fungerar. Under normal belastning kontrollerna lägga till mindre än en minut, men det är säkert i extrema förhållanden och fel som mycket tid samtidigt som data kan du lägga till.

### <a name="indexing-time"></a>Indexering tid
Det finns en inbyggd balans för varje stordataplattform mellan att tillhandahålla analys och avancerade sökfunktioner i stället för att ge omedelbar åtkomst till data. Azure Monitor kan du köra kraftfulla frågor på miljarder poster och hämta resultaten inom några sekunder. Detta är möjligt eftersom infrastrukturen som omvandlar data avsevärt under dess datainmatning och lagrar den i unika compact strukturer. Systemet buffrar data tills tillräckligt stor del av den är tillgänglig för att skapa dessa strukturer. Detta måste slutföras innan loggposten visas i sökresultaten.

Den här processen tar för närvarande cirka 5 minuter om det finns låg volym av data, men mindre tid för med en högre överföringshastighet. Det ser ut kontraintuitivt, men den här processen kan optimering av svarstid för produktionsarbetsbelastningar med hög volym.



## <a name="checking-ingestion-time"></a>Kontrollera inmatningen tid
Datainmatning tiden kan variera för olika resurser under olika omständigheter. Du kan använda loggfrågor för att identifiera specifika beteendet för din miljö.

### <a name="ingestion-latency-delays"></a>Datainmatning svarstid fördröjningar
Du kan mäta svarstiden för en viss post genom att jämföra resultatet av den [ingestion_time()](/azure/kusto/query/ingestiontimefunction) funktionen för att den _TimeGenerated_ fält. Dessa data kan användas med olika aggregeringar för att hitta hur datainmatningssvarstid fungerar. Granska vissa: e percentilen inmatning tid att hämta insikter för stora mängder data. 

Till exempel visar följande fråga du vilka datorer hade den högsta tiden för inmatning under den aktuella dagen: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Använd följande fråga där informationen även visualiseras i ett diagram om du vill öka detaljnivån för inmatning tiden för en specifik dator under en viss tidsperiod: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Använd följande fråga för att visa datortiden för inmatning av land de befinner sig i som baseras på deras IP-adress: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Olika datatyper från agenten kan ha olika inmatning svarstid tid så att de tidigare frågorna kan användas med andra typer. Använd följande fråga för att undersöka inmatning tidpunkten för olika Azure-tjänster: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Resurser som inte svarar 
I vissa fall kan en resurs sluta skicka data. För att förstå om en resurs skickar data eller inte, titta på dess senaste post som kan identifieras av standarden _TimeGenerated_ fält.  

Använd den _pulsslag_ tabell för att kontrollera tillgängligheten för en virtuell dator eftersom ett pulsslag skickas en gång i minuten av agenten. Använd följande fråga för att lista de aktiva datorer som inte har rapporterat pulsslag nyligen: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Nästa steg
* Läs den [servicenivåavtal (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) för Azure Monitor.

