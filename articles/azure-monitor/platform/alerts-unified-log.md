---
title: Logg aviseringar i Azure Monitor
description: Utlös e-post, meddelanden, anropa webb adresser för webbplatser (Webhooks) eller automatisering när de analytiska frågevillkor som du anger är uppfyllda för Azure-aviseringar.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77665229"
---
# <a name="log-alerts-in-azure-monitor"></a>Logg aviseringar i Azure Monitor

Den här artikeln innehåller information om logg aviseringar är en av de typer av aviseringar som stöds i [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md) och låter användare använda Azures analys plattform som bas för aviseringar.

Logg aviseringen består av loggs öknings regler som skapats för [Azure Monitor loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Mer information om användningen finns i [Skapa logg aviseringar i Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Populära loggdata från [Azure Monitor-loggar](../../azure-monitor/learn/tutorial-viewdata.md) är nu även tillgängliga på mått plattformen i Azure Monitor. Mer information finns i [Metric-aviseringar för loggar](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Loggs ökning-aviserings regel – definition och typer

Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Contributor](../../azure-monitor/platform/roles-permissions-security.md) -rollen för att skapa, ändra och uppdatera logg aviseringar kan krävas. tillsammans med åtkomst & frågekörning för analys mål i varnings regeln eller varnings frågan. Om användaren som skapar inte har åtkomst till alla analys mål i varnings regeln eller varnings frågan, kan det hända att regeln inte skapas eller att logg aviserings regeln körs med partiella resultat.

Logg Sök regler definieras av följande information:

- **Logg fråga**.  Den fråga som körs varje gång varningsregeln utlöses.  Posterna som returneras av den här frågan används för att avgöra om en avisering ska utlösas. Analytics-frågan kan vara avsedd för en speciell Log Analytics arbets yta eller Application Insights app och till och med sträcka sig över [flera Log Analytics och Application Insights resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) förutsatt att användaren har åtkomst och behörighet till alla resurser. 
    > [!IMPORTANT]
    > [frågor om kors resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) i logg aviseringar för Application Insights-och logg aviseringar för [Log Analytics som kon figurer ATS med scheduledQueryRules-API: et](../../azure-monitor/platform/alerts-log-api-switch.md) .

    Vissa analys kommandon och kombinationer är inkompatibla med användning i logg aviseringar. Mer information finns [i Logga varnings frågor i Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Tids period**.  Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tids perioden begränsar de data som hämtas för logg frågan för att förhindra missbruk och kringgår eventuella tids kommandon (t. ex. sedan) som används i logg frågan. <br>*Om tids perioden till exempel är inställd på 60 minuter och frågan körs med 1:15 PM, returneras endast poster som skapats mellan 12:15 PM och 1:15 PM för att köra logg frågor. Nu om logg frågan använder Time-kommandot som sedan kör (7d), skulle logg frågan endast köras för data mellan 12:15 och 1:15 PM, som om data bara finns för de senaste 60 minuterna. Och inte för sju dagars data som anges i logg frågan.*

- **Frekvens**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tids perioden.  Om värdet är större än tids perioden, riskerar du att poster saknas.<br>*Ta till exempel en tids period på 30 minuter och en frekvens på 60 minuter.  Om frågan körs vid 1:00 returneras poster mellan 12:30 och 1:00 PM.  Nästa gång frågan skulle köras är 2:00 när den skulle returnera poster mellan 1:30 och 2:00.  Poster som skapats mellan 1:00 och 1:30 utvärderas aldrig.*

- **Tröskel**.  Resultaten av loggs ökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet skiljer sig för de olika typerna av loggs öknings aviserings regler.

Logg Sök regler är den för [Azure Monitor loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)kan vara av två typer. Var och en av dessa typer beskrivs i detalj i de avsnitt som följer.

- **[Antal resultat](#number-of-results-alert-rules)**. En enda avisering skapas när antalet poster som returneras av loggs ökningen överskrider ett angivet tal.
- **[Mått mått](#metric-measurement-alert-rules)**.  Avisering som skapats för varje objekt i resultatet av loggs ökningen med värden som överskrider det angivna tröskelvärdet.

Skillnaderna mellan aviserings regel typer är följande.

- *Antal resultat* varnings regler skapar alltid en enda avisering, medan varnings regeln för *mått mätning* skapar en avisering för varje objekt som överskrider tröskelvärdet.
- *Antal resultat* varnings regler skapa en avisering när tröskelvärdet överskrids en gång. Varnings regler för *mått mätning* kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antal aviserings regler för resultat

**Antal resultat** varnings regler skapa en enda avisering när antalet poster som returneras av Sök frågan överskrider det angivna tröskelvärdet. Den här typen av varnings regel är idealisk för att arbeta med händelser som Windows-händelseloggar, syslog, WebApp-svar och anpassade loggar.  Du kanske vill skapa en avisering när en viss fel händelse skapas, eller när flera fel händelser skapas inom en viss tids period.

**Tröskelvärde**: tröskelvärdet för ett antal resultat varnings regler är större än eller lika med ett visst värde.  Om antalet poster som returneras av loggs ökningen matchar det här kriteriet skapas en avisering.

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

- **Mängd funktion**: bestämmer beräkningen som utförs och eventuellt ett numeriskt fält som ska aggregeras.  **Count ()** returnerar till exempel antalet poster i frågan, **AVG (CounterValue)** returnerar medelvärdet för fältet CounterValue över intervallet. Mängd funktionen i frågan måste vara namngiven/anropad: AggregatedValue och ange ett numeriskt värde. 

- **Grupp fält**: en post med ett aggregerat värde skapas för varje instans av det här fältet och en avisering kan genereras för varje instans.  Om du till exempel vill generera en avisering för varje dator använder du **datorn**. Om det finns flera grupp fält angivna i varnings frågan, kan användaren ange vilket fält som ska användas för att sortera resultaten med hjälp av parametern **agg regerings på** (metricColumn)

    > [!NOTE]
    > Alternativet *aggregera på* (metricColumn) är tillgängligt för mått måtts typ logg aviseringar för Application Insights-och logg aviseringar för [Log Analytics som kon figurer ATS med scheduledQueryRules-API: et](../../azure-monitor/platform/alerts-log-api-switch.md) .

- **Interval**: definierar det tidsintervall under vilket data aggregeras.  Om du till exempel har angett **fem minuter**skapas en post för varje instans av grupp fältet som sammanställs med 5 minuters intervall under den angivna tids perioden för aviseringen.

    > [!NOTE]
    > Bin-funktionen måste användas i Query för att ange intervall. Som bin () kan resultera i ojämna tidsintervaller – aviseringen kommer automatiskt att konvertera bin-kommandot till bin_at kommando med lämplig tid vid körning, för att säkerställa resultat med en fast punkt. Mått mått typen för logg avisering är utformad för att fungera med frågor som har upp till tre instanser av bin ()-kommandot
    
- **Tröskel**: tröskelvärdet för varnings regler för mått mätning definieras av ett sammanställt värde och ett antal överträdelser.  Om någon data punkt i loggs ökningen överstiger detta värde, betraktas den som en överträdelse.  Om antalet överträdelser av ett objekt i resultatet överskrider det angivna värdet, skapas en avisering för objektet.

Felaktig konfiguration av alternativet *aggregera på* eller *metricColumn* kan orsaka att varnings regler inaktive ras. Mer information finns i [fel sökning när varnings regeln för mått mått är felaktig](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exempel på mått för mått typ logg avisering

Tänk dig ett scenario där du ville ha en avisering om en dator har överskridit processor användningen på 90% tre gånger över 30 minuter.  Du skapar en varnings regel med följande information:  

- **Fråga:** Prestanda | där ObjectName = = "processor" och CounterName = = "% processor tid" | sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 5 m), dator<br>
- **Tids period:** 30 minuter<br>
- **Aviserings frekvens:** fem minuter<br>
- **Aviserings logik – villkor & tröskel:** Större än 90<br>
- **Grupp fält (mängd-på):** Persondator
- **Utlös avisering baserat på:** Totalt antal överträdelser större än 2<br>

Frågan skulle skapa ett genomsnitts värde för varje dator med 5 minuters intervall.  Den här frågan körs var 5: e minut för data som samlas in under de senaste 30 minuterna. Eftersom det grupp fält (aggregerat) som valts är kolumn "dator", är AggregatedValue delat för olika värden för "dator" och den genomsnittliga processor belastningen för varje dator bestäms för en tid-behållare på 5 minuter.  Exempel frågans resultat för (t) tre datorer är som nedan.


|TimeGenerated [UTC] |Dator  |AggregatedValue  |
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

Loggs ökning varnings regler fungerar bara på den logik som du skapar i frågan. Varnings systemet har inte någon annan kontext för systemets tillstånd, din avsikt eller rotor saken underförstådda av frågan. Därför kallas logg aviseringar som tillstånds lösa. Villkoren utvärderas som "TRUE" eller "FALSe" varje gången de körs.  En avisering aktive ras varje stund som utvärderingen av aviserings villkoret är "sant", oavsett om den har utlösts tidigare.    

Nu ska vi se hur det fungerar i praktiken med ett praktiskt exempel. Anta att vi har en logg varnings regel som heter *contoso-log-Alert*, som är konfigurerad som i [exemplet för antalet resultat typ logg avisering](#example-of-number-of-records-type-log-alert). Villkoret är en anpassad aviserings fråga som har utformats för att söka efter 500 resultat kod i loggar. Om det finns mer än en resultat kod för 500 i loggarna, är villkoret för aviseringen sant. 

I varje intervall nedan utvärderar Azure-aviseringar villkoret för *contoso-logg-aviseringen*.


| Tid    | Antal poster som returneras av logg Sök frågan | Evalution för logg villkor | Resultat 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0 poster | 0 är inte > 0 så falskt |  Aviseringen utlöses inte. Inga åtgärder har anropats.
| 1:10 PM | 2 poster | 2 > 0 så sant  | Aviserings Utlös och åtgärds grupper anropas. Aviserings tillstånd aktivt.
| 1:15 PM | 5 poster | 5 > 0 så sant  | Aviserings Utlös och åtgärds grupper anropas. Aviserings tillstånd aktivt.
| 1:20 PM | 0 poster | 0 är inte > 0 så falskt |  Aviseringen utlöses inte. Inga åtgärder har anropats. Aviserings tillstånd kvar aktivt.

Använd föregående fall som exempel:

Vid 1:15 PM kan Azure-aviseringar inte avgöra om de underliggande problemen visas vid 1:10 kvar och om posterna är net New-Failure eller upprepade fel vid 1:10PM. Frågan som tillhandahålls av användaren kanske inte tar hänsyn till tidigare poster och systemet känner inte igen. Azures aviserings system är skapat för fel på varnings sidan och utlöser aviseringen och de associerade åtgärderna på 1:15 PM. 

Vid 1:20 PM när noll poster visas med 500 resultat kod kan Azure-aviseringar inte vara säker på att orsak till 500 resultat kod som visas på 1:10 PM och 1:15 PM nu har lösts. Det vet inte om 500-fel problemen inträffar av samma orsaker igen. Det innebär att *contoso-log-Alert* inte ändras till **löst** i Azures aviserings instrument panel och/eller att meddelanden inte skickas ut som anger att aviseringen har lösts. Endast du, som förstår det exakta villkoret eller orsaken till att logiken är inbäddad i analys frågan, kan [Markera aviseringen som stängd](alerts-managing-alert-states.md) vid behov.

## <a name="pricing-and-billing-of-log-alerts"></a>Priser och fakturering av logg aviseringar

Prissättningen som gäller för logg aviseringar anges på sidan för [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/) . I Azure-räkningar visas logg aviseringar som typ `microsoft.insights/scheduledqueryrules` med:

- Logg aviseringar på Application Insights visas med exakt varnings namn tillsammans med resurs grupps-och aviserings egenskaper
- Logga aviseringar på Log Analytics visas med ett exakt varnings namn tillsammans med resurs grupps-och aviserings egenskaper. När du skapar med [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

Det [äldre Log Analytics API: t](../../azure-monitor/platform/api-alerts.md) har varnings åtgärder och scheman som en del av Log Analytics sparade sökningar och inte rätt [Azure-resurser](../../azure-resource-manager/management/overview.md). Det innebär att du kan aktivera fakturering för sådana äldre logg aviseringar som skapats för Log Analytics att använda Azure Portal **utan** [att växla till nytt API](../../azure-monitor/platform/alerts-log-api-switch.md) eller via [äldre Log Analytics API](../../azure-monitor/platform/api-alerts.md) – dolda pseudo `microsoft.insights/scheduledqueryrules` -aviserings regler skapas på för fakturering i Azure. De dolda egenskaperna för att skapa en dold pseudo- `microsoft.insights/scheduledqueryrules` avisering för fakturering `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` på som visas tillsammans med resurs gruppens och aviserings egenskaper.

> [!NOTE]
> Om ogiltiga tecken, t `<, >, %, &, \, ?, /` . ex. finns, ersätts de `_` med i namnet på den dolda pseudo-aviserings regeln och därför också på Azure-fakturan.

För att ta bort de dolda scheduleQueryRules-resurser som har skapats för fakturering av aviserings regler med hjälp av [äldre Log Analytics-API](api-alerts.md)kan användaren göra något av följande:

- Antingen kan användaren [byta API-inställning för aviserings reglerna på Log Analytics arbets ytan](../../azure-monitor/platform/alerts-log-api-switch.md) och utan att förlora sina varnings regler eller övervaka över gången till Azure Resource Manager kompatibla [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Eliminerar därmed behovet av att göra dolda aviserings regler för att fakturera.
- Eller om användaren inte vill växla API-inställningen måste användaren **ta bort** det ursprungliga schemat och aviserings åtgärden med hjälp av [äldre Log Analytics-API](api-alerts.md) eller ta bort [Azure Portal den ursprungliga logg varnings regeln](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

För de dolda scheduleQueryRules-resurser som har skapats för fakturering av aviserings regler med hjälp av [äldre Log Analytics-API](api-alerts.md), kommer eventuella ändringar som sker som det inte går att utföra. Som `microsoft.insights/scheduledqueryrules` typ av pseudo-regler är det syftet att fakturera aviserings reglerna som skapats med [äldre Log Analytics-API](api-alerts.md). Eventuella ändringar av varnings regeln bör utföras med hjälp av [äldre Log Analytics-API](api-alerts.md) (eller)-användare kan [byta API-inställning för aviserings reglerna](../../azure-monitor/platform/alerts-log-api-switch.md) för att använda [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [skapa i logg aviseringar i Azure](../../azure-monitor/platform/alerts-log.md).
* Förstå [webhookar i logg aviseringar i Azure](alerts-log-webhook.md).
* Lär dig mer om [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md).
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
* Läs mer om [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
