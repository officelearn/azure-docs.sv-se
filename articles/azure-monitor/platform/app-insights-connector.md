---
title: Visa appdata för Azure Application Insights | Microsoft-dokument
description: Du kan använda Application Insights Connector-lösningen för att diagnostisera prestandaproblem och förstå vad användarna gör med din app när de övervakas med Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665173"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Hanteringslösning för Application Insights Connector (föråldrad)

![Symbol för Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Med stöd för [resursövergripande frågor](../../azure-monitor/log-query/cross-workspace-query.md)krävs inte längre hanteringslösningen Application Insights Connector. Den har tagits bort från Azure Marketplace, tillsammans med OMS-portalen som officiellt inaktuella den 15 januari 2019 för Azure kommersiella moln. Det kommer att dras tillbaka den 30 mars 2019 för Azure US Government cloud.
>
>Befintliga anslutningar kommer att fortsätta att fungera fram till den 30 juni 2019.  Med OMS-portalens utfasning finns det inget sätt att konfigurera och ta bort befintliga anslutningar från portalen. Se [Ta bort kopplingen med PowerShell](#removing-the-connector-with-powershell) nedan för ett skript på att använda PowerShell för att ta bort befintliga anslutningar.
>
>Mer information om hur du frågar programstatistikloggdata för flera program finns i Identifiera resurser för [flera Azure Monitor Application Insights](../log-query/unify-app-resource-data.md). Mer information om OMS-portalens utfasning finns i [OMS-portalen som flyttar till Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Application Insights Connector-lösningen hjälper dig att diagnostisera prestandaproblem och förstå vad användarna gör med din app när den övervakas med [Application Insights](../../azure-monitor/app/app-insights-overview.md). Vyer av samma programtelemetri som utvecklare ser i Application Insights är tillgängliga i Logganalys. Men när du integrerar dina Application Insights-appar med Log Analytics ökar synligheten för dina program genom att du har drift- och programdata på ett ställe. Med samma vyer kan du samarbeta med dina apputvecklare. De gemensamma vyerna kan bidra till att minska tiden för att identifiera och lösa både program- och plattformsproblem.

När du använder lösningen kan du:

- Visa alla dina Application Insights-appar på ett och samma ställe, även när de finns i olika Azure-prenumerationer
- Korrelera infrastrukturdata med programdata
- Visualisera programdata med perspektiv i loggsökning
- Pivotera från Log Analytics-data till appen Application Insights i Azure-portalen


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra Log Analytics-lösningar samlas data inte in för Application Insights Connector av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Inga | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM:s ledningsgrupp](../../azure-monitor/platform/om-agents.md) | Inga | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure-lagringskonto](collect-azure-metrics-logs.md) | Inga | Lösningen samla inte in information från Azure-lagring. |

## <a name="prerequisites"></a>Krav

- För att komma åt Application Insights Connector-information måste du ha en Azure-prenumeration
- Du måste ha minst en konfigurerad Application Insights-resurs.
- Du måste vara ägare eller deltagare i application insights-resursen.

## <a name="configuration"></a>Konfiguration

1. Aktivera Azure Web Apps Analytics-lösningen från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) eller genom att använda den process som beskrivs i [Lägg till logganalyslösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Bläddra till [Azure-portalen](https://portal.azure.com). Välj **Alla tjänster** för att öppna Application Insights. Sök sedan efter Programinsikter. 
3. Under **Prenumerationer**väljer du en prenumeration som har Application Insights-resurser och väljer sedan ett eller flera program under **Namn.**
4. Klicka på **Spara**.

På cirka 30 minuter blir data tillgängliga och panelen Application Insights uppdateras med data, till exempel följande bild:

![Panelen Programinsikter](./media/app-insights-connector/app-insights-tile.png)

Andra punkter att tänka på:

- Du kan bara länka Application Insights-appar till en Log Analytics-arbetsyta.
- Du kan bara länka [grundläggande resurser eller enterprise application insights-resurser](https://azure.microsoft.com/pricing/details/application-insights) till Log Analytics. Du kan dock använda den kostnadsfria nivån för Logganalys.

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inga hanteringspaket i anslutna hanteringsgrupper.

## <a name="use-the-solution"></a>Använd lösningen

I följande avsnitt beskrivs hur du kan använda bladen som visas på instrumentpanelen Application Insights för att visa och interagera med data från dina appar.

### <a name="view-application-insights-connector-information"></a>Visa anslutningsinformation för Application Insights

Klicka på panelen **Programstatistik** om du vill öppna instrumentpanelen **Application Insights** för att se följande blad.

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash02.png)

Instrumentpanelen innehåller bladen som visas i tabellen. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster när du klickar på **Visa alla** längst ned i bladet eller när du klickar på bladhuvudet.


| **Kolumn** | **Beskrivning** |
| --- | --- |
| Ansökningar - Antal ansökningar | Visar antalet program i programresurser. Visar även programnamn och för varje antal programposter. Klicka på numret för att köra en loggsökning efter<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klicka på ett programnamn om du vill köra en loggsökning efter programmet som visar programposter per värd, poster efter telemetrityp och alla data efter typ (baserat på den sista dagen). |
| Datavolym – Värdar som skickar data | Visar antalet datorvärdar som skickar data. Listar även datorvärdar och postantal för varje värd. Klicka på numret för att köra en loggsökning efter<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klicka på ett datornamn för att köra en loggsökning för värden som visar programposter per värd, poster efter telemetrityp och alla data efter typ (baserat på den sista dagen). |
| Tillgänglighet – Webbtestresultat | Visar ett ringdiagram för webbtestresultat som anger godkänd eller misslyckad. Klicka på diagrammet för att köra en loggsökning efter<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Resultaten visar antalet pass och fel för alla tester. Den visar alla webbappar med trafik för sista minuten. Klicka på ett programnamn om du vill visa en loggsökning som visar information om misslyckade webbtester. |
| Serverförfrågningar – begäranden per timme | Visar ett linjediagram över serverbegäranden per timme för olika program. Hovra över en linje i diagrammet för att se de tre vanligaste programmen som tar emot begäranden för en tidpunkt. Visar också en lista över de program som tar emot begäranden och antalet begäranden för den valda perioden. <br><br>Klicka på diagrammet om du <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> vill köra en loggsökning efter som visar ett mer detaljerat linjediagram över serverbegäranden per timme för olika program. <br><br> Klicka på ett program i listan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> för att köra en loggsökning efter som visar en lista över begäranden, diagram för begäranden över tid och begärandens varaktighet och en lista över svarskoder för begäranden.   |
| Fel – misslyckade begäranden per timme | Visar ett linjediagram över misslyckade programbegäranden per timme. Hovra över diagrammet för att se de tre bästa programmen med misslyckade begäranden för en viss tid. Visar också en lista över program med antalet misslyckade begäranden för varje. Klicka på diagrammet om du <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> vill köra en loggsökning efter som visar ett mer detaljerat linjediagram över misslyckade programbegäranden. <br><br>Klicka på ett objekt i listan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> för att köra en loggsökning efter som visar misslyckade begäranden, diagram för misslyckade begäranden över tid och begärandens varaktighet och en lista över misslyckade svarskoder för begäran. |
| Undantag – Undantag per timme | Visar ett linjediagram med undantag per timme. Hovra över diagrammet för att se de tre bästa programmen med undantag för en viss tid. Visar också en lista över program med antalet undantag för varje. Klicka på diagrammet om du <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> vill köra en loggsökning efter som visar ett mer detaljerat länkdiagram med undantag. <br><br>Klicka på ett objekt i listan <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> om du vill köra en loggsökning efter som visar en lista med undantag, diagram för undantag över tid och misslyckade begäranden och en lista över undantagstyper.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visa application insights-perspektivet med loggsökning

När du klickar på ett objekt på instrumentpanelen visas ett application insights-perspektiv som visas i sökningen. Perspektivet ger en utökad visualisering, baserat på den telemetrityp som har valts. Visualiseringsinnehållet ändras därför för olika telemetrityper.

När du klickar någonstans i bladet Program visas **standardperspektivet program.**

![Application Insights Applications perspektiv](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektivet visar en översikt över det program som du har valt.

Bladet **Tillgänglighet** visar en annan perspektivvy där du kan se webbtestresultat och relaterade misslyckade begäranden.

![Tillgänglighetsperspektiv för programinsikter](./media/app-insights-connector/availability-blade-drill-search.png)

När du klickar någonstans i bladen **Serverbegäranden** eller **fel** ändras perspektivkomponenterna så att du får en visualisering som är relaterad till begäranden.

![Bladet Application Insights-fel](./media/app-insights-connector/server-requests-failures-drill-search.png)

När du klickar någonstans i bladet **Undantag** visas en visualisering som är anpassad till undantag.

![Bladet Undantag för Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Oavsett om du klickar på något i instrumentpanelen **Search** Application **Insights Connector,** visar alla frågor som returnerar Application Insights-data perspektivet i sidan Application Insights. Om du till exempel visar Programstatistikdata visas även perspektivfliken på fliken **&#42;** som följande bild:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektivkomponenter uppdateras beroende på sökfrågan. Det innebär att du kan filtrera resultaten med hjälp av ett sökfält som ger dig möjlighet att se data från:

- Alla dina program
- Ett enda valt program
- En grupp ansökningar

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivotera till en app i Azure-portalen

Application Insights Connector-bladen är utformade så att du kan pivotera till den valda Application Insights-appen *när du använder Azure-portalen*. Du kan använda lösningen som en övervakningsplattform på hög nivå som hjälper dig att felsöka en app. När du ser ett potentiellt problem i något av dina anslutna program kan du antingen gå in i det i Logganalyssökning eller pivotera direkt till application insights-appen.

Om du vill pivotera klickar du på de ellipser (**...**) som visas i slutet av varje rad och väljer **Öppna i Application Insights**.

>[!NOTE]
>Det går inte **att öppna i Application Insights** i Azure-portalen.

![Öppna i Programinsikter](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Exempel korrigerade data

Application Insights ger *[samplingskorrigering](../../azure-monitor/app/sampling.md)* för att minska telemetritrafiken. När du aktiverar sampling i appen Application Insights får du ett minskat antal poster som lagras både i Application Insights och i Logganalys. Även om datakonsekvens bevaras på sidan **Application Insights Connector** och perspektiv, bör du manuellt korrigera exempeldata för dina anpassade frågor.

Här är ett exempel på samplingskorrigering i en loggsökningsfråga:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Fältet **Antal prov** består finns i alla poster och visar antalet datapunkter som transaktionen representerar. Om du aktiverar sampling för appen Application Insights är **antalet exempel på** prov större än 1. Om du vill räkna det faktiska antalet transaktioner som genereras i programmet summerar du fälten **Antal prov.**

Sampling påverkar bara det totala antalet transaktioner som programmet genererar. Du behöver inte korrigera sampling för måttfält som **RequestDuration** eller **AvailabilityDuration** eftersom dessa fält visar medelvärdet för representerade transaktioner.

## <a name="input-data"></a>Indata

Lösningen tar emot följande telemetrityper av data från dina anslutna Application Insights-appar:

- Tillgänglighet
- Undantag
- Begäranden
- Sidvisningar – För att arbetsytan ska kunna ta emot sidvisningar måste du konfigurera dina appar så att de samlar in den informationen. Mer information finns i [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Anpassade händelser – För att arbetsytan ska kunna ta emot anpassade händelser måste du konfigurera dina appar för att samla in den informationen. Mer information finns i [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Data tas emot av Log Analytics från Application Insights när de blir tillgängliga.

## <a name="output-data"></a>Utdata

En post med en *typ* av *ApplicationInsights skapas* för varje typ av indata. ApplicationInsights-poster har egenskaper som visas i följande avsnitt:

### <a name="generic-fields"></a>Allmänna fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| KlientIP |   |
| TimeGenerated | Tid för posten |
| ApplicationId | Instrumenteringsnyckel för appen Application Insights |
| ApplicationName | Namn på appen Application Insights |
| RollIntance | ID för servervärd |
| DeviceType | Klientenhet |
| SkärmUpplösning |   |
| Kontinent | Kontinent där begäran kom från |
| Land/region | Land/region där begäran har sitt ursprung |
| Provinsen | Provins, delstat eller språk där begäran har sitt ursprung |
| Ort | Stad eller stad där begäran har sitt ursprung |
| ärSynthetic | Anger om begäran har skapats av en användare eller med automatiserad metod. True = automatiserad metod eller false = genererad användare |
| SamplingTra | Procentandel telemetri som genereras av SDK som skickas till portalen. Räckvidd 0,0-100,0. |
| SampledCount | 100/(SamplingRate). Till exempel 4&gt; = 25% |
| Ärautentiserade | Sant eller falskt |
| OperationID | Objekt som har samma åtgärds-ID visas som Relaterade objekt i portalen. Vanligtvis begäran ID |
| ParentOperationID | ID för den överordnade åtgärden |
| OperationName |   |
| Sessionid | GUID för att unikt identifiera sessionen där begäran skapades |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Tillgänglighetsspecifika fält

| Egenskap | Beskrivning |
| --- | --- |
| Telemetrityp | Tillgänglighet |
| TillgänglighetTestNamn | Namn på webbtestet |
| TillgänglighetRunLocation | Geografisk källa till http-begäran |
| TillgänglighetResultat | Anger resultatet av webbtestet |
| TillgänglighetMessage | Meddelandet som är kopplat till webbtestet |
| Tillgänglighetskonto | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| DataSizeMetricValue | 1.0 eller 0.0 |
| DataSizeMetricCount | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| TillgänglighetUndergång | Tid, i millisekunder, av webbtestets varaktighet |
| TillgänglighetUnderationsräknad | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| TillgänglighetVärdera |   |
| TillgänglighetMetricCount |   |
| TillgänglighetTestId | Unikt GUID för webbtestet |
| TillgänglighetTimestamp | Exakt tidsstämpel för tillgänglighetstestet |
| TillgänglighetUnderationMin | För samplade poster visar det här fältet den minsta webbtestvaraktigheten (millisekunder) för de representerara datapunkterna |
| TillgänglighetUnderationMax | För samplade poster visar det här fältet den maximala webbtesttiden (millisekunder) för de representerara datapunkterna |
| TillgänglighetDurationStdDev | För samplade poster visar det här fältet standardavvikelsen mellan alla webbtestvaraktigheter (millisekunder) för de representerade datapunkterna |
| TillgänglighetMin |   |
| TillgänglighetMax |   |
| TillgänglighetStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Undantagsspecifika fält

| Typ | ApplicationInsights |
| --- | --- |
| Telemetrityp | Undantag |
| Undantagstyp | Typ av undantag |
| UndantagMethod | Den metod som skapar undantaget |
| UndantagMontering | Församlingen innehåller ramverket och versionen samt den offentliga nyckeltoken |
| Undantagsgrupp | Typ av undantag |
| ExceptionHandledAt | Anger den nivå som hanterade undantaget |
| Undantagsräkning | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| UndantagMessage | Meddelande om undantaget |
| ExceptionStack | Full stack av undantaget |
| ExceptionHasStack | Sant, om undantaget har en stapel |



### <a name="request-specific-fields"></a>Request-specifika fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| Telemetrityp | Förfrågan |
| Svarskod | HTTP-svar som skickas till klienten |
| Begäranstrå | Anger framgång eller misslyckande. Sant eller falskt. |
| RequestID | ID för att unikt identifiera begäran |
| RequestName (RequestName) | HÄMTA/POST + URL-bas |
| BegäranDuration | Tid, i sekunder, för begärans varaktighet |
| URL | URL för begäran som inte inkluderar värd |
| Värd | Värd för webbserver |
| URLBase (URLBase) | Fullständig URL för begäran |
| ApplicationProtocol | Typ av protokoll som används av programmet |
| Begäranrring | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| Begäranrvarelse | 100/(Provtagningshastighet). Till exempel 4&gt; = 25% |
| BegäranDurationMin | För samplade poster visar det här fältet minsta varaktighet för begäran (millisekunder) för de representerade datapunkterna. |
| BegäranDurationMax | För poster som har samplat visar det här fältet den maximala begärandens varaktighet (millisekunder) för de representerara datapunkterna |
| BegäranDurationStdDev | För samplade poster visar det här fältet standardavvikelsen mellan alla varaktigheter för begäran (millisekunder) för de representerade datapunkterna |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Den här lösningen har inte en uppsättning exempelloggsökningar som visas på instrumentpanelen. Exempel på loggsökningsfrågor med beskrivningar visas dock i avsnittet [Visa programinsiktsanslutningsinformation.](#view-application-insights-connector-information)

## <a name="removing-the-connector-with-powershell"></a>Ta bort kontakten med PowerShell
Med OMS-portalens utfasning finns det inget sätt att konfigurera och ta bort befintliga anslutningar från portalen. Du kan ta bort befintliga anslutningar med följande PowerShell-skript. Du måste vara ägare eller deltagare i arbetsytan och läsaren av Application Insights-resursen för att kunna utföra den här åtgärden.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Du kan hämta en lista över program med följande PowerShell-skript som anropar ett REST API-anrop. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Det här skriptet kräver en autentiseringstoken för bärare för autentisering mot Azure Active Directory. Ett sätt att hämta den här token är att använda en artikel på [rest API-dokumentationsplatsen](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Klicka på **Prova** och logga in på din Azure-prenumeration. Du kan kopiera innehavartoken från **förhandsgranskningen av begäran** enligt följande bild.


![Bärare token](media/app-insights-connector/bearer-token.png)


Du kan också hämta en lista över program med hjälp av en loggfråga:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Nästa steg

- Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerad information för dina Application Insights-appar.
