---
title: Loggaviseringar i Azure Monitor
description: Utlösa e-postmeddelanden, meddelanden, url:er för samtalswebbplatser (webhooks) eller automatisering när de analytiska frågevillkor som du anger uppfylls för Azure-aviseringar.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665229"
---
# <a name="log-alerts-in-azure-monitor"></a>Loggaviseringar i Azure Monitor

Den här artikeln innehåller information om loggaviseringar är en av de typer av aviseringar som stöds i [Azure-aviseringarna](../../azure-monitor/platform/alerts-overview.md) och tillåter användare att använda Azures analysplattform som grund för aviseringar.

Loggvarning består av loggsökregler som skapats för [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Mer information om dess användning finns [i skapa loggaviseringar i Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Populära loggdata från [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) är nu också tillgängliga på måttplattformen i Azure Monitor. För mer informationsvy, [Måttavisering för loggar](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Loggsökvarningsregel - definition och typer

Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Contributor](../../azure-monitor/platform/roles-permissions-security.md) roll för att skapa, ändra och uppdatera loggaviseringar kan krävas. tillsammans med åtkomst & frågekörningsrättigheter för analysmålen i varningsregeln eller varningsfrågan. Om användaren som skapar inte har åtkomst till alla analytics-mål i varningsregel eller varningsfråga - kan regelskapandet misslyckas eller loggvarningsregeln köras med partiella resultat.

Loggsökregler definieras av följande information:

- **Loggfråga**.  Den fråga som körs varje gång varningsregeln utlöses.  De poster som returneras av den här frågan används för att avgöra om en avisering ska utlösas. Analytics-frågan kan vara för en specifik Log Analytics-arbetsyta eller Application Insights-app och till och med sträcker sig över [flera log analytics- och application insights-resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) förutsatt att användaren har åtkomst samt frågerättigheter till alla resurser. 
    > [!IMPORTANT]
    > [stöd](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) för fråga över flera resurser i loggaviseringar för Programstatistik och loggaviseringar för [Log Analytics som konfigurerats med hjälp av schemalagtQueryRules API.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Vissa analytiska kommandon och kombinationer är inkompatibla med användning i loggaviseringar. För mer informationsvy, [Loggaviseringsfrågor i Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Tidsperiod**.  Anger frågans tidsintervall. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tidsperioden begränsar de data som hämtas för loggfrågan för att förhindra missbruk och kringgår alla tidskommandon (som sedan) som används i loggfrågan. <br>*Om till exempel tidsperioden är inställd på 60 minuter och frågan körs klockan 13:15 returneras endast poster som skapats mellan 12:15 och 13:15 för att köra loggfrågan. Nu om loggfrågan använder tidskommandot som sedan (7d), skulle loggfrågan köras endast för data mellan 12:15 och 1:15 - som om data bara finns för de senaste 60 minuterna. Och inte för sju dagars data som anges i loggfrågan.*

- **Frekvens**.  Anger hur ofta frågan ska köras. Kan vara valfritt värde mellan 5 minuter och 24 timmar. Ska vara lika med eller mindre än tidsperioden.  Om värdet är större än tidsperioden riskerar du att poster missas.<br>*Tänk dig till exempel en tidsperiod på 30 minuter och en frekvens på 60 minuter.  Om frågan körs klockan 13:00 returneras poster mellan 12:30 och 13:00.  Nästa gång frågan skulle köras är 2:00 när den returnerar poster mellan 1:30 och 2:00.  Alla poster som skapas mellan 1:00 och 1:30 skulle aldrig utvärderas.*

- **Tröskelvärdet**.  Resultaten av loggsökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för de olika typerna av loggsökningsaviseringsregler.

Loggsökregler vara det för [Azure Monitor Loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller Application [Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), kan vara av två typer. Var och en av dessa typer beskrivs i detalj i de avsnitt som följer.

- **[Antal resultat](#number-of-results-alert-rules)**. En varning som skapas när de nummerposter som returneras av loggsökningen överskrider ett angivet antal.
- **[Måttmätning](#metric-measurement-alert-rules)**.  Avisering som skapats för varje objekt i resultatet av loggsökningen med värden som överskrider det angivna tröskelvärdet.

Skillnaderna mellan aviseringsregeltyper är följande.

- *Antal resultatvarningsregler* skapar alltid en enda avisering, medan måttmätningsaviseringsregeln skapar en avisering för varje objekt som överskrider tröskelvärdet. *Metric measurement*
- *Antal resultatvarningsregler* skapar en avisering när tröskelvärdet överskrids en enda gång. *Måttmätningsvarningsregler* kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antal regler för resultatvarning

**Antal resultatvarningsregler** skapar en enda avisering när antalet poster som returneras av sökfrågan överskrider det angivna tröskelvärdet. Den här typen av varningsregel är idealisk för att arbeta med händelser som Windows-händelseloggar, Syslog, WebApp Response och Anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom en viss tidsperiod.

**Tröskelvärde**: Tröskelvärdet för ett antal resultatvarningsregler är större än eller mindre än ett visst värde.  Om antalet poster som returneras av loggsökningen matchar det här villkoret skapas en avisering.

Om du vill avisera en enskild händelse anger du antalet resultat som större än 0 och söker efter förekomsten av en enskild händelse som skapades sedan frågan senast kördes. Vissa program kan logga ett tillfälligt fel som inte nödvändigtvis bör höja en avisering.  Programmet kan till exempel försöka igen processen som skapade felhändelsen och sedan lyckas nästa gång.  I det här fallet kanske du inte vill skapa aviseringen om inte flera händelser skapas inom en viss tidsperiod.  

I vissa fall kanske du vill skapa en avisering i avsaknad av en händelse.  En process kan till exempel logga vanliga händelser för att visa att den fungerar som den ska.  Om någon av dessa händelser inte loggas inom en viss tidsperiod ska en avisering skapas.  I det här fallet ställer du in tröskelvärdet till **mindre än 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exempel på loggavisering för typen Antal poster

Tänk dig ett scenario där du vill veta när din webbaserade app ger ett svar till användare med kod 500 (det vill säga) internt serverfel. Du skulle skapa en aviseringsregel med följande information:  

- **Fråga:** begäranden | där resultCode == "500"<br>
- **Tidsperiod:** 30 minuter<br>
- **Varningsfrekvens:** fem minuter<br>
- **Tröskelvärde:** Större än 0<br>

Sedan avisering skulle köra frågan var 5 minuter, med 30 minuter data - att leta efter någon post där resultatkoden var 500. Om en sådan post hittas utlöses aviseringen och utlöser åtgärden konfigurerad.

### <a name="metric-measurement-alert-rules"></a>Måttvarningsregler

**Måttmätningsaviseringsregler** skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde och angivet utlösarvillkor. Till skillnad från **Antal resultatvarningsregler** fungerar **måttmätningsaviseringsregler** när analysresultat ger en tidsserie. De har följande distinkta skillnader från **Antal resultatvarningsregler.**

- **Mängdfunktion**: Bestämmer den beräkning som utförs och eventuellt ett numeriskt fält att aggregera.  **Antal()** returnerar till exempel antalet poster i frågan, **avg(CounterValue)** returnerar medelvärdet av countervalue-fältet över intervallet. Mängdfunktionen i frågan måste namnges/anropas: AggregatedValue och ange ett numeriskt värde. 

- **Gruppfält:** En post med ett aggregerat värde skapas för varje instans av det här fältet och en avisering kan genereras för varje.  Om du till exempel vill generera en avisering för varje dator använder du **den via dator**. Om det finns flera gruppfält som anges i aviseringsfrågan kan användaren ange vilket fält som ska användas för att sortera resultat med **parametern Aggregate On** (metricColumn)

    > [!NOTE]
    > *Alternativet Aggregate On* (metricColumn) är tillgängligt för loggaviseringar för måttmätning för application insights och log alerts för [Log Analytics som konfigurerats med hjälp av scheduledQueryRules API.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Intervall**: Definierar det tidsintervall över vilket data aggregeras.  Om du till exempel angav **fem minuter**skapas en post för varje instans av gruppfältet som aggregeras med 5 minuters intervall under den tidsperiod som angetts för aviseringen.

    > [!NOTE]
    > Lagerplatsfunktionen måste användas i frågan för att ange intervall. Som bin() kan resultera i ojämlika tidsintervall - Alert konverterar automatiskt bin-kommandot till bin_at kommandot med lämplig tid vid körning, för att säkerställa resultat med en fast punkt. Måttmåttstypen loggavisering är utformad för att fungera med frågor med upp till tre instanser av kommandot bin()
    
- **Tröskelvärde**: Tröskelvärdet för måttmätningsvarningsregler definieras av ett aggregerat värde och ett antal överträdelser.  Om någon datapunkt i loggsökningen överskrider det här värdet betraktas det som ett brott.  Om antalet intrång för ett objekt i resultaten överskrider det angivna värdet skapas en avisering för det objektet.

Felkonfiguration av alternativet *Aggregate On* eller *metricColumn* kan orsaka feltändning av varningsregler. Mer information finns i [felsökning när måttmätningsvarningsregeln är felaktig](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exempel på logvarning för måttmätningstyp

Tänk dig ett scenario där du ville ha en avisering om någon dator överskred processoranvändningen på 90 % tre gånger under 30 minuter.  Du skulle skapa en aviseringsregel med följande information:  

- **Fråga:** Perf | där ObjectName == "Processor" och CounterName == "% processortid" | sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats (TimeGenerated, 5m), Dator<br>
- **Tidsperiod:** 30 minuter<br>
- **Varningsfrekvens:** fem minuter<br>
- **Varningslogik - villkor & tröskelvärde:** Större än 90<br>
- **Gruppfält (mängd-on):** Dator
- **Utlösarvarning baserad på:** Totala överträdelser större än 2<br>

Frågan skulle skapa ett genomsnittligt värde för varje dator med 5-minutersintervall.  Den här frågan skulle köras var femte minut för data som samlats in under de föregående 30 minuterna. Eftersom det gruppfält (Aggregate-on) som valts är columnar 'Computer' - den aggregeradevaluet delas för olika värden av "Dator" och genomsnittlig processorutnyttjande för varje dator bestäms för en tidsbehållare på 5 minuter.  Exempel på frågeresultat för (säg) tre datorer, skulle vara som nedan.


|TimeGenerated [UTC] |Dator  |Aggregeradvärde  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Om frågeresultatet skulle ritas, verkar det som.

![Exempel på frågeresultat](media/alerts-unified-log/metrics-measurement-sample-graph.png)

I det här exemplet ser vi i lagerplatser på 5 minuter för var och en av de tre datorerna - genomsnittlig processoranvändning som beräknas i 5 minuter. Tröskel på 90 som överträds av srv01 endast en gång på 1:25 bin. Som jämförelse överskrider srv02 tröskelvärdet 90 vid 1:10, 1:15 och 1:25 fack; medan srv03 överskrider 90 tröskel vid 1:10, 1:15, 1:20 och 1:30.
Eftersom aviseringen är konfigurerad för att utlösa baserat på totala överträdelser är mer än två, ser vi att srv02 och srv03 endast uppfyller kriterierna. Därför skulle separata aviseringar skapas för srv02 och srv03 eftersom de bröt mot tröskelvärdet på 90 % två gånger över flera tidslagerplatser.  Om *utlösaraviseringen baserat på:* parametern i stället konfigurerades för alternativet *Kontinuerliga överträdelser,* skulle en avisering **endast** aktiveras för srv03 eftersom den bröt tröskelvärdet för tre på varandra följande tidslagerplatser från 1:10 till 1:20. Och **inte** för srv02, eftersom det brutit tröskeln för två på varandra följande tid fack från 1:10 till 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Loggvarningsregel - bränning och tillstånd

Loggsökvarningsregler fungerar bara på den logik du skapar i frågan. Varningssystemet har ingen annan kontext för systemets tillstånd, din avsikt eller grundorsaken som frågan antyder. Därför kallas loggaviseringar för tillståndslös. Villkoren utvärderas som "SANT" eller "FALSKT" varje gång de körs.  En varning utlöses varje gång utvärderingen av varningsvillkoret är "SANT", oavsett att den avfyras tidigare.    

Låt oss se detta beteende i aktion med ett praktiskt exempel. Anta att vi har en loggvarningsregel som kallas *Contoso-Log-Alert*, som är konfigurerad enligt [exemplet för Loggavisering för antal resultattyp](#example-of-number-of-records-type-log-alert). Villkoret är en anpassad aviseringsfråga som är utformad för att leta efter 500 resultatkod i loggar. Om ytterligare en 500-resultatkoder hittas i loggarna gäller tillståndet för aviseringen. 

Vid varje intervall nedan utvärderar Azure-varningssystemet villkoret för *Contoso-Log-Alert*.


| Tid    | Antal poster som returneras av loggsökningsfråga | Utvärdering av loggvillkor | Resultat 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 poster | 0 är inte > 0 så FALSKT |  Varning avfyras inte. Inga åtgärder anropas.
| 13:10 | 2 poster | 2 > 0 så SANT  | Varningsbränder och åtgärdsgrupper anropade. VarningstillståndET AKTIVT.
| 13:15 | 5 poster | 5 > 0 så SANT  | Varningsbränder och åtgärdsgrupper anropade. VarningstillståndET AKTIVT.
| 13:20 | 0 poster | 0 är inte > 0 så FALSKT |  Varning avfyras inte. Inga åtgärder anropas. Varningstillståndet kvar aktivt.

Använda föregående ärende som exempel:

Vid 1:15 Azure-aviseringar kan inte avgöra om de underliggande problemen som visas vid 1:10 kvarstår och om posterna är netto nya fel eller upprepningar av äldre fel vid 1:10. Frågan som tillhandahålls av användaren kanske eller kanske inte tar hänsyn till tidigare poster och systemet vet inte. Azure-varningssystemet är byggt för att avlägga på sidan av försiktighet och utlöser aviseringen och tillhörande åtgärder igen klockan 13:15. 

Klockan 13:20 när noll poster visas med 500 resultatkod kan Azure-aviseringar inte vara säker på att orsaken till 500-resultatkoden som visas klockan 13:10 och 13:15 är nu löst. Det vet inte om 500 fel problem kommer att hända av samma skäl igen. Därför ändras inte *Contoso-Log-Alert* till **Löst** i Azure Alert-instrumentpanelen och/eller meddelanden skickas inte ut om att aviseringen är löst. Endast du, som förstår det exakta villkoret eller orsaken till logiken som är inbäddad i analysfrågan, kan [markera aviseringen som stängd](alerts-managing-alert-states.md) efter behov.

## <a name="pricing-and-billing-of-log-alerts"></a>Prissättning och fakturering av loggaviseringar

Priser som gäller för loggaviseringar anges på sidan [Azure Monitor-prissättning.](https://azure.microsoft.com/pricing/details/monitor/) I Azure-fakturor representeras `microsoft.insights/scheduledqueryrules` loggaviseringar som typ med:

- Loggaviseringar i application insights som visas med exakt aviseringsnamn tillsammans med resursgrupp och varningsegenskaper
- Loggaviseringar i Logganalys som visas med exakt aviseringsnamn tillsammans med resursgrupp och varningsegenskaper. när det skapas med [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

Det [äldre Log Analytics-API:et](../../azure-monitor/platform/api-alerts.md) har varningsåtgärder och scheman som en del av sparad sökning i Logganalys och inte korrekt [Azure-resurser](../../azure-resource-manager/management/overview.md). Därför för att aktivera fakturering för sådana äldre loggaviseringar som skapats för Log Analytics med hjälp av `microsoft.insights/scheduledqueryrules` Azure-portalen **utan** att byta till nytt [API](../../azure-monitor/platform/alerts-log-api-switch.md) eller via äldre Log Analytics [API](../../azure-monitor/platform/api-alerts.md) - dolda pseudovarningsregler skapas på för fakturering på Azure. De dolda pseudoaviseringsregler `microsoft.insights/scheduledqueryrules` som skapats för fakturering på som visas tillsammans `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` med resursgrupp och varningsegenskaper.

> [!NOTE]
> Om ogiltiga `<, >, %, &, \, ?, /` tecken som finns ersätts `_` de med i det dolda pseudoaviseringsregelnamnet och därmed även i Azure-fakturan.

Om du vill ta bort de dolda schemaknaventa resurser som skapats för fakturering av varningsregler med äldre [Log Analytics API](api-alerts.md)kan användaren göra något av följande:

- Antingen kan användaren [byta API-inställningar för varningsreglerna på log analytics-arbetsytan](../../azure-monitor/platform/alerts-log-api-switch.md) och utan förlust av sina varningsregler eller övervaka övergången till Azure Resource Manager-kompatibel [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Därmed eliminera behovet av att göra pseudo dolda varningsregler för fakturering.
- Eller om användaren inte vill byta API-inställningar måste användaren **ta bort** det ursprungliga schemat och aviseringsåtgärden med hjälp av äldre Log [Analytics API](api-alerts.md) eller ta bort den ursprungliga [loggaviseringsregeln i Azure-portalen](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Dessutom för dolda schemaQueryRules resurser som skapats för fakturering av aviseringsregler med hjälp av [äldre Log Analytics API](api-alerts.md), någon ändring åtgärd som PUT kommer att misslyckas. Eftersom `microsoft.insights/scheduledqueryrules` typ pseudo regler är i syfte att fakturera varningsregler som skapats med hjälp av [äldre Log Analytics API](api-alerts.md). Alla ändringar av varningsregeln bör göras med hjälp av [äldre Log Analytics API](api-alerts.md) (eller) användare kan byta [API-inställningar för varningsregler](../../azure-monitor/platform/alerts-log-api-switch.md) för att använda [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) istället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du skapar i loggaviseringar i Azure](../../azure-monitor/platform/alerts-log.md).
* Förstå [webhooks i loggaviseringar i Azure](alerts-log-webhook.md).
* Läs mer om [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md).
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
* Läs mer om [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
