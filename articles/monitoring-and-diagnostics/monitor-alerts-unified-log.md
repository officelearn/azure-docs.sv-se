---
title: Loggaviseringar i Azure Monitor
description: 'Utlösa e-postmeddelanden, meddelanden, anropa webbplatser URL: er (webhooks) eller automation när angivna analytisk fråga villkor är uppfyllda för Azure-aviseringar.'
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 49607809d6bb8697cc6b7a4063a45d893b9e1eac
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50421092"
---
# <a name="log-alerts-in-azure-monitor"></a>Loggaviseringar i Azure Monitor
Den här artikeln innehåller information om aviseringar är en av typerna av aviseringar som stöds i den [Azure Alerts](monitoring-overview-unified-alerts.md) och Tillåt användare att använda Azures analysplattform som bas för aviseringar.

Log aviseringen består av Loggsökning regler som har skapats för [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events). Läs mer om användningen i [skapar loggaviseringar i Azure](alert-log.md)

> [!NOTE]
> Populära loggdata från [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) finns nu även på plattformen mått i Azure Monitor. För information om vy [mått aviseringar för loggar](monitoring-metric-alerts-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Sök loggvarningsregel - definitions- och typer

Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](monitoring-action-groups.md). 

Regler för log search definieras av följande information:
- **Loggar frågor**.  Den fråga som körs varje gång regeln utlöses.  Poster som returneras av den här frågan används för att avgöra om en avisering skapas. Analytics-fråga kan även inkludera [schemaläggningskapacitet mellan program anrop](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), [mellan arbetsytan anrop och [mellan resurser anrop](../log-analytics/log-analytics-cross-workspace-search.md) förutsatt att användaren har behörighet att externa program. 

    > [!IMPORTANT]
    > Användaren måste ha [Azure Monitoring deltagare](monitoring-roles-permissions-security.md) roll för att skapa, ändra och uppdatera loggaviseringar i Azure Monitor, tillsammans med åtkomst & fråga körningsbehörigheter för analytics-mål i varningsregeln eller aviseringsfråga. Om användaren skapar inte har åtkomst till alla analytics mål i varningsregeln eller aviseringsfråga - regeln skapades kan misslyckas eller loggvarningsregel körs med ofullständiga resultat.

- **Tidsperiod**.  Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tidsperiod begränsar de data som hämtats för loggfråga att förhindra missbruk och kringgår alla tid-kommandon (t.ex. sedan) används i loggen frågan. <br>*Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, returneras endast de poster som har skapats mellan 12:15 PM och 1:15 log frågan. Om log frågan använder tid kommandot som sedan nu 7d, log frågan skulle köras endast för data mellan 12:15 PM och 1:15 – som om det finns data för endast de senaste 60 minuterna. Och inte för sju dagarnas data som anges i loggfråga.*
- **Frekvens**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Ska vara lika med eller mindre än tidsperioden.  Om värdet är större än hur lång tid, riskerar du poster som saknas.<br>*Anta exempelvis att en tidsperiod på 30 minuter och en frekvens på 60 minuter.  Om frågan körs i 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när den skulle returnera poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig ska utvärderas.*
- **Tröskelvärde för**.  Resultatet av loggsökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för olika typer av Varningsregler i log search.

Log search regler oavsett om det för [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), kan vara av två typer. De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antal resultat](#number-of-results-alert-rules)**. Avisering skapas när antalet poster som returneras av loggsökningen överskrider ett angivet tal.
- **[Metrisk måttenhet](#metric-measurement-alert-rules)**.  Varning skapad för varje objekt i resultatet av loggsökningen med värden som överskrider angivet tröskelvärde.

Skillnaderna mellan varningsregel typerna är som följer.

- *Antal resultat* Varningsregler skapar alltid en enda avisering, stund *metriska måttenheter* varningsregel skapar en avisering för varje objekt som är över tröskeln.
- *Antal resultat* Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. *Metrisk måttenhet* Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antal resultat Varningsregler
**Antal resultat** Varningsregler skapa en avisering när antalet poster som returneras av sökfrågan överskrider det angivna tröskelvärdet. Den här typen av varningsregel är perfekt för att arbeta med händelser, t.ex Windows-händelseloggar, Syslog, WebApp svar och anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom en viss tidsperiod.

**Tröskelvärde för**: tröskelvärde för ett antal resultat Varningsregler är större än eller mindre än ett visst värde.  Om antalet poster som returneras av loggsökningen matchar det här villkoret, skapas en avisering.

För att skicka en enskild händelse, ange hur många resultat till större än 0 och Sök efter förekomsten av en enskild händelse som har skapats sedan den senaste gången frågan kördes. Vissa program kan logga in med ett enstaka fel som inte nödvändigtvis genererar en avisering.  Programmet kan till exempel försök processen som skapade felhändelsen och lyckas nästa gång.  I det här fallet kan du inte vill skapa aviseringen om flera händelser skapas inom en viss tidsperiod.  

I vissa fall kan du skapa en avisering om en händelse.  En process kan exempelvis logga regelbundna händelser som indikerar att den fungerar korrekt.  Om det inte logga en av dessa händelser inom en viss tidsperiod, ska en avisering skapas.  I det här fallet kan du ange tröskelvärde för **mindre än 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exempel på antal poster typ log avisering
Tänk dig ett scenario där du vill veta när en webbaserad App får ett svar till användare med kod 500 (det vill säga) internt serverfel. Du skapar en aviseringsregel med följande uppgifter:  
- **Fråga:** begäranden | där Resultatkod == ”500”<br>
- **Tidsperiod:** 30 minuter<br>
- **Aviseringsfrekvens:** fem minuter<br>
- **Värdet för tröskelvärde:** större än 0<br>

Sedan skulle aviseringen köra frågan var femte minut, med 30 minuters data – att söka efter en post där Resultatkod var 500. Om med ett enda posten hittas, utlöses aviseringen och utlöser en åtgärd som har konfigurerats.

### <a name="metric-measurement-alert-rules"></a>Varningsregler för metriska måttenheter

- **Metrisk måttenhet** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde.  De har följande tydliga skillnader från **antal resultat** aviseringsregler.
- **Mängdfunktion**: avgör den beräkning som utförs och eventuellt ett numeriskt fält ska aggregeras.  Till exempel **antal()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period. Mängdfunktion i fråga måste vara med namnet/kallas: AggregatedValue och ange ett numeriskt värde. 
- **Gruppera fältet**: skapas en post med ett aggregerat värde för varje instans av det här fältet och en avisering genereras för varje.  Till exempel om du vill generera en avisering för varje dator använder du **per dator** 

    > [!NOTE]
    > Du kan ange fältet för att gruppera data för metriska måttenheter Varningsregler som baseras på Application Insights. Gör detta genom att använda den **sammanställda på** alternativet i Regeldefinitionen för.   
    
- **Intervall**: definierar det tidsintervall som aggregeras dina data.  Exempel: Om du har angett **fem minuter**, skapas en post för varje instans av gruppfältet samman med 5 minuters mellanrum under den tidsperiod som angetts för aviseringen.

    > [!NOTE]
    > Bin-funktionen måste användas i frågan för att ange intervall. Eftersom bin() kan leda till olika tidsintervall - omvandlas avisering automatiskt bin-kommando till bin_at kommandot med lämplig tidpunkt vid körning, för att säkerställa att resultaten med en fast tidpunkt. Metrisk måttenhet typen av avisering för log har utformats för att arbeta med frågor som har rapportanvändare bin() kommando
    
- **Tröskelvärde för**: tröskelvärde för metriska måttenheter Varningsregler definieras av ett samlat värde och ett antal intrång.  Om en datapunkt i loggsökningen överskrider detta värde, den betraktas som ett intrång.  Om antalet överträdelser i för alla objekt i resultatet överskrider det angivna värdet, skapas en avisering för objektet.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exempel på mått mätning typ log avisering
Tänk dig ett scenario där du vill ha en avisering om en dator har överskridits processoranvändning 90% tre gånger under 30 minuter.  Du skapar en aviseringsregel med följande uppgifter:  

- **Fråga:** Perf | där ObjectName == ”Processor” och CounterName == ”% processortid” | sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 5 m), dator<br>
- **Tidsperiod:** 30 minuter<br>
- **Aviseringsfrekvens:** fem minuter<br>
- **Aggregera värde:** överstiger 90<br>
- **Utlös aviseringen baserat på:** totalt dataintrång är större än 2<br>

Frågan skapar ett genomsnittligt värde för varje dator med 5 minuters mellanrum.  Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna.  Exempeldata visas nedan för tre datorer.

![Exemplet frågeresultat](media/monitor-alerts-unified-log/metrics-measurement-sample-graph.png)

I det här exemplet skulle separata aviseringar skapas för srv02 och srv03 eftersom de brott mot tröskelvärdet 90% tre gånger under tidsperioden.  Om den **Utlös aviseringen baserat på:** ändrades till **sidordning** och sedan skapas en avisering bara för srv03 eftersom den har brutit mot tröskelvärdet för tre på varandra följande prover.

## <a name="log-search-alert-rule---firing-and-state"></a>Sök loggvarningsregel - den utlösts och tillstånd
Sök aviseringsregeln fungerar på logiken vinstmöjligheter av användaren enligt konfiguration och anpassade analytics-fråga som används. Utlösare är inkapslad i en Analytics-fråga – som kan skilja sig i varje loggvarningsregel sedan logiken för exakta villkoret eller orsak varför varningsregeln ska. Azure-aviseringar har begränsade-information för den specifika underliggande grundorsaken i loggen resultaten när villkoret tröskelvärdet för log search varningsregel uppnåtts eller överskridits. Därför loggvarningar refereras till som tillstånd utan och utlöses varje gång resultatet är tillräckligt för att överstiga tröskelvärdet som angetts i loggaviseringar av *antal resultat* eller *metriska måttenheter* typ för villkoret. Och loggaviseringsregler kommer hela tiden hålla, så länge som aviseringstillståndet uppfylls av resultatet av anpassade analysfråga angav; utan aviseringen var komma löst. Som logik på den exakta orsaken till övervakning fel maskeras i analytics-frågan som angetts av användaren. Det finns inga innebär som vilka Azure-aviseringar definitivt härleda om resultatet uppfyller inte tröskelvärdet anger lösning av problemet.

Nu antar vi att vi har en loggvarningsregel som kallas *Contoso-Log-avisering*, enligt konfigurationen i den [exempel som angetts för många resultat typ log avisering](#example-of-number-of-records-type-log-alert). 
- Klockan 13:05 när Contoso-Log-avisering utfördes av Azure-aviseringar, gav resultatet från 0 poster; under tröskelvärdet och kan därför inte startas aviseringen. 
- I nästa iteration klockan 13:10 när Contoso-Log-avisering utfördes av Azure-aviseringar tillhandahålls resultatet från 5 posterna; som överskrider tröskeln och aktiveringen aviseringen strax efter genom att utlösa den [åtgärdsgrupp](monitoring-action-groups.md) som är associerade. 
- Klockan 13:15 när Contoso-Log-avisering utfördes av Azure-aviseringar, får resultatet 2 poster; som överskrider tröskeln och aktiveringen aviseringen strax efter genom att utlösa den [åtgärdsgrupp](monitoring-action-groups.md) som är associerade.
- Nu får resultatet i nästa iteration klockan 13:20 när Contoso-Log-aviseringen har utförts genom Azure avisering igen 0 poster; under tröskelvärdet och kan därför inte startas aviseringen.

Men ovan listade om klockan 13:15 - Azure-aviseringar kan inte fastställa att de underliggande problem som kan uppstå vid 1:10 kvarstår och om det finns net nya fel; Azure-aviseringar går inte att att som frågan som användaren kan hänsyn till tidigare poster –. Därför för att ta till en viss försiktighet, Contoso-Log-aviseringen skickas igen klockan 13:15 via konfigurerats [åtgärdsgrupp](monitoring-action-groups.md). Klockan 13:20 när inga poster ses: Azure-aviseringar får inte vara säker på att har nu orsaken posterna åtgärdats; Contoso-Log-aviseringen ska därför inte ändras till löst i aviseringen om Azure-instrumentpanel och/eller meddelanden som skickas ut om upplösning av avisering.


## <a name="pricing-and-billing-of-log-alerts"></a>Priser och fakturering av aviseringar
Priser för loggvarningar anges på den [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) sidan. I Azure-fakturor loggvarningar representeras som typ `microsoft.insights/scheduledqueryrules` med:
- Aviseringar i Application Insights visas med exakt aviseringsnamn tillsammans med resursgruppen och egenskaper för aviseringen
- Loggaviseringar på Log Analytics som visas med aviseringsnamn som `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` tillsammans med resursgruppen och egenskaper för aviseringen

    > [!NOTE]
    > Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste vara i gemener. Om ogiltiga tecken som `<, >, %, &, \, ?, /` är används – de kommer att ersättas med `_` på fakturan.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [skapa i loggaviseringar i Azure](alert-log.md).
* Förstå [webhooks i loggaviseringar i Azure](monitor-alerts-unified-log-webhook.md).
* Lär dig mer om [Azure-aviseringar](monitoring-overview-unified-alerts.md).
* Läs mer om [Application Insights](../application-insights/app-insights-analytics.md).
* Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md).    
