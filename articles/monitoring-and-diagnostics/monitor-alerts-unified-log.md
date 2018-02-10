---
title: "Loggen aviseringar i Azure-Monitor - aviseringar (förhandsversion) | Microsoft Docs"
description: "Aktivera e-postmeddelanden, meddelanden, anropa webbplatser URL: er (webhooks) eller automation när de komplicerade frågan du anger villkor för aviseringar i Azure (förhandsversion)."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: f6072e4e8a9ab72f677c35e498e31b5218579f1b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Loggen aviseringar i Azure-Monitor - aviseringar (förhandsgranskning)
Den här artikeln innehåller information om hur Varningsregler i Analytics-frågor fungerar i Azure varningar (förhandsversion) och beskrivs skillnaderna mellan olika typer av loggen Varningsregler.

För närvarande Azure aviseringar (förhandsversion) stöder Logga varningar på frågor från [Azure logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) och [Programinsikter](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> Loggen aviseringar i Azure aviseringar (förhandsversion) stöder för närvarande inte mellan arbetsytan eller mellan appar frågor.

## <a name="log-alert-rules"></a>Logga Varningsregler

Aviseringar skapas med Azure-aviseringar (förhandsgranskning) automatiskt köra loggen frågor med jämna mellanrum.  Om resultatet av logg-fråga matchar särskilda villkor, skapas en avisering post. Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddelar dig om aviseringen eller anropa en annan process som att köra runbooks, med [åtgärdsgrupper](monitoring-action-groups.md).  Olika typer av Varningsregler använda olika logik för att utföra den här analysen.

Varningsregler definieras av följande information:

- **Loggar frågan**.  Den fråga som körs varje gång regeln utlöses.  Poster som returneras av den här frågan används för att avgöra om en avisering skapas.
- **Tidsfönstret**.  Anger tidsintervallet för frågan.  Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden.  Tidsfönstret kan vara ett värde mellan 5 minuter och 1 440 minuter och 24 timmar. Om frågan körs klockan 13:15 tidsfönstret har angetts till 60 minuter, returneras endast de poster som skapats mellan 12:15:00 och 1:15 i Återställningsmappen.
- **Frekvensen**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tidsperioden.  Om värdet är större än tidsfönstret riskerar du poster som saknas.<br>Tänk dig ett tidsfönster på 30 minuter och en frekvens som 60 minuter.  Om frågan körs 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när återgår den poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig utvärderas.
- **Tröskelvärde för**.  Resultaten av loggen sökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för olika typer av Varningsregler.

Varje avisering regel i Log Analytics är en av två typer.  De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antalet resultat](#number-of-results-alert-rules)**. Avisering skapas när antalet poster som returneras av loggen sökningen överskrider ett angivet tal.
- **[Mått mätning](#metric-measurement-alert-rules)**.  Avisering som skapas för varje objekt i resultaten av loggen sökning med värden som överskrider angiven tröskel.

Skillnaderna mellan varningsregeln typer är som följer.

- **Antalet resultat** varningsregeln skapar alltid en enda avisering stund **mått mätning** varningsregeln skapar en avisering för varje objekt som överstiger tröskelvärdet.
- **Antalet resultat** Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. **Mått mätning** Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

## <a name="number-of-results-alert-rules"></a>Antalet resultat Varningsregler
**Antalet resultat** Varningsregler skapar en avisering när antalet poster som returneras av frågan överskrider det angivna tröskelvärdet.

**Tröskelvärde för**: tröskelvärdet för en **antalet resultat** varningsregeln är större än eller mindre än ett visst värde.  Om antalet poster som returneras av loggen sökningen matchar det här villkoret, skapas en avisering.

### <a name="scenarios"></a>Scenarier

#### <a name="events"></a>Händelser
Den här typen av regel för varning är idealisk för att arbeta med händelser, t.ex Windows-händelseloggar Syslog, och anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom ett visst tidsintervall.

Ange antalet resultat för att Avisera om en enskild händelse med större än 0 och både frekvens och tid till fem minuter.  Som kör frågan var fem minuter och Sök efter en enskild händelse som har skapats sedan den senaste gången frågan kördes.  En längre frekvens kan fördröja tiden mellan händelser som samlas in och den avisering som skapas.

Vissa program får logga in ett tillfälligt fel som inte nödvändigtvis rera en avisering.  Programmet kan till exempel gör processen som skapade felhändelsen och lyckas nästa gång.  I det här fallet kan du inte vill skapa en avisering om flera händelser skapas inom ett visst tidsintervall.  

I vissa fall kanske du vill skapa en avisering om en händelse.  En process kan till exempel logga regelbundna händelser som indikerar att den fungerar korrekt.  Om det inte logga en av dessa händelser inom ett visst tidsintervall, ska en avisering skapas.  I det här fallet kan du ange ett tröskelvärde **mindre än 1**.

## <a name="metric-measurement-alert-rules"></a>Mått mätning Varningsregler

**Mått mätning** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde.  De har olika följande skillnader från **antalet resultat** Varna regler.

**Mängdfunktion**: Anger beräkningen som utförs och kan vara ett numeriskt fält ska aggregeras.  Till exempel **count()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period.

> [!NOTE]

> Mängdfunktion i fråga måste vara namnet/kallas: AggregatedValue och ange ett numeriskt värde.


**Gruppera fältet**: en post med ett insamlat värde skapas för varje instans av det här fältet och en avisering genereras för varje.  Till exempel om du vill generera en avisering för varje dator du vill använda **per dator**   

> [!NOTE]

> Du kan ange fältet för att gruppera data för mått mätning Varningsregler som baseras på Application Insights. Det gör du genom att använda den **sammanställd på** alternativet i Regeldefinitionen.   

**Intervallet**: definierar det tidsintervall under vilken data sammanställs.  Till exempel om du har angett **fem minuter**, skapas en post för varje instans av fältet samman med 5 minuters intervall under tidsfönster som angetts för aviseringen.

**Tröskelvärde för**: tröskelvärdet för mått mätning Varningsregler definieras av ett samlat värde och ett antal intrång.  Om varje datapunkt i loggen sökningen överskrider detta värde, anses det har ett intrång.  Om antalet överträdelser i för alla objekt i resultaten överskrider det angivna värdet, skapas en avisering för objektet.

#### <a name="example"></a>Exempel
Föreställ dig ett scenario där du vill lägga till en avisering om alla datorer överskrids processoranvändning 90% tre gånger under 30 minuter.  Du kan skapa en aviseringsregel med följande information:  

**Fråga:** Perf | där ObjectName == ”-Processor” och CounterName == ”% processortid” | sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated, 5 m), datorn<br>
**Tidsfönstret:** 30 minuter<br>
**Varna frekvens:** fem minuter<br>
**Aggregera värde:** bra än 90<br>
**Utlösaren avisering baserat på:** totalt överträdelser som är större än 5<br>

Frågan skulle skapa ett genomsnittligt värde för varje dator med 5 minuters mellanrum.  Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna.  Exempeldata visas nedan för tre datorer.

![Exempel frågeresultat](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

I det här exemplet skulle separata aviseringar skapas för srv02 och srv03 eftersom de utsatts för intrång tröskelvärdet 90% 3 gånger under tidsperioden.  Om den **utlösaren avisering baserat på:** ändrades till **sidordning** sedan en avisering skapas endast för srv03 eftersom den utsatts för intrång tröskelvärdet för tre på varandra följande prover.


## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure aviseringar (förhandsgranskning)](monitoring-overview-unified-alerts.md)
* Lär dig mer om [med aviseringar i Azure (förhandsversion)](monitor-alerts-unified-usage.md)
* Lär dig mer om [logganalys](../log-analytics/log-analytics-overview.md).    
