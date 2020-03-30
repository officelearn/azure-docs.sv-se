---
title: Smart identifiering - felavvikelser i Application Insights | Microsoft-dokument
description: Varnar dig för ovanliga ändringar i hastigheten för misslyckade begäranden till din webbapp och tillhandahåller diagnostikanalys. Ingen konfiguration behövs.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: e1c07fca3a4eee19e56c313a889e5b86ce2b4c42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671757"
---
# <a name="smart-detection---failure-anomalies"></a>Smart identifiering - Felavvikelser
[Application Insights](../../azure-monitor/app/app-insights-overview.md) varnar dig automatiskt i nära realtid om din webbapp upplever en onormal ökning av antalet misslyckade begäranden. Den upptäcker en ovanlig ökning av antalet HTTP-begäranden eller beroendeanrop som rapporteras som misslyckade. För begäranden har misslyckade begäranden vanligtvis svarskoder på 400 eller högre. För att hjälpa dig att triage och diagnostisera problemet, en analys av egenskaperna hos fel och relaterade programdata finns i varningsinformationen. Det finns också länkar till Application Insights-portalen för ytterligare diagnos. Funktionen behöver ingen konfiguration eller konfiguration, eftersom den använder maskininlärningsalgoritmer för att förutsäga den normala felfrekvensen.

Den här funktionen fungerar för alla webbappar, som finns i molnet eller på dina egna servrar, som genererar programbegäran eller beroendedata. Om du till exempel har en arbetarroll som anropar [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) eller [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

När du har [konfigurerat Application Insights för ditt projekt](../../azure-monitor/app/app-insights-overview.md)och om appen genererar en viss minsta mängd data tar smart identifiering av felavvikelser 24 timmar att lära sig appens normala beteende innan den är påslagen och kan skicka aviseringar.

Här är en exempelavisering:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

Varningsinformationen kommer att berätta:

* Felfrekvensen jämfört med normalt appbeteende.
* Hur många användare påverkas - så du vet hur mycket du ska oroa dig.
* Ett karakteristiskt mönster som är associerat med felen. I det här exemplet finns det en viss svarskod, begärannamn (åtgärd) och programversion. Det berättar omedelbart var du ska börja leta i din kod. Andra möjligheter kan vara en specifik webbläsare eller klient operativsystem.
* Undantaget, loggspårningar och beroendefel (databaser eller andra externa komponenter) som verkar vara associerade med de karakteriserade felen.
* Länkar direkt till relevanta sökningar på data i Application Insights.

## <a name="benefits-of-smart-detection"></a>Fördelar med smart identifiering
Vanliga [måttaviseringar](../../azure-monitor/app/alerts.md) talar om för dig att det kan vara ett problem. Men Smart Detection startar det diagnostiska arbetet för dig, utför mycket den analys du annars skulle behöva göra själv. Du får resultaten prydligt förpackade, hjälper dig att snabbt komma till roten av problemet.

## <a name="how-it-works"></a>Hur det fungerar
Smart identifiering övervakar data som tas emot från din app, och i synnerhet felfrekvensen. Den här regeln räknar antalet `Successful request` begäranden som egenskapen är falsk för `Successful call` och antalet beroendeanrop som egenskapen är falsk till. För begäranden som `Successful request == (resultCode < 400)` standard (såvida du inte har skrivit anpassad kod för att [filtrera](../../azure-monitor/app/api-filtering-sampling.md#filtering) eller generera egna [TrackRequest-anrop).](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 

Appens prestanda har ett typiskt beteendemönster. Vissa begäranden eller beroendeanrop är mer benägna att misslyckas än andra. och den totala felfrekvensen kan öka när belastningen ökar. Smart identifiering använder maskininlärning för att hitta dessa avvikelser.

När data kommer till Application Insights från din webbapp jämför Smart Detection det aktuella beteendet med de mönster som har setts under de senaste dagarna. Om en onormal ökning av felfrekvensen observeras i jämförelse med tidigare prestanda utlöses en analys.

När en analys utlöses utför tjänsten en klusteranalys på den misslyckade begäran för att försöka identifiera ett mönster av värden som kännetecknar felen. 

I exemplet ovan har analysen upptäckt att de flesta fel handlar om en specifik resultatkod, begärannamn, server-URL-värd och rollinstans. 

När tjänsten är instrumenterad med dessa anrop söker analysatorn efter ett undantag och ett beroendefel som är associerade med begäranden i klustret som den har identifierat, tillsammans med ett exempel på eventuella spårningsloggar som är associerade med dessa begäranden.

Den resulterande analysen skickas till dig som avisering, såvida du inte har konfigurerat den till.

Precis som de [aviseringar du ställer in manuellt](../../azure-monitor/app/alerts.md)kan du kontrollera tillståndet för den avfyrade aviseringen, som kan lösas om problemet är åtgärdat. Konfigurera varningsreglerna på sidan Aviseringar på application insights-resursen. Men till skillnad från andra aviseringar behöver du inte konfigurera eller konfigurera smart identifiering. Om du vill kan du inaktivera den eller ändra dess mål-e-postadresser.

### <a name="alert-logic-details"></a>Information om varningslogik

Aviseringarna utlöses av vår egen maskininlärningsalgoritm så att vi inte kan dela de exakta implementeringsinformationerna. Med det sagt förstår vi att du ibland behöver veta mer om hur den underliggande logiken fungerar. De primära faktorer som utvärderas för att avgöra om en avisering ska utlösas är: 

* Analys av felprocenten för begäranden/beroenden i ett rullande tidsfönster på 20 minuter.
* En jämförelse av felprocenten för de senaste 20 minuterna och hastigheten under de senaste 40 minuterna och de senaste sju dagarna, och letar efter betydande avvikelser som överstiger X-gånger den standardavvikelsen.
* Använda en adaptiv gräns för den minsta felprocenten, som varierar beroende på appens volym av begäranden/beroenden.
* Det finns logik som automatiskt kan lösa det avfyrade varningsövervakningsvillkoret om problemet inte längre identifieras på 8-24 timmar.

## <a name="configure-alerts"></a>Konfigurera varningar

Du kan inaktivera regeln för smart identifieringsavisering från portalen eller använda Azure Resource Manager ([se mallexempel](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)).

Den här aviseringsregeln skapas med en associerad [åtgärdsgrupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) med namnet "Application Insights Smart Detection" som innehåller e-post- och webhook-åtgärder och kan utökas för att utlösa ytterligare åtgärder när aviseringen utlöses.

> [!NOTE]
> E-postmeddelanden som skickas från den här varningsregeln skickas nu som standard till användare som är associerade med prenumerationens roller övervakningsläsare och övervakningsdeltagare. Mer information om detta finns [här](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
> Meddelanden som skickas från den här varningsregeln följer det [gemensamma varningsschemat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Öppna sidan Aviseringar. Aviseringsregler för felavvikelser ingår tillsammans med alla aviseringar som du har angett manuellt och du kan se om den för närvarande är i aviseringstillståndet.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Klicka på aviseringen för att konfigurera den.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Observera att du kan inaktivera eller ta bort en aviseringsregel för felavvikelser, men du kan inte skapa en annan på samma Application Insights-resurs.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Exempel på felavvikelser varning webhook nyttolast

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Triage och diagnostisera en varning

En avisering indikerar att en onormal ökning av den misslyckade begäranden upptäcktes. Det är troligt att det finns vissa problem med din app eller dess miljö.

För att undersöka vidare, klicka på "Visa fullständig information i Application Insights" länkarna på denna sida tar dig direkt till en [söksida](../../azure-monitor/app/diagnostic-search.md) filtreras till relevanta förfrågningar, undantag, beroende eller spår. 

Du kan också öppna [Azure-portalen,](https://portal.azure.com)navigera till application insights-resursen för din app och öppna sidan Fel.

Genom att klicka på "Diagnostisera fel" hjälper dig att få mer information och lösa problemet.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

Från procentandelen begäranden och antalet användare som påverkas kan du bestämma hur brådskande problemet är. I exemplet ovan, felfrekvens på 78,5% kan jämföras med en normal hastighet på 2,2%, visar att något dåligt pågår. Å andra sidan var endast 46 användare drabbade. Om det var din app skulle du kunna bedöma hur allvarligt det är.

I många fall kan du snabbt diagnostisera problemet från begärandens namn, undantag, beroendefel och spårningsdata som tillhandahålls.

I det här exemplet fanns det ett undantag från SQL-databasen på grund av att begäransgränsen har nåtts.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Granska de senaste aviseringarna

Klicka på **Aviseringar** på sidan Application Insights-resurs för att komma åt de senaste avfyrade aviseringarna:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Vad är skillnaden ...
Smart identifiering av felavvikelser kompletterar andra liknande men distinkta funktioner i Application Insights.

* [Måttaviseringar](../../azure-monitor/app/alerts.md) ställs in av dig och kan övervaka ett brett spektrum av mått som CPU-beläggning, begärandehastigheter, sidinläsningstider och så vidare. Du kan använda dem för att varna dig, till exempel om du behöver lägga till fler resurser. Smart identifiering av felavvikelser omfattar däremot ett litet antal kritiska mått (för närvarande endast misslyckad begäranhetsfrekvens), som utformats för att meddela dig i nära realtid när webbappens misslyckade begäranhet ökar jämfört med webbappens normala beteende. Till skillnad från måttaviseringar anger smart identifiering automatiskt och uppdaterar tröskelvärden i svarsändringar i beteendet. Smart identifiering startar också diagnostikarbetet åt dig, vilket sparar tid för att lösa problem.

* [Smart identifiering av prestandaavvikelser](proactive-performance-diagnostics.md) använder också maskininformation för att identifiera ovanliga mönster i dina mått, och ingen konfiguration av dig krävs. Men till skillnad från Smart Detection av felavvikelser är syftet med smart identifiering av prestandaavvikelser att hitta segment av din användningsgrenrör som kan vara dåligt betjänta - till exempel av specifika sidor i en viss typ av webbläsare. Analysen utförs dagligen, och om något resultat hittas, är det sannolikt att vara mycket mindre brådskande än en varning. Analysen av felavvikelser utförs däremot kontinuerligt på inkommande programdata och du meddelas inom några minuter om serverns felfrekvens är större än förväntat.

## <a name="if-you-receive-a-smart-detection-alert"></a>Om du får en varning om smart identifiering
*Varför har jag fått den här varningen?*

* Vi upptäckte en onormal ökning av misslyckade begäranden ränta jämfört med den normala baslinjen för föregående period. Efter analys av fel och tillhörande programdata, tror vi att det finns ett problem som du bör undersöka.

*Betyder anmälan att jag definitivt har ett problem?*

* Vi försöker varna om appstörningar eller försämringar, men bara du kan förstå semantiken och effekten på appen eller användarna.

*Så du tittar på mina programdata?*

* Nej. Tjänsten är helt automatisk. Bara du får aviseringarna. Dina data är [privata](../../azure-monitor/app/data-retention-privacy.md).

*Måste jag prenumerera på den här aviseringen?*

* Nej. Varje program som skickar begärandedata har aviseringsregeln för smart identifiering.

*Kan jag avsluta prenumerationen eller få meddelandena skickade till mina kollegor i stället?*

* Ja, i varningsregler klickar du på regeln Smart identifiering för att konfigurera den. Du kan inaktivera aviseringen eller ändra mottagare för aviseringen.

*Jag förlorade e-postmeddelandet. Var hittar jag aviseringarna i portalen?*

* I aktivitetsloggarna. Öppna application insights-resursen för din app i Azure och välj sedan Aktivitetsloggar.

*Några av aviseringarna handlar om kända problem och jag vill inte ta emot dem.*

* Du kan använda funktionen för undertryckande av [varningsåtgärdsregler.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules)

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att granska data från din app:

* [Statistikutforskare](../../azure-monitor/app/metrics-explorer.md)
* [Sök explorer](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - kraftfullt frågespråk](../../azure-monitor/log-query/get-started-portal.md)

Smarta identifieringar är automatiska. Men du kanske vill sätta upp fler varningar?

* [Manuellt konfigurerade måttaviseringar](../../azure-monitor/app/alerts.md)
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
