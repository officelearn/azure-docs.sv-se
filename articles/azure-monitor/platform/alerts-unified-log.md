---
title: Logg aviseringar i Azure Monitor
description: Utlös e-post, meddelanden, anropa webb adresser för webbplatser (Webhooks) eller automatisering när de analytiska frågevillkor som du anger är uppfyllda för Azure-aviseringar.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: f78f7c37fafd7f0b29f76220206b9adfb62f52c9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677738"
---
# <a name="log-alerts-in-azure-monitor"></a>Logg aviseringar i Azure Monitor

Den här artikeln innehåller information om logg aviseringar är en av de typer av aviseringar som stöds i [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md) och låter användare använda Azures analys plattform som bas för aviseringar.

Logg aviseringen består av loggs öknings regler som skapats för [Azure Monitor loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Mer information om användningen finns i [Skapa logg aviseringar i Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Populära loggdata från [Azure Monitor-loggar](../../azure-monitor/learn/tutorial-viewdata.md) är nu även tillgängliga på mått plattformen i Azure Monitor. Mer information finns i [Metric-aviseringar för loggar](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Loggs ökning-aviserings regel – definition och typer

Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Contributor](../../azure-monitor/platform/roles-permissions-security.md) -rollen för att skapa, ändra och uppdatera logg aviseringar kan krävas. tillsammans med åtkomst & frågekörning för analys mål i varnings regeln eller varnings frågan. Om användaren som skapar inte har åtkomst till alla analys mål i varnings regeln eller varnings frågan, kan det hända att regeln inte skapas eller att logg aviserings regeln körs med partiella resultat.

Logg Sök regler definieras av följande information:

- **Logg fråga**.  Frågan som körs varje gång varnings regeln utlöses.  Posterna som returneras av den här frågan används för att avgöra om en avisering ska utlösas. Analytics-frågan kan vara avsedd för en speciell Log Analytics arbets yta eller Application Insights app och till och med sträcka sig över [flera Log Analytics och Application Insights resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) förutsatt att användaren har åtkomst och behörighet till alla resurser. 
    > [!IMPORTANT]
    > [frågor om kors resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) i logg aviseringar för Application Insights-och logg aviseringar för [Log Analytics som kon figurer ATS med scheduledQueryRules-API: et](../../azure-monitor/platform/alerts-log-api-switch.md) .

    Vissa analys kommandon och kombinationer är inkompatibla med användning i logg aviseringar. Mer information finns [i Logga varnings frågor i Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Tids period**.  Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tids perioden begränsar de data som hämtas för logg frågan för att förhindra missbruk och kringgår eventuella tids kommandon (t. ex. sedan) som används i logg frågan. <br>*For exempel, om tids perioden är inställd på 60 minuter och frågan körs med 1:15 PM, returneras endast poster som skapats mellan 12:15 PM och 1:15 PM för att köra logg frågor. Nu om logg frågan använder Time-kommandot som sedan kör (7d), skulle logg frågan endast köras för data mellan 12:15 och 1:15 PM, som om data bara finns för de senaste 60 minuterna. Och inte för sju dagars data som anges i logg frågan.*

- **Frekvens**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tids perioden.  Om värdet är större än tids perioden, riskerar du att poster saknas.<br>*For-exempel, överväg en tids period på 30 minuter och en frekvens på 60 minuter.  Om frågan körs vid 1:00 returneras poster mellan 12:30 och 1:00 PM.  Nästa gång frågan skulle köras är 2:00 när den skulle returnera poster mellan 1:30 och 2:00.  Poster som skapats mellan 1:00 och 1:30 utvärderas aldrig.*

- **Tröskel**.  Resultaten av loggs ökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet skiljer sig för de olika typerna av loggs öknings aviserings regler.

Logg Sök regler är den för [Azure Monitor loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)kan vara av två typer. Var och en av dessa typer beskrivs i detalj i de avsnitt som följer.

- **[Antal resultat](#number-of-results-alert-rules)** . En enda avisering skapas när antalet poster som returneras av loggs ökningen överskrider ett angivet tal.
- **[Mått mått](#metric-measurement-alert-rules)** .  Avisering som skapats för varje objekt i resultatet av loggs ökningen med värden som överskrider det angivna tröskelvärdet.

Skillnaderna mellan aviserings regel typer är följande.

- *Antal resultat* varnings regler skapar alltid en enda avisering, medan varnings regeln för *mått mätning* skapar en avisering för varje objekt som överskrider tröskelvärdet.
- *Antal resultat* varnings regler skapa en avisering när tröskelvärdet överskrids en gång. Varnings regler för *mått mätning* kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antal aviserings regler för resultat

**Antal resultat** varnings regler skapa en enda avisering när antalet poster som returneras av Sök frågan överskrider det angivna tröskelvärdet. Den här typen av varnings regel är idealisk för att arbeta med händelser som Windows-händelseloggar, syslog, WebApp-svar och anpassade loggar.  Du kanske vill skapa en avisering när en viss fel händelse skapas, eller när flera fel händelser skapas inom en viss tids period.

**Tröskelvärde**: Tröskelvärdet för ett antal resultat varnings regler är större än eller lika med ett visst värde.  Om antalet poster som returneras av loggs ökningen matchar det här kriteriet skapas en avisering.

Om du vill varna vid en enskild händelse anger du antalet resultat till större än 0 och söker efter förekomsten av en enskild händelse som skapades sedan frågan senast kördes. Vissa program kan logga ett tillfälligt fel som inte behöver utlösa en avisering.  Programmet kan till exempel försöka utföra processen som skapade fel händelsen och sedan lyckas nästa gång.  I det här fallet kanske du inte vill skapa en avisering om inte flera händelser skapas inom en viss tids period.  

I vissa fall kanske du vill skapa en avisering om en händelse saknas.  En process kan till exempel Logga vanliga händelser för att indikera att den fungerar som den ska.  Om den inte loggar en av dessa händelser inom en viss tids period, ska en avisering skapas.  I detta fall anger du tröskelvärdet till **mindre än 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exempel på antal poster typ logg avisering

Tänk dig ett scenario där du vill veta när den webbaserade appen ger svar på användare med kod 500 (det är) internt Server fel. Du skapar en varnings regel med följande information:  

- **Fråga:** begär Anden | där resultCode = = "500"<br>
- **Tids period:** 30 minuter<br>
- **Aviserings frekvens:** fem minuter<br>
- **Tröskel värde:** Större än 0<br>

Aviseringen kör sedan frågan var 5: e minut, med 30 minuters data, för att leta efter poster där resultat koden var 500. Om även en sådan post hittas utlöses aviseringen och den åtgärd som har kon figurer ATS utlöses.

### <a name="metric-measurement-alert-rules"></a>Aviserings regler för mått mätning

Varnings regler för **mått mätning** skapa en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde och angivet utlösnings villkor. Till skillnad från **antalet resultat** aviserings regler fungerar **mått mätnings** aviserings reglerna när Analytics-resultatet innehåller en tids serie. De har följande distinkta skillnader jämfört med **antalet resultat** varnings regler.

- **Mängd funktion**: Bestämmer beräkningen som utförs och eventuellt ett numeriskt fält som ska aggregeras.  **Count ()** returnerar till exempel antalet poster i frågan, **AVG (CounterValue)** returnerar medelvärdet för fältet CounterValue över intervallet. Mängd funktionen i frågan måste vara namngiven/anropad: AggregatedValue och ange ett numeriskt värde. 

- **Grupp fält**: En post med ett sammanställt värde skapas för varje instans av det här fältet och en avisering kan genereras för varje instans.  Om du till exempel vill generera en avisering för varje dator använder du **datorn**. Om det finns flera grupp fält angivna i varnings frågan, kan användaren ange vilket fält som ska användas för att sortera resultaten med hjälp av parametern **agg regerings på** (metricColumn)

    > [!NOTE]
    > Alternativet *aggregera på* (metricColumn) är tillgängligt för mått måtts typ logg aviseringar för Application Insights-och logg aviseringar för [Log Analytics som kon figurer ATS med scheduledQueryRules-API: et](../../azure-monitor/platform/alerts-log-api-switch.md) .

- **Intervall**:  Definierar det tidsintervall under vilket data aggregeras.  Om du till exempel har angett **fem minuter**skapas en post för varje instans av grupp fältet som sammanställs med 5 minuters intervall under den angivna tids perioden för aviseringen.

    > [!NOTE]
    > Bin-funktionen måste användas i Query för att ange intervall. Som bin () kan resultera i ojämna tidsintervaller – aviseringen kommer automatiskt att konvertera bin-kommandot till bin_at-kommandot med lämplig tid vid körning för att säkerställa resultat med en fast punkt. Mått mått typen för logg avisering är utformad för att fungera med frågor som har upp till tre instanser av bin ()-kommandot
    
- **Tröskelvärde**: Tröskelvärdet för varnings regler för mått mätningar definieras av ett sammanställt värde och ett antal överträdelser.  Om någon data punkt i loggs ökningen överstiger detta värde, betraktas den som en överträdelse.  Om antalet överträdelser av ett objekt i resultatet överskrider det angivna värdet, skapas en avisering för objektet.

Felaktig konfiguration av alternativet *aggregera på* eller *metricColumn* kan orsaka att varnings regler inaktive ras. Mer information finns i [fel sökning när varnings regeln för mått mått är felaktig](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exempel på mått för mått typ logg avisering

Tänk dig ett scenario där du ville ha en avisering om en dator har överskridit processor användningen på 90% tre gånger över 30 minuter.  Du skapar en varnings regel med följande information:  

- **Fråga:** Prestanda | där ObjectName = = "processor" och CounterName = = "% processor tid" | sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 5 m), dator<br>
- **Tids period:** 30 minuter<br>
- **Aviserings frekvens:** fem minuter<br>
- **Aviserings logik – villkor & tröskel:** Större än 90<br>
- **Grupp fält (mängd-på):** Computer
- **Utlös avisering baserat på:** Totalt antal överträdelser större än 2<br>

Frågan skulle skapa ett genomsnitts värde för varje dator med 5 minuters intervall.  Den här frågan körs var 5: e minut för data som samlas in under de senaste 30 minuterna. Eftersom det grupp fält (aggregerat) som valts är kolumn "dator", är AggregatedValue delat för olika värden för "dator" och den genomsnittliga processor belastningen för varje dator bestäms för en tid-behållare på 5 minuter.  Exempel frågans resultat för (t) tre datorer är som nedan.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Om frågeresultatet skulle ritas, visas det som.

![Exempel på frågeresultat](media/alerts-unified-log/metrics-measurement-sample-graph.png)

I det här exemplet ser vi på lager platser om 5 minuter för var och en av de tre datorerna – genomsnittlig processor användning som beräknas för 5 minuter. Tröskelvärdet på 90 blir överträtt av SRV01 endast en gång på 1:25 bin. I jämförelse är SRV02 överskrider 90-tröskeln på 1:10, 1:15 och 1:25 lager platser. medan srv03 överskrider 90 tröskelvärdet vid 1:10, 1:15, 1:20 och 1:30.
Eftersom aviseringen har kon figurer ATS för att utlösa baserat på totala överträdelser är fler än två, ser vi att SRV02 och srv03 endast uppfyller kriterierna. Därför skapas separata aviseringar för SRV02 och srv03 eftersom de orsakade tröskelvärdet på 90% två gånger över flera olika tidpunkter.  Om *Utlösar-aviseringen baserat på:* parametern har kon figurer ATS för alternativet för *kontinuerliga överträdelser* , skulle en avisering **endast** utlösas för srv03 eftersom den har överskridit tröskelvärdet under tre på varandra följande tidpunkts lager platser från 1:10 till 1:20. Och **inte** för SRV02, eftersom det strider mot tröskelvärdet för två på varandra följande tids lager platser från 1:10 till 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Loggs ökning, varnings regel – utlösare och tillstånd

Loggs öknings varnings regeln fungerar på den logik som predikat av användaren enligt konfigurationen och den anpassade analys frågan som används. Eftersom övervaknings logiken inklusive det exakta villkoret eller skälet till varför varnings regeln ska utlösas kapslas in i en Analytics-fråga – som kan skilja sig i varje logg aviserings regel. Azure-aviseringar innehåller information om det angivna underliggande begränsade (eller) scenariot som utvärderas när tröskel tillståndet för varnings regeln för loggs ökning är uppfyllt eller har överskridits. Därför kallas logg aviseringar som tillstånds lösa. Och loggar varnings regler kommer att behållas så länge som aviserings villkoret uppfylls genom resultatet av den anpassade analys frågan som tillhandahållits. Utan varningen var uppfylld, eftersom logiken för den exakta rotor Saks orsaken till övervaknings felet maskeras inuti den Analytics-fråga som anges av användaren. Det finns för närvarande ingen mekanism för Azure Monitor aviseringar för att på ett avgörande sätt härleda rotor saken att lösas.

Gör att vi kan se samma sak med ett praktiskt exempel. Anta att vi har en logg varnings regel som kallas *contoso-log-Alert*, enligt konfigurationen i [exemplet för antalet resultat typ logg avisering](#example-of-number-of-records-type-log-alert) – där den anpassade aviserings frågan är utformad för att söka efter 500 resultat kod i loggar.

- Kl. 1:05 när contoso-log-Alert kördes av Azure-aviseringar, gav logg Sök resultatet noll poster med resultat kod med 500. Eftersom noll är lägre än tröskelvärdet och aviseringen inte utlöses.
- Vid nästa upprepning vid 1:10 PM när contoso-log-Alert kördes av Azure-aviseringar har logg Sök resultatet fem poster med resultat koden 500. Eftersom fem överskrider tröskelvärdet och aviseringen utlöses med tillhör ande åtgärder utlöses.
- Kl. 1:15 när contoso-log-Alert kördes av Azure-aviseringar, har logg Sök resultatet två poster med 500 resultat kod. Eftersom två överskrider tröskelvärdet och aviseringen utlöses med tillhör ande åtgärder utlöses.
- Nu vid nästa upprepning vid 1:20 PM när contoso-log-Alert kördes av Azure-aviseringen har logg Sök resultatet angetts igen noll poster med 500 resultat kod. Eftersom noll är lägre än tröskelvärdet och aviseringen inte utlöses.

Men i ovanstående fall, vid 1:15 PM, kan Azure-aviseringar inte avgöra om de underliggande problemen visas vid 1:10 kvar och om det finns nya net New-fel. Eftersom en fråga som tillhandahålls av användaren kan ta hänsyn till tidigare poster – Azure-aviseringar kan vara säker. Eftersom logiken för aviseringen kapslas in i aviserings frågan – så de två posterna med 500 resultat kod som visas på 1:15 PM kan eller kanske inte redan ses på 1:10 PM. Till fel på varnings sidan när contoso-logg aviseringen körs vid 1:15 PM utlöses den konfigurerade åtgärden igen. Nu vid 1:20 PM när noll poster visas med 500 resultat kod – Azure-aviseringar kan inte vara säker på att orsaken till 500 resultat koden som visas på 1:10 PM och 1:15 PM nu har lösts och Azure Monitor aviseringar säkert kan härleda att problem med 500-fel inte inträffar av samma orsak s igen. Därför kommer contoso-log-aviseringen inte att ändras till löst i Azures aviserings instrument panel och/eller meddelanden som skickas med aviserings lösning. I stället kan användaren som förstår det exakta villkoret eller orsaken till att logiken är inbäddad i analys frågan [Markera aviseringen som stängd](alerts-managing-alert-states.md) vid behov.

## <a name="pricing-and-billing-of-log-alerts"></a>Priser och fakturering av logg aviseringar

Prissättningen som gäller för logg aviseringar anges på sidan för [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/) . I Azure-räkningar visas logg aviseringar som typen `microsoft.insights/scheduledqueryrules` med:

- Logg aviseringar på Application Insights visas med exakt varnings namn tillsammans med resurs grupps-och aviserings egenskaper
- Logga aviseringar på Log Analytics visas med ett exakt varnings namn tillsammans med resurs grupps-och aviserings egenskaper. När du skapar med [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

Det [äldre Log Analytics API: t](../../azure-monitor/platform/api-alerts.md) har varnings åtgärder och scheman som en del av Log Analytics sparade sökningar och inte rätt [Azure-resurser](../../azure-resource-manager/resource-group-overview.md). Det innebär att du kan aktivera fakturering för sådana äldre logg aviseringar som skapats för Log Analytics att använda Azure Portal **utan** [att växla till nytt API](../../azure-monitor/platform/alerts-log-api-switch.md) eller via [äldre Log Analytics API](../../azure-monitor/platform/api-alerts.md) – dolda pseudo-aviserings regler skapas på `microsoft.insights/scheduledqueryrules` för fakturering i Azure. De dolda egenskaperna för att skapa en dold pseudo-avisering för fakturering på `microsoft.insights/scheduledqueryrules` som visas som `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` tillsammans med resurs gruppens och aviserings egenskaper.

> [!NOTE]
> Om ogiltiga tecken, t. ex. `<, >, %, &, \, ?, /` finns, ersätts de med `_` i den dolda pseudo-aviserings regelns namn och därför också på Azure-fakturan.

För att ta bort de dolda scheduleQueryRules-resurser som har skapats för fakturering av aviserings regler med hjälp av [äldre Log Analytics-API](api-alerts.md)kan användaren göra något av följande:

- Antingen kan användaren [byta API-inställning för aviserings reglerna på Log Analytics arbets ytan](../../azure-monitor/platform/alerts-log-api-switch.md) och utan att förlora sina varnings regler eller övervaka över gången till Azure Resource Manager kompatibla [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Eliminerar därmed behovet av att göra dolda aviserings regler för att fakturera.
- Eller om användaren inte vill växla API-inställningen måste användaren **ta bort** det ursprungliga schemat och aviserings åtgärden med hjälp av [äldre Log Analytics-API](api-alerts.md) eller ta bort [Azure Portal den ursprungliga logg varnings regeln](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

För de dolda scheduleQueryRules-resurser som har skapats för fakturering av aviserings regler med hjälp av [äldre Log Analytics-API](api-alerts.md), kommer eventuella ändringar som sker som det inte går att utföra. Som `microsoft.insights/scheduledqueryrules`-typ av pseudo-regler används för fakturering av aviserings reglerna som skapats med [äldre Log Analytics API](api-alerts.md). Eventuella ändringar av varnings regeln bör utföras med hjälp av [äldre Log Analytics-API](api-alerts.md) (eller)-användare kan [byta API-inställning för aviserings reglerna](../../azure-monitor/platform/alerts-log-api-switch.md) för att använda [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [skapa i logg aviseringar i Azure](../../azure-monitor/platform/alerts-log.md).
* Förstå [webhookar i logg aviseringar i Azure](alerts-log-webhook.md).
* Lär dig mer om [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md).
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
* Läs mer om [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).