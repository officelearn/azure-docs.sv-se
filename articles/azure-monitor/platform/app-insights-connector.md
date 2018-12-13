---
title: Visa Azure Application Insights AppData | Microsoft Docs
description: Du kan använda lösningen för Application Insights-anslutningsprogram för att diagnostisera prestandaproblem och förstå vad användarna gör med din app när övervakas med Application Insights.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: magoedte
ms.openlocfilehash: 8c601bdd7fbba536aa5b8a4d1965f4ef2bb906f9
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187859"
---
# <a name="application-insights-connector-management-solution-preview"></a>Lösning för Application Insights-Anslutningsapp (förhandsversion)

![Application Insights symbol](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Med hjälp av [mellan resurser frågor](../../azure-monitor/log-query/cross-workspace-query.md), hanteringslösningen för Application Insights-anslutning krävs inte längre och upphör att gälla. Från och juli, kommer du inte att kunna länka ny Application Insights-resurser till Log Analytics-arbetsytor. Befintliga länkar och instrumentpaneler kommer fortsätta att fungera tills November 2018. Mer information finns i [OMS-portalen som flyttar till Azure](../../azure-monitor/platform/oms-portal-transition.md).

Program Insights Connector-lösningen hjälper dig att diagnostisera prestandaproblem och förstå vad användarna gör med din app när den är övervakad med [Application Insights](../../application-insights/app-insights-overview.md). Vyer för samma programtelemetri som utvecklare kan se i Application Insights är tillgängliga i Log Analytics. När du integrerar dina Application Insights-appar med Log Analytics, ökas visningen av dina program genom att använda åtgärden och programdata på samma ställe. Att ha samma vyer hjälper dig att samarbeta med dina apputvecklare. Vanliga vyer kan du minska tiden för att identifiera och lösa både programmet och plattformsproblem.

När du använder lösningen kan du:

- Visa alla Application Insights-appar på samma plats, även om de finns i olika Azure-prenumerationer
- Korrelera infrastrukturdata med programdata
- Visualisera programdata med perspektiv i loggsökning
- Växla över från Log Analytics-data till din Application Insights-app i Azure portal

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra Log Analytics-lösningar, inte som samlas in för Application Insights-anslutningsprogram av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanteringsgrupp](../../azure-monitor/platform/om-agents.md) | Nej | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure Storage-konto](collect-azure-metrics-logs.md) | Nej | Lösningen gör inte samlingsinformation från Azure storage. |

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration för att komma åt information för Application Insights-anslutningsprogram
- Du måste ha minst en konfigurerade Application Insights-resurs.
- Du måste vara ägare eller deltagare i Application Insights-resursen.

## <a name="configuration"></a>Konfiguration

1. Aktivera Azure Web Apps Analytics-lösningen från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) eller genom att använda processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Bläddra till [Azure-portalen](https://portal.azure.com). Välj **alla tjänster** att öppna Application Insights. Sök sedan efter Application Insights. 
3. Under **prenumerationer**, Välj en prenumeration med Application Insights-resurser och sedan under **namn**, Välj ett eller flera program.
4. Klicka på **Spara**.

Data blir tillgängliga i cirka 30 minuter och Application Insights-panelen uppdateras med data, som på följande bild:

![Application Insights-panelen](./media/app-insights-connector/app-insights-tile.png)

Andra saker att tänka på:

- Du kan bara koppla Application Insights-appar till en Log Analytics-arbetsyta.
- Du kan bara koppla [Basic eller Enterprise Application Insights-resurser](https://azure.microsoft.com/pricing/details/application-insights) till Log Analytics. Du kan dock använda den kostnadsfria nivån av Log Analytics.

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inte alla hanteringspaket i anslutna hanteringsgrupper.

## <a name="use-the-solution"></a>Använd lösningen

I följande avsnitt beskrivs hur du kan använda bladen visas i instrumentpanelen med Application Insights för att visa och interagera med data från dina appar.

### <a name="view-application-insights-connector-information"></a>Visa information om Application Insights-anslutningsprogram

Klicka på den **Application Insights** panelen för att öppna den **Application Insights** instrumentpanelen för att se följande blad.

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash02.png)

Instrumentpanelen innehåller blad visas i tabellen. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster när du klickar på **se alla** längst ned på bladet eller när du klickar på maximeras.


| **Kolumn** | **Beskrivning** |
| --- | --- |
| Program - antal program | Visar antalet program i programresurser. Visar också programnamn och för varje antalet programposter. Klicka på siffran för att köra en loggsökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klicka på namnet på ett program att köra en loggsökning som visar programposter per värd, poster per telemetrityp och alla data per typ (baserat på den sista dagen). |
| Datavolym – värdar som skickar data | Visar antalet datorn värdar som skickar data. Visas även datorn är värd och antal poster för varje värd. Klicka på siffran för att köra en loggsökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klicka på ett datornamn att köra en loggsökning för värden som visar programposter per värd, poster per telemetrityp och alla data per typ (baserat på den sista dagen). |
| Tillgänglighet – webbtestresultat | Visar ett ringdiagram för test av Webbresultat, som anger pass eller misslyckas. Klicka på diagrammet för att köra en loggsökning för <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Resultatet visar antalet pass och fel för alla test. Den visar alla Web Apps med trafik för den senaste minuten. Klicka på namnet på ett program att visa en loggsökning som visar information om misslyckade webbtester. |
| Serverbegäranden – förfrågningar per timme | Visar ett linjediagram med serverbegäranden per timme för olika program. Hovra över en rad i diagrammet för att ta emot begäranden för en punkt i tiden topp 3 programmen. Visar även en lista över program som tar emot begäranden och antal begäranden för den valda perioden. <br><br>Klicka på diagrammet för att köra en loggsökning för <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar en mer detaljerad linjediagram med serverbegäranden per timme för olika program. <br><br> Klicka på ett program i listan för att köra en loggsökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> som visar en lista över begäranden, diagram för begäranden över tid och begäran varaktighet och en lista över begäran svarskoder.   |
| Fel – misslyckade förfrågningar per timme | Visar ett linjediagram med misslyckade programbegäranden per timme. Hovra över diagrammet för att se de 3 främsta programmen med misslyckade begäranden för en punkt i tiden. Visar även en lista över program med antalet misslyckade förfrågningar för var och en. Klicka på diagrammet för att köra en loggsökning för <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar en mer detaljerad linjediagram med misslyckade programbegäranden. <br><br>Klicka på ett objekt i listan för att köra en loggsökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> som visar misslyckade begäranden, diagram för misslyckade förfrågningar över tid och begäran varaktighet och en lista över misslyckade begäranden svarskoder. |
| Undantag – undantag per timme | Visar ett linjediagram med undantag per timme. Hovra över diagrammet för att se de 3 främsta programmen med undantag för en punkt i tiden. Visar även en lista över program med antalet undantag för var och en. Klicka på diagrammet för att köra en loggsökning för <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar en mer detaljerad länk diagram över undantag. <br><br>Klicka på ett objekt i listan för att köra en loggsökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> som visar en lista över undantag, diagram för undantag över tid och misslyckade begäranden och en lista över undantagstyper.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visa Application Insights-perspektiv med loggsökning

När du klickar på ett objekt i instrumentpanelen kan du se en Application Insights-perspektiv som visas i sökningen. Perspektivet innehåller en utökad visualisering, beroende på vilken telemetrityp som valts. I så fall visualisering innehållsändringar för olika telemetrityper.

När du klickar någonstans i bladet program kan du se standard **program** perspektiv.

![Application Insights program perspektiv](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektivet visar en översikt över det program som du har valt.

Den **tillgänglighet** bladet visar en olika perspektivvy där du kan se Webbresultat för testning och relaterade misslyckade begäranden.

![Application Insights tillgänglighet perspektiv](./media/app-insights-connector/availability-blade-drill-search.png)

När du klickar någonstans i den **serverbegäranden** eller **fel** blad, perspektiv komponenterna ändras för att ge dig en visualisering som rör begäranden.

![Bladet för Application Insights-fel](./media/app-insights-connector/server-requests-failures-drill-search.png)

När du klickar någonstans i den **undantag** bladet visas en visualisering som är skräddarsydd för undantag.

![Bladet för Application Insights-undantag](./media/app-insights-connector/exceptions-blade-drill-search.png)

Oavsett om du klickar på något någon den **Application Insights-anslutningsprogram** instrumentpanelen, i den **Search** sidan, alla frågor som returnerar Application Insights-data visar programmet Insikter perspektiv. Om du visar Application Insights-data, till exempel en **&#42;** frågan också visar fliken perspektiv som på följande bild:

![Application Insights ](./media/app-insights-connector/app-insights-search.png)

Perspektivet komponenter uppdateras beroende på frågan. Det innebär att du kan filtrera resultatet med hjälp av valfri sökfältet som ger dig möjlighet att se data från:

- Alla dina program
- Ett enda valda program
- En grupp av program

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Växla över till en app i Azure portal

Application Insights-anslutningsprogram blad är utformade så att du kan växla över till den valda appen Application Insights *när du använder Azure-portalen*. Du kan använda lösningen övervakningsplattform på hög nivå som hjälper dig att felsöka en app. När du ser ett potentiellt problem i någon av dina anslutna program, kan du antingen fördjupa dig i den i Log Analytics-sökningen eller du kan växla över direkt till appen Application Insights.

Pivottabell, klickar du på ellipserna (**...** ) som visas i slutet av varje rad och välj **öppna i Application Insights**.

>[!NOTE]
>**Öppna i Application Insights** är inte tillgänglig i Azure-portalen.

![Öppna i Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Exemplet korrigeras data

Application Insights ger *[sampling korrigering](../../application-insights/app-insights-sampling.md)* för att minska telemetritrafik. När du aktiverar sampling i Application Insights-app kan få du ett lägre antal poster som lagras både i Application Insights och Log Analytics. Medan datakonsekvens bevaras i den **Application Insights-anslutningsprogram** sidan och perspektiv, du kan manuellt rätta samplade data för de anpassade frågorna.

Här är ett exempel på sampling korrigering i en sökfråga i loggen:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Den **samplas antal** fält finns i alla poster och visar antalet datapunkter som posten representerar. Om du aktiverar sampling för din app med Application Insights, **samplas antal** är större än 1. Om du vill räkna antalet poster som ditt program genererar summera det **samplas antal** fält.

Sampling påverkar endast det totala antalet poster som ditt program genererar. Du behöver inte korrigera sampling för metriska fält som **RequestDuration** eller **AvailabilityDuration** eftersom dessa fält visar medelvärdet för representeras poster.

## <a name="input-data"></a>Indata

Lösningen tar emot följande telemetrityper av data från dina anslutna Application Insights-appar:

- Tillgänglighet
- Undantag
- Begäranden
- Sidvisningar – att ta emot sidvyer-arbetsytan måste du konfigurera dina appar för att samla in informationen. Mer information finns i [PageViews](../../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Anpassade händelser – att ta emot anpassade händelser-arbetsytan måste du konfigurera dina appar för att samla in informationen. Mer information finns i [TrackEvent](../../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Data tas emot av Log Analytics från Application Insights så fort de blir tillgängliga.

## <a name="output-data"></a>Utdata

En post med en *typ* av *ApplicationInsights* skapas för varje typ av indata. ApplicationInsights poster har egenskaper som visas i följande avsnitt:

### <a name="generic-fields"></a>Allmän fält

| Egenskap  | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tid för posten |
| ApplicationId | Instrumenteringsnyckeln för Application Insights-app |
| ApplicationName | Namnet på Application Insights app |
| Rollinstans | ID för server-värd |
| Enhetstyp | Klientenheten |
| ScreenResolution |   |
| Kontinent | Kontinent som begäran kom från |
| Land/region | Land där begäran har sitt ursprung |
| Län | Provins, tillstånd eller nationella inställningar som begäran kom från |
| Ort | Stad som begäran kom från |
| isSynthetic | Anger om begäran har skapats av en användare eller med automatiserad metod. = Användargenererat TRUE eller false = automatiserad metod |
| SamplingRate | Den procentandel av telemetri som genereras av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Exempel: 4 =&gt; 25% |
| IsAuthenticated | Sant eller falskt |
| Åtgärds-ID | Objekt som har samma åtgärd ID som visas som relaterade objekt i portalen. Vanligtvis begäran-ID |
| ParentOperationID | ID för den överordnade åtgärden |
| OperationName |   |
| sessions-ID | GUID för unik identifiering session där den skapades |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Tillgänglighet-specifika fält

| Egenskap  | Beskrivning |
| --- | --- |
| TelemetryType | Tillgänglighet |
| AvailabilityTestName | Namnet på webbtestet |
| AvailabilityRunLocation | Geografisk källan för http-begäran |
| AvailabilityResult | Visar resultatet av webbtestet lyckades |
| AvailabilityMessage | Meddelandet som är kopplade till webbtestet |
| AvailabilityCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 eller 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| AvailabilityDuration | Tid i millisekunder för web testets varaktighet |
| AvailabilityDurationCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikt GUID för webbtestet |
| AvailabilityTimestamp | Exakt tidsstämpel för tillgänglighetstestet |
| AvailabilityDurationMin | För provade poster visar det här fältet minsta web testets varaktighet (millisekunder) för representeras datapunkter |
| AvailabilityDurationMax | För provade poster visar det här fältet maximala web testets varaktighet (millisekunder) för representeras datapunkter |
| AvailabilityDurationStdDev | För provade poster visar det här fältet standardavvikelsen mellan alla web test varaktigheter (millisekunder) för representeras datapunkter |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Undantag-specifika fält

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Undantag |
| ExceptionType | Typen av undantag |
| ExceptionMethod | Metoden som skapar undantaget |
| ExceptionAssembly | Sammansättningen innehåller ramverket och version samt token för offentlig nyckel |
| ExceptionGroup | Typen av undantag |
| ExceptionHandledAt | Anger den nivå som hanteras undantaget |
| ExceptionCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| ExceptionMessage | Meddelande för undantaget |
| ExceptionStack | Fullständig stack av undantag |
| ExceptionHasStack | SANT om undantag har en stack |



### <a name="request-specific-fields"></a>Begäran-specifika fält

| Egenskap  | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Förfrågan |
| ResponseCode | HTTP-svar som skickas till klienten |
| RequestSuccess | Anger lyckad eller misslyckad. SANT eller FALSKT. |
| Begärande-ID | ID för att unikt identifiera begäran |
| RequestName | GET/POST + URL-bas |
| RequestDuration | Tid i sekunder, för varaktigheten för begäran |
| URL | URL för begäran inte inklusive värden |
| Värd | Web server-värden |
| URLBase | Fullständiga URL: en för begäran |
| ApplicationProtocol | Typ av protokoll som används av programmet |
| RequestCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Exempel: 4 =&gt; 25% |
| RequestDurationMin | Fältet visar varaktigheten för minsta begäran (millisekunder) för representeras datapunkter för provade poster. |
| RequestDurationMax | Det här fältet visas för provade poster i maximal varaktighet för begäran (millisekunder) för representeras datapunkter |
| RequestDurationStdDev | Det här fältet visas för provade poster standardavvikelsen mellan alla begäran varaktigheter (millisekunder) för representeras datapunkter |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Den här lösningen har inte en uppsättning exempel på loggsökningar visas på instrumentpanelen. Men exempel loggsökningsfrågor med beskrivningar visas i den [visa Application Insights-anslutningsprogram information](#view-application-insights-connector-information) avsnittet.

## <a name="next-steps"></a>Nästa steg

- Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad information för Application Insights-appar.
