---
title: Logga inmatningstid för data i Azure Monitor | Microsoft-dokument
description: Förklarar de olika faktorer som påverkar svarstiden vid insamling av loggdata i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666645"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Inmatningstid för loggdata i Azure Monitor
Azure Monitor är en storskalig datatjänst som betjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Det finns ofta frågor om hur tid det tar för loggdata att bli tillgängliga när de har samlats in. I den här artikeln beskrivs de olika faktorer som påverkar den här svarstiden.

## <a name="typical-latency"></a>Typisk latens
Svarstid refererar till den tid som data skapas på det övervakade systemet och den tid som den blir tillgänglig för analys i Azure Monitor. Den typiska svarstiden för att inta loggdata är mellan 2 och 5 minuter. Den specifika svarstiden för en viss data varierar beroende på en mängd olika faktorer som beskrivs nedan.


## <a name="factors-affecting-latency"></a>Faktorer som påverkar latens
Den totala inmatningstiden för en viss uppsättning data kan delas upp i följande områden på hög nivå. 

- Agenttid - Tiden för att identifiera en händelse, samla in den och sedan skicka den till Azure Monitor-inmatningspunkten som en loggpost. I de flesta fall hanteras den här processen av en agent.
- Pipeline-tid – Den tid det tar inmatnings-pipelinen att bearbeta loggposten. Detta inkluderar att tolka egenskaperna för händelsen och eventuellt lägga till beräknad information.
- Indexeringstid – Den tid som läggs på att inta en loggpost i Azure Monitor stordatalager.

Information om de olika svarstid som introduceras i den här processen beskrivs nedan.

### <a name="agent-collection-latency"></a>Svarstid för agentinsamling
Agenter och hanteringslösningar använder olika strategier för att samla in data från en virtuell dator, vilket kan påverka svarstiden. Några specifika exempel är följande:

- Windows-händelser, syslog-händelser och prestandamått samlas in omedelbart. Linux-prestandaräknare avsöks med 30 sekunders mellanrum.
- IIS-loggar och anpassade loggar samlas in när deras tidsstämpel ändras. För IIS-loggar påverkas detta av [det överrullningsschema som konfigurerats på IIS](data-sources-iis-logs.md). 
- Active Directory Replication-lösningen utför sin utvärdering var femte dag, medan Active Directory Assessment-lösningen utför en veckobedömning av Active Directory-infrastrukturen. Agenten samlar endast in dessa loggar när utvärderingen är klar.

### <a name="agent-upload-frequency"></a>Frekvens för agentuppladdning
För att säkerställa att Log Analytics-agenten är lätt buffrar agenten loggar och överför dem regelbundet till Azure Monitor. Uppladdningsfrekvensen varierar mellan 30 sekunder och 2 minuter beroende på vilken typ av data det är. De flesta data laddas upp på under 1 minut. Nätverksvillkor kan påverka svarstiden för dessa data negativt för att nå Azure Monitor-inmatningspunkten.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure-aktivitetsloggar, resursloggar och mått
Azure-data lägger till ytterligare tid för att bli tillgänglig vid log analytics-inmatningspunkten för bearbetning:

- Data från resursloggar tar 2-15 minuter, beroende på Azure-tjänsten. Se [frågan nedan](#checking-ingestion-time) för att undersöka den här svarstiden i din miljö
- Azure-plattformsmått tar 3 minuter att skicka till Log Analytics inmatningspunkt.
- Aktivitetsloggdata tar cirka 10-15 minuter att skickas till Log Analytics inmatningspunkt.

När data är tillgängliga vid inmatningspunkten tar det ytterligare 2-5 minuter att vara tillgängliga för frågor.

### <a name="management-solutions-collection"></a>Insamling av hanteringslösningar
Vissa lösningar samlar inte in sina data från en agent och kan använda en insamlingsmetod som introducerar ytterligare svarstid. Vissa lösningar samlar in data med jämna mellanrum utan att försöka samla in nästan i realtid. Specifika exempel är följande:

- Office 365-lösningssökningar av aktivitetsloggar med hjälp av API:et för Office 365-hanteringsaktivitet, som för närvarande inte ger några svarstidsgarantier i nära realtid.
- Windows Analytics-lösningar (uppdatera efterlevnad till exempel) samlas in av lösningen med en daglig frekvens.

Se dokumentationen för varje lösning för att fastställa dess insamlingsfrekvens.

### <a name="pipeline-process-time"></a>Tid för pipeline-process
När loggposter har förtärs in i Azure Monitor-pipelinen (som identifieras i [egenskapen _TimeReceived)](log-standard-properties.md#_timereceived) skrivs de till tillfällig lagring för att säkerställa klientisolering och se till att data inte går förlorade. Den här processen lägger vanligtvis till 5-15 sekunder. Vissa hanteringslösningar implementerar tyngre algoritmer för att aggregera data och härleda insikter när data strömmas in. Nätverksprestandaövervakning sammanställer till exempel inkommande data över 3-minutersintervall, vilket ger 3 minuters svarstid. En annan process som lägger till svarstid är den process som hanterar anpassade loggar. I vissa fall kan den här processen lägga till några minuters svarstid i loggar som samlas in från filer av agenten.

### <a name="new-custom-data-types-provisioning"></a>Nya anpassade datatyper etablering
När en ny typ av anpassade data skapas från en [anpassad logg](data-sources-custom-logs.md) eller Data [Collector API,](data-collector-api.md)skapar systemet en dedikerad lagringsbehållare. Detta är en engångsomkostnader som bara inträffar vid det första utseendet på den här datatypen.

### <a name="surge-protection"></a>Överspänningsskydd
Högsta prioritet för Azure Monitor är att se till att inga kunddata går förlorade, så att systemet har inbyggt skydd för dataöverspänningar. Detta inkluderar buffertar för att säkerställa att även under enorm belastning, kommer systemet att fortsätta fungera. Under normal belastning lägger dessa kontroller till mindre än en minut, men under extrema förhållanden och fel kan de lägga till betydande tid samtidigt som data är säkra.

### <a name="indexing-time"></a>Indexeringstid
Det finns en inbyggd balans för varje stordataplattform mellan att tillhandahålla analys och avancerade sökfunktioner i stället för att ge omedelbar åtkomst till data. Med Azure Monitor kan du köra kraftfulla frågor på miljarder poster och få resultat inom några sekunder. Detta är möjligt eftersom infrastrukturen omvandlar data dramatiskt under sitt intag och lagrar den i unika kompakta strukturer. Systemet buffrar data tills tillräckligt av det är tillgängligt för att skapa dessa strukturer. Detta måste slutföras innan loggposten visas i sökresultaten.

Den här processen tar för närvarande cirka 5 minuter när det finns låg mängd data men mindre tid vid högre datahastigheter. Detta verkar kontraintuitivt, men den här processen möjliggör optimering av svarstid för produktionsarbetsbelastningar med stora volymer.



## <a name="checking-ingestion-time"></a>Kontrollera inmatningstid
Intagstiden kan variera för olika resurser under olika omständigheter. Du kan använda loggfrågor för att identifiera specifika beteenden i din miljö. Följande tabell anger hur du kan bestämma de olika tiderna för en post när den skapas och skickas till Azure Monitor.

| Steg | Egenskap eller funktion | Kommentarer |
|:---|:---|:---|
| Post som skapats vid datakälla | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Om datakällan inte anger det här värdet ställs det in på samma tid som _TimeReceived. |
| Post som tas emot av slutpunkten för inmatning av Azure Monitor | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Spela in lagrad på arbetsytan och tillgänglig för frågor | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Fördröjningar för inmatningsfördröjningar
Du kan mäta svarstiden för en viss post genom att jämföra resultatet av funktionen [ingestion_time()](/azure/kusto/query/ingestiontimefunction) med egenskapen _TimeGenerated._ Dessa data kan användas med olika aggregeringar för att ta reda på hur inmatningsfördröjning fungerar. Undersök någon percentil av intagstiden för att få insikter för stora mängder data. 

I följande fråga visas till exempel vilka datorer som hade högst inmatningstid under de föregående 8 timmarna: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Föregående percentilkontroller är bra för att hitta allmänna trender i svarstid. Om du vill identifiera en kortsiktig ökning av`max()`svarstiden kan det vara mer effektivt att använda den maximala ( ).

Om du vill öka detaljnivån för inmatningstiden för en viss dator under en viss tidsperiod använder du följande fråga, som också visualiserar data från den senaste dagen i ett diagram: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Använd följande fråga för att visa datorns inmatningstid efter det land/den region de befinner sig i, som baseras på deras IP-adress: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Olika datatyper som kommer från agenten kan ha olika svarstid för inmatning, så tidigare frågor kan användas med andra typer. Använd följande fråga för att undersöka inmatningstiden för olika Azure-tjänster: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Resurser som slutar svara 
I vissa fall kan en resurs sluta skicka data. Om du vill förstå om en resurs skickar data eller inte, titta på den senaste posten som kan identifieras av _standardfältet TimeGenerated._  

Använd tabellen _Pulsslag_ för att kontrollera tillgängligheten för en virtuell dator, eftersom ett hjärtslag skickas en gång i minuten av agenten. Använd följande fråga för att visa de aktiva datorer som inte har rapporterat pulsslag nyligen: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Nästa steg
* Läs [servicenivåavtalet (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) för Azure Monitor.

