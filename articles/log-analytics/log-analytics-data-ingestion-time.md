---
title: Tid för inmatning av data i Azure Log Analytics | Microsoft Docs
description: Beskriver de olika faktorer som påverkar svarstid i insamling av data i Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: f40c8ed7eb6bfae958b3b57c4b7d525963ab9741
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955270"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Tid för inmatning av data i Log Analytics
Azure Log Analytics är en tjänst för hög skala i Azure Monitor som hanterar tusentals kunder skickar terabyte data varje månad i en växande takt. Det finns ofta frågor om den tid det tar innan data blir tillgängliga i Log Analytics när den har samlats in. Den här artikeln beskrivs de olika faktorer som påverkar den här fördröjningen.

## <a name="typical-latency"></a>Typisk svarstid
Svarstid refererar till den tid som data skapas på den övervakade datorn och den tid som det blir tillgängliga för analys i Log Analytics. Typisk svarstid mata in data i Log Analytics är mellan 2 och 5 minuter. Specifika svarstiden för specifika data varierar beroende på en mängd olika faktorer som beskrivs nedan.


## <a name="factors-affecting-latency"></a>Faktorer som påverkar svarstid
Totalt antal inmatning tiden för en viss uppsättning data kan delas upp i följande övergripande områden. 

- Agenttid - tiden för att upptäcka en händelse, samla in den och skicka den till Log Analytics inläsningspunkten som en loggpost. I de flesta fall är den här processen hanteras av en agent.
- Pipeline tid - format för pipelinen att bearbeta loggposten inmatning. Detta inkluderar parsning egenskaperna för händelsen och eventuellt att lägga till beräknade information.
- Indexering tid – den tid som krävs för att mata in data till Log Analytics Big Data store.

Information om olika svarstiden i den här processen beskrivs nedan.

### <a name="agent-collection-latency"></a>Agenten samling svarstid
Agenter och lösningar för hantering kan du använda olika strategier för att samla in data från en virtuell dator, vilket kan påverka svarstiden. Vissa specifika exempel är följande:

- Windows-händelser, syslog-händelser och prestandamått samlas omedelbart. Prestandaräknare för Linux avsökas med 30 sekunders mellanrum.
- IIS-loggar och egna loggar samlas in när tidsstämpel ändras. För IIS-loggar är detta påverkas av den [förnya schema som konfigurerats på IIS](log-analytics-data-sources-iis-logs.md). 
- Lösningen för Active Directory-replikering utför sin bedömning var femte dag, medan lösningen för Active Directory-utvärderingen utför en veckovis bedömning av Active Directory-infrastrukturen. Agenten samlar in dessa loggar endast när utvärderingen är klar.

### <a name="agent-upload-frequency"></a>Överföringsfrekvensen för agenten
För att säkerställa Log Analytics-agenten är enkel, agenten buffrar loggar och överför dem regelbundet till Log Analytics. Ladda upp frekvens varierar mellan 30 sekunder och 2 minuter beroende på vilken typ av data. De flesta data har överförts under 1 minut. Nätverksförhållanden kan negativt påverka svarstiden för dessa data för att nå inläsningspunkten för Log Analytics.

### <a name="azure-logs-and-metrics"></a>Azure loggar och mått 
Aktivitetsloggdata tar cirka 5 minuter ska bli tillgänglig i Log Analytics. Data från diagnostikloggar och mått kan det ta 1 – 5 minuter att bli tillgängliga, beroende på Azure-tjänsten. Sedan tar en ytterligare mellan 30 och 60 sekunder för loggar och 3 minuter för mått för data som ska skickas till Log Analytics inläsningspunkten.

### <a name="management-solutions-collection"></a>Hantering av lösningar samling
Vissa lösningar samlar inte in data från en agent och använder en samling-metod som medför en ytterligare fördröjning. Vissa lösningar samla in data med jämna mellanrum utan försök nästan i realtid samling. Specifika exempel är följande:

- Office 365-lösningen avsöker aktivitetsloggar med hjälp av Office 365 Management aktivitet API, som för närvarande inte ger någon nästan i realtid svarstidsgarantier.
- Windows Analytics-lösningar (till exempel Uppdateringsefterlevnad) data som samlas in av lösningen med en frekvens för dagliga.

I dokumentationen för varje lösning att fastställa dess insamlingsfrekvens.

### <a name="pipeline-process-time"></a>Pipeline-bearbetningstid
När loggposter är matas in i Log Analytics-pipeline, har de skrivits till ett tillfälligt lagringsutrymme så klientisolering och se till att data inte förloras. Den här processen lägger vanligtvis 5 till 15 sekunder. Vissa lösningar implementerar tyngre algoritmer för att samla in data och slutsatser som strömmar data i. Till exempel aggregerar prestanda nätverksövervakning inkommande data över 3 minuters intervall, ett effektivt sätt att lägga till 3-minuters fördröjning. En annan process som lägger till svarstiden är den process som hanterar anpassade loggar. Den här processen kan i vissa fall kan lägga till några minuter med svarstider till loggar som samlas in från filer av agenten.

### <a name="new-custom-data-types-provisioning"></a>Den nya anpassade datatyper etablering
När en ny typ av anpassade data har skapats från en [anpassad logg](../log-analytics/log-analytics-data-sources-custom-logs.md) eller [Data Collector API](../log-analytics/log-analytics-data-collector-api.md), systemet skapar en dedikerad lagringsbehållare. Det här är en enstaka kostnader som uppstår bara på de första tecknen på den här datatypen.

### <a name="surge-protection"></a>Ökning skydd
Högsta prioritet för Log Analytics är att säkerställa att ingen kunddata går förlorade, så att systemet har ett inbyggt skydd för data toppar. Detta inkluderar buffertar för att säkerställa att även under stora belastning systemet ska hålla fungerar. Under normal belastning kontrollerna lägga till mindre än en minut, men det är säkert i extrema förhållanden och fel som mycket tid samtidigt som data kan du lägga till.

### <a name="indexing-time"></a>Indexering tid
Det finns en inbyggd balans för varje stordataplattform mellan att tillhandahålla analys och avancerade sökfunktioner i stället för att ge omedelbar åtkomst till data. Azure Log Analytics kan du köra kraftfulla frågor på miljarder poster och hämta resultaten inom några sekunder. Detta är möjligt eftersom infrastrukturen som omvandlar data avsevärt under dess datainmatning och lagrar den i unika compact strukturer. Systemet buffrar data tills tillräckligt stor del av den är tillgänglig för att skapa dessa strukturer. Detta måste slutföras innan loggposten visas i sökresultaten.

Den här processen tar för närvarande cirka 5 minuter om det finns låg volym av data, men mindre tid för med en högre överföringshastighet. Det ser ut kontraintuitivt, men den här processen kan optimering av svarstid för produktionsarbetsbelastningar med hög volym.



## <a name="checking-ingestion-time"></a>Kontrollera inmatningen tid
Du kan använda den **pulsslag** tabell för att få en uppskattning av svarstid för data från agenter. Eftersom pulsslag skickas en gång en minut, skillnaden mellan aktuell tid och den sista posten för pulsslag ska helst vara så nära lite som möjligt.

Använd följande fråga listar du datorerna med den högsta svarstiden.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Använd följande fråga i stora miljöer sammanfatta svarstiden för olika procentandelar av totalt antal datorer.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Nästa steg
* Läs den [servicenivåavtal (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) för Log Analytics.

