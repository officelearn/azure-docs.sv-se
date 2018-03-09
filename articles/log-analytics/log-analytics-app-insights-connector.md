---
title: Visa Azure Application Insights AppData | Microsoft Docs
description: "Du kan använda Application Insights Connector-lösning för att diagnostisera prestandaproblem och förstå vad användarna göra med din app när övervakas med Application Insights."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 1556e91710990351d6723325789201afa99b1943
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="application-insights-connector-management-solution-preview"></a>Application Insights Connector lösning (förhandsgranskning)

![Application Insights symbol](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

Program Insights Connector-lösningen hjälper dig att diagnostisera prestandaproblem och förstå vad användare göra med din app när den är övervakad med [Programinsikter](../application-insights/app-insights-overview.md). Vyer för samma programtelemetri som utvecklare finns i Application Insights är tillgängliga i logganalys. När du integrerar dina Application Insights-appar med logganalys ökas visningen av dina program genom att låta igen och programdata på ett ställe. Med samma vyer kan du samarbeta med din apputvecklare. Vanliga vyer kan du minska tiden för att identifiera och lösa både program- och plattform problem.

När du använder lösningen kan du:

- Visa alla dina appar i Application Insights på en plats, även om de finns i olika Azure-prenumerationer
- Korrelera infrastrukturdata med programdata
- Visualisera programdata med perspektiv i loggen Sök
- Pivotera från logganalys data i appen Application Insights i Azure-portalen

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra logganalys-lösningar är inte samlas in för Application Insights Connector av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](log-analytics-windows-agent.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Nej | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Lösningen matchar inte samlingsinformation från Azure storage. |

## <a name="prerequisites"></a>Förutsättningar

- Om du vill komma åt information om Application Insights Connector måste du ha en Azure-prenumeration
- Du måste ha minst en konfigurerade Application Insights-resurs.
- Du måste vara ägare eller deltagare i Application Insights-resursen.

## <a name="configuration"></a>Konfiguration

1. Aktivera Azure Web Apps Analytics-lösning från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. I OMS-portalen klickar du på **inställningar** &gt; **Data** &gt; **Programinsikter**.
3. Under **välja en prenumeration**, välja en prenumeration med Application Insights-resurser och sedan under **programnamn**, Välj en eller flera program.
4. Klicka på **Spara**.

Data blir tillgängliga i cirka 30 minuter och panelen Application Insights uppdateras med data, till exempel följande bild:

![Application Insights panelen](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Andra punkter att tänka på:

- Du kan bara koppla Application Insights appar till en logganalys-arbetsytan.
- Du kan bara koppla [grundläggande eller Enterprise Application Insights-resurser](https://azure.microsoft.com/pricing/details/application-insights) till logganalys. Du kan dock använda den kostnadsfria nivån av logganalys.

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inte några management packs i anslutna hanteringsgrupper.

## <a name="use-the-solution"></a>Använda lösningen

I följande avsnitt beskrivs hur du kan använda blad som visas i Application Insights-instrumentpanelen för att visa och interagera med data från dina appar.

### <a name="view-application-insights-connector-information"></a>Visa information om Application Insights Connector

Klicka på den **Programinsikter** öppna den **Application Insights** instrumentpanelen för att se följande blad.

![Instrumentpanel för program insikter](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Instrumentpanel för program insikter](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Instrumentpanelen innehåller blad som visas i tabellen. Varje bladet visar upp till 10 objekt som matchar det bladet villkoren för angivet omfång och tidsintervall. Du kan köra en sökning i loggen som returnerar alla poster när du klickar på **se alla** längst ned på bladet eller när du klickar på rubriken bladet.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Kolumnen** | **Beskrivning** |
| --- | --- |
| Program – antal program | Visar antalet program i programresurser. Visar också programnamn och för varje antalet poster i programmet. Klicka på siffran om du vill köra en logg sökning efter <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by ApplicationName</code> <br><br>  Klicka på ett programnamn att köra en sökning för loggen för det program som visar programmet poster per värd, poster av typen telemetri och alla data av typen (baserat på den sista dagen). |
| Datavolymen – värdar som skickar data | Visar antalet värdar på datorn som data skickas. Visar också datorn är värd och antal poster för varje värd. Klicka på siffran om du vill köra en logg sökning efter <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by Host</code> <br><br> Klicka på ett datornamn om du vill utföra en logg-sökning för värden som visar programmet poster per värd, poster av typen telemetri och alla data av typen (baserat på den sista dagen). |
| Tillgänglighet – Webtest resultat | Visar ett ringdiagram för webbtestresultat, som anger pass eller misslyckas. Klicka på diagrammet om du vill köra en logg sökning efter <code>Type=ApplicationInsights TelemetryType=Availability &#124; measure sum(SampledCount) by AvailabilityResult</code> <br><br> Resultatet visar antalet gånger och fel för alla test. Den visar alla webbprogram med trafik för den senaste minuten. Klicka på ett namn på programmet ska visa en logg sökning visar information om misslyckade webbtest. |
| Serverbegäranden – förfrågningar per timme | Visar ett linjediagram för serverbegäranden per timme för olika program. Hovra över en linje i diagrammet för att se upp 3 programmen ta emot begäranden för en punkt i tiden. Visar också en lista över program som tar emot begäranden och antalet begäranden för den valda perioden. <br><br>Klicka på diagrammet om du vill köra en logg sökning efter <code>Type=ApplicationInsights TelemetryType=Request &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> som visar mer detaljerad linjediagram för serverbegäranden per timme för olika program. <br><br> Klicka på ett program i listan för att köra en logg sökning efter <code>Type=ApplicationInsights  ApplicationName=yourapplicationname  TelemetryType=Request</code> som visar en lista med begäranden, diagram för begäranden över tid och begäran varaktighet och en lista över begäran svarskoder.   |
| Fel – misslyckade begäranden per timme | Visar ett linjediagram för misslyckade programförfrågningar per timme. Hovra över diagrammet för att se de översta 3 program med misslyckade begäranden för en punkt i tiden. Visar en lista över program med antalet misslyckade begäranden för varje också. Klicka på diagrammet om du vill köra en logg sökning efter <code>Type=ApplicationInsights TelemetryType=Request  RequestSuccess = false &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> som visar mer detaljerad linjediagram av begäranden om misslyckade program. <br><br>Klicka på ett objekt i listan för att köra en logg sökning efter <code>Type=ApplicationInsights ApplicationName=yourapplicationname TelemetryType=Request  RequestSuccess=false</code> som visar misslyckade begäranden, diagram för misslyckade begäranden över tid och begäran varaktighet och en lista över svarskoder för misslyckade begäranden. |
| Undantag – undantag per timme | Visar ett linjediagram av undantag per timme. Hovra över diagrammet för att se de översta 3 program med undantag för en punkt i tiden. Visar en lista över program med antalet undantag för varje också. Klicka på diagrammet om du vill köra en logg sökning efter <code>Type=ApplicationInsights TelemetryType=Exception &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> som visar mer detaljerad länken i diagrammet över undantag. <br><br>Klicka på ett objekt i listan för att köra en logg sökning efter <code>Type=ApplicationInsights  ApplicationName=yourapplicationname TelemetryType=Exception</code> som visar en lista över undantag, diagram för undantag över tid och misslyckade begäranden och en lista över typer av undantag.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visa Application Insights perspektiv med Sök i loggfilen

När du klickar på ett objekt i instrumentpanelen kan du se ett perspektiv för Application Insights som visas i sökningen. Perspektivet innehåller en utökad visualiseringen baserat på typen telemetri som valts. I så fall visualiseringen innehållsändringar för olika telemetri typer.

När du klickar någonstans i bladet program visas standard **program** perspektiv.

![Application Insights program perspektiv](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

Perspektiv visar en översikt över det program som du har valt.

Den **tillgänglighet** bladet visar perspektivvyn olika där du kan se webbtestresultat och relaterade misslyckade begäranden.

![Application Insights tillgänglighet perspektiv](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

När du klickar någonstans i den **serverbegäranden** eller **fel** blad, perspektiv komponenterna ändras så att du får en visualisering som rör begäranden.

![Application Insights fel bladet](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

När du klickar någonstans i den **undantag** bladet finns en visualisering som är anpassad efter undantag.

![Application Insights undantag bladet](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Oavsett om du klickar på något en den **Application Insights Connector** instrumentpanelen, inom den **Sök** sidan, alla frågor som returnerar Application Insights-data visar Application Insights perspektiv. Om du visar Application Insights-data, till exempel en  **&#42;**  frågan också visar fliken perspektiv som på följande bild:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Perspektiv komponenter uppdateras beroende på frågan. Detta innebär att du kan filtrera resultaten genom att använda alla sökfält som ger dig möjlighet att se data från:

- Alla program
- Ett enda valt program
- En grupp av program

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivotera till en app i Azure-portalen

Application Insights Connector blad är utformade att pivotera till den valda appen Application Insights *när du använder OMS-portalen*. Du kan använda lösningen som en övergripande övervakning plattform som hjälper dig att felsöka en app. När du ser ett potentiellt problem i någon av dina anslutna program, kan du antingen gå till den i logganalys sökning eller du kan pivotera direkt i appen Application Insights.

Om du vill pivotera, klickar du på ellipserna (**...** ) som visas i slutet av varje rad och välj **öppna i Application Insights**.

>[!NOTE]
>**Öppna i Application Insights** är inte tillgängligt i Azure-portalen.

![Öppna i Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Korrigerade exempel data

Application Insights ger  *[provtagning korrigering](../application-insights/app-insights-sampling.md)*  för att minska telemetri trafik. När du aktiverar provtagning på appen Application Insights hämta minskat antal poster som lagras i Application Insights och i logganalys. Medan datakonsekvens bevaras i den **Application Insights Connector** sida och perspektiv, bör du manuellt korrigera samplade data för dina egna frågor.

Här är ett exempel på provtagning korrigering i en sökning i loggen:

```
Type=ApplicationInsights | measure sum(SampledCount) by TelemetryType
```

Den **provtagning antal** fältet finns i alla poster och visar antalet datapunkter som representerar posten. Om du aktiverar samplingsfrekvensen för appen Application Insights **provtagning antal** är större än 1. Om du vill räkna det faktiska antalet poster som ditt program genererar sum den **provtagning antal** fält.

Sampling påverkar endast det totala antalet poster som programmet skapar. Du behöver inte korrigera samplingsfrekvensen för mått fält som **RequestDuration** eller **AvailabilityDuration** eftersom dessa fält visar medelvärdet för representeras poster.

## <a name="input-data"></a>Indata

Lösningen tar emot telemetri följande typer av data från dina anslutna appar Application Insights:

- Tillgänglighet
- Undantag
- Begäranden
- Sidvisningar – för din arbetsyta för att ta emot sidvisningar, måste du konfigurera dina appar för att samla in informationen. Mer information finns i [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Anpassade händelser – för din arbetsyta för att ta emot anpassade händelser, måste du konfigurera dina appar för att samla in informationen. Mer information finns i [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Data tas emot av logganalys från Application Insights vartefter det blir tillgängligt.

## <a name="output-data"></a>Utdata

En post med en *typen* av *ApplicationInsights* skapas för varje typ av indata. ApplicationInsights poster har egenskaperna som visas i följande avsnitt:

### <a name="generic-fields"></a>Allmän fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tiden då posten |
| ApplicationId | Instrumentation nyckeln för appen Application Insights |
| ApplicationName | Namnet på Application Insights app |
| RoleInstance | ID för server-värd |
| DeviceType | Klientenheten |
| ScreenResolution |   |
| Kontinent | Kontinent som begäran kom från |
| Land/region | Land där begäran har sitt ursprung |
| Län | Region, status eller locale där begäran har sitt ursprung |
| Ort | Stad där begäran har sitt ursprung |
| isSynthetic | Anger om begäran har skapats av en användare eller automatiserad metod. = Användargenererat TRUE eller false = automatiserad metod |
| SamplingRate | Procentandelen telemetri som genererats av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Till exempel 4 =&gt; 25% |
| IsAuthenticated | Sant eller falskt |
| OperationID | Objekt som har samma åtgärd ID visas som relaterade objekt i portalen. Vanligtvis begäran-ID |
| ParentOperationID | ID för överordnad igen |
| OperationName |   |
| SessionId | GUID för att identifiera den session där den skapades |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Tillgänglighet-specifika fält

| Egenskap | Beskrivning |
| --- | --- |
| TelemetryType | Tillgänglighet |
| AvailabilityTestName | Namnet på webbtestet |
| AvailabilityRunLocation | Geografisk källan för http-begäran |
| AvailabilityResult | Anger lyckade resultat av webbtestet |
| AvailabilityMessage | Meddelandet som är kopplade till webbtestet |
| AvailabilityCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 eller 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| AvailabilityDuration | Tid i millisekunder för testperioden för webbprogram |
| AvailabilityDurationCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikt GUID för webbtestet |
| AvailabilityTimestamp | Exakt tidsstämpel tillgängligheten testet |
| AvailabilityDurationMin | Det här fältet visar minsta web testperioden (millisekunder) för datapunkter som representeras för provade poster |
| AvailabilityDurationMax | För provade poster visar i det här fältet maximala web testperioden (millisekunder) för representeras datapunkter |
| AvailabilityDurationStdDev | Det här fältet visar standardavvikelsen mellan alla web test varaktighet (i millisekunder) för representeras datapunkter för provade poster |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Undantag-specifika fält

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Undantag |
| ExceptionType | Typ av undantag |
| ExceptionMethod | Den metod som skapar undantaget |
| ExceptionAssembly | Sammansättningen som innehåller ramen och version samt token för offentlig nyckel |
| ExceptionGroup | Typ av undantag |
| ExceptionHandledAt | Anger nivån som hanteras av undantag |
| ExceptionCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| ExceptionMessage | Meddelandet för undantaget |
| ExceptionStack | Fullständig stack för undantaget |
| ExceptionHasStack | TRUE om undantag har en stack |



### <a name="request-specific-fields"></a>Begäran-specifika fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Förfrågan |
| ResponseCode | HTTP-svar som skickats till klienten |
| RequestSuccess | Anger lyckad eller misslyckad. SANT eller FALSKT. |
| Begärande-ID | ID för att unikt identifiera begäran |
| RequestName | GET/POST + URL-bas |
| RequestDuration | Tid i sekunder, för varaktighet för begäran |
| URL | URL för begäran inte inklusive värden |
| Värd | Web server-värd |
| URLBase | Fullständig URL för begäran |
| ApplicationProtocol | Typ av protokoll som används av programmet |
| RequestCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| RequestDurationMin | Fältet visar den minsta begäran varaktigheten (i millisekunder) för representeras datapunkter för provade poster. |
| RequestDurationMax | För provade poster visar i det här fältet den maximal varaktigheten för begäran (millisekunder) för representeras datapunkter |
| RequestDurationStdDev | Det här fältet visas för provade poster standardavvikelsen mellan alla begäran varaktighet (i millisekunder) för representeras datapunkter |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Den här lösningen har inte en uppsättning exempel loggen sökningar visas på instrumentpanelen. Dock exempelfrågor loggen sökning med en beskrivning som visas i den [visa Application Insights Connector information](#view-application-insights-connector-information) avsnitt.

## <a name="next-steps"></a>Nästa steg

- Använd [loggen Sök](log-analytics-log-searches.md) att visa detaljerad information för Application Insights-appar.
