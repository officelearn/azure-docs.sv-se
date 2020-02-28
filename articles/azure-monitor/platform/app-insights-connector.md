---
title: Visa Azure Application Insights AppData | Microsoft Docs
description: Du kan använda lösningen för Application Insights-anslutningsprogram för att diagnostisera prestandaproblem och förstå vad användarna gör med din app när övervakas med Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665173"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-anslutningsprogram hanterings lösning (inaktuell)

![Application Insights symbol](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Med stöd för [frågor över olika resurser](../../azure-monitor/log-query/cross-workspace-query.md)krävs inte längre den Application Insights-anslutningsprogram hanterings lösningen. Den har ersatts och tagits bort från Azure Marketplace, tillsammans med OMS-portalen som officiellt förbrukades den 15 januari 2019 för Azures kommersiella moln. Den kommer att dras tillbaka den 30 mars 2019 för Azures moln för amerikanska myndigheter.
>
>Befintliga anslutningar fortsätter att fungera fram till den 30 juni 2019.  Med OMS-portalen är det inte möjligt att konfigurera och ta bort befintliga anslutningar från portalen. Se [ta bort anslutningen med PowerShell](#removing-the-connector-with-powershell) nedan för ett skript på att använda PowerShell för att ta bort befintliga anslutningar.
>
>Anvisningar om hur du frågar Application Insights loggdata för flera program finns i [förena flera Azure Monitor Application Insights resurser](../log-query/unify-app-resource-data.md). Mer information om utfasningen av OMS-portalen finns i [OMS-portalen flytta till Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Med program insikts kopplings lösningen kan du diagnostisera prestanda problem och förstå vad användarna gör med din app när den övervakas med [Application Insights](../../azure-monitor/app/app-insights-overview.md). Vyer för samma programtelemetri som utvecklare kan se i Application Insights är tillgängliga i Log Analytics. När du integrerar dina Application Insights-appar med Log Analytics, ökas visningen av dina program genom att använda åtgärden och programdata på samma ställe. Att ha samma vyer hjälper dig att samarbeta med dina apputvecklare. Vanliga vyer kan du minska tiden för att identifiera och lösa både programmet och plattformsproblem.

När du använder lösningen kan du:

- Visa alla Application Insights-appar på samma plats, även om de finns i olika Azure-prenumerationer
- Korrelera infrastrukturdata med programdata
- Visualisera programdata med perspektiv i loggsökning
- Växla över från Log Analytics-data till din Application Insights-app i Azure portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra Log Analytics-lösningar, inte som samlas in för Application Insights-anslutningsprogram av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanterings grupp](../../azure-monitor/platform/om-agents.md) | Nej | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure Storage-konto](collect-azure-metrics-logs.md) | Nej | Lösningen gör inte samlingsinformation från Azure storage. |

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration för att komma åt information för Application Insights-anslutningsprogram
- Du måste ha minst en konfigurerade Application Insights-resurs.
- Du måste vara ägare eller deltagare i Application Insights-resursen.

## <a name="configuration"></a>Konfiguration

1. Aktivera Azure Web Apps-analys-lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till Log Analytics lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Bläddra till [Azure-portalen](https://portal.azure.com). Välj **alla tjänster** för att öppna Application Insights. Sök sedan efter Application Insights. 
3. Under **prenumerationer**väljer du en prenumeration som har Application Insights resurser och väljer sedan ett eller flera program under **namn**.
4. Klicka på **Save** (Spara).

Data blir tillgängliga i cirka 30 minuter och Application Insights-panelen uppdateras med data, som på följande bild:

![Application Insights-panelen](./media/app-insights-connector/app-insights-tile.png)

Andra saker att tänka på:

- Du kan bara koppla Application Insights-appar till en Log Analytics-arbetsyta.
- Du kan bara länka [Basic-eller Enterprise Application Insights-resurser](https://azure.microsoft.com/pricing/details/application-insights) till Log Analytics. Du kan dock använda den kostnadsfria nivån av Log Analytics.

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inte alla hanteringspaket i anslutna hanteringsgrupper.

## <a name="use-the-solution"></a>Använd lösningen

I följande avsnitt beskrivs hur du kan använda bladen visas i instrumentpanelen med Application Insights för att visa och interagera med data från dina appar.

### <a name="view-application-insights-connector-information"></a>Visa information om Application Insights-anslutningsprogram

Klicka på panelen **Application Insights** för att öppna instrument panelen **Application Insights** för att se följande blad.

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights-instrumentpanel](./media/app-insights-connector/app-insights-dash02.png)

Instrumentpanelen innehåller blad visas i tabellen. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggs ökning som returnerar alla poster när du klickar på **Visa alla** längst ned på bladet eller när du klickar på bladet rubrik.


| **Artikeln** | **Beskrivning** |
| --- | --- |
| Program - antal program | Visar antalet program i programresurser. Visar också programnamn och för varje antalet programposter. Klicka på numret för att köra en loggs ökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klicka på namnet på ett program att köra en loggsökning som visar programposter per värd, poster per telemetrityp och alla data per typ (baserat på den sista dagen). |
| Datavolym – värdar som skickar data | Visar antalet datorn värdar som skickar data. Visas även datorn är värd och antal poster för varje värd. Klicka på numret för att köra en loggs ökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klicka på ett datornamn att köra en loggsökning för värden som visar programposter per värd, poster per telemetrityp och alla data per typ (baserat på den sista dagen). |
| Tillgänglighet – webbtestresultat | Visar ett ringdiagram för test av Webbresultat, som anger pass eller misslyckas. Klicka på diagrammet för att köra en loggs ökning för <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Resultatet visar antalet pass och fel för alla test. Den visar alla Web Apps med trafik för den senaste minuten. Klicka på namnet på ett program att visa en loggsökning som visar information om misslyckade webbtester. |
| Serverbegäranden – förfrågningar per timme | Visar ett linjediagram med serverbegäranden per timme för olika program. Hovra över en rad i diagrammet för att ta emot begäranden för en punkt i tiden topp 3 programmen. Visar även en lista över program som tar emot begäranden och antal begäranden för den valda perioden. <br><br>Klicka på grafen för att köra en loggs ökning för <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat linje diagram över server begär Anden per timme för olika program. <br><br> Klicka på ett program i listan om du vill köra en loggs ökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> som visar en lista över begär Anden, diagram för förfrågningar över tid och varaktighet för begäran samt en lista över begär ande svars koder.   |
| Fel – misslyckade förfrågningar per timme | Visar ett linjediagram med misslyckade programbegäranden per timme. Hovra över diagrammet för att se de 3 främsta programmen med misslyckade begäranden för en punkt i tiden. Visar även en lista över program med antalet misslyckade förfrågningar för var och en. Klicka på diagrammet för att köra en loggs ökning för <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat linje diagram över misslyckade program begär Anden. <br><br>Klicka på ett objekt i listan om du vill köra en loggs ökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> som visar misslyckade förfrågningar, diagram för misslyckade förfrågningar över tid och varaktighet för begäran och en lista över misslyckade svars koder för begäran. |
| Undantag – undantag per timme | Visar ett linjediagram med undantag per timme. Hovra över diagrammet för att se de 3 främsta programmen med undantag för en punkt i tiden. Visar även en lista över program med antalet undantag för var och en. Klicka på diagrammet för att köra en loggs ökning för <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat länk diagram över undantag. <br><br>Klicka på ett objekt i listan om du vill köra en loggs ökning för <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> som visar en lista över undantag, diagram för undantag över tid och misslyckade förfrågningar, samt en lista över undantags typer.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visa Application Insights-perspektiv med loggsökning

När du klickar på ett objekt i instrumentpanelen kan du se en Application Insights-perspektiv som visas i sökningen. Perspektivet innehåller en utökad visualisering, beroende på vilken telemetrityp som valts. I så fall visualisering innehållsändringar för olika telemetrityper.

När du klickar var som helst på bladet program visas standard **program** perspektivet.

![Application Insights program perspektiv](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektivet visar en översikt över det program som du har valt.

Bladet **tillgänglighet** visar en annan perspektivvy där du kan se webb test resultat och relaterade misslyckade förfrågningar.

![Application Insights tillgänglighet perspektiv](./media/app-insights-connector/availability-blade-drill-search.png)

När du klickar någonstans i bladet **server begär Anden** eller **haverier** ändras perspektiv komponenterna för att ge dig en visualisering som är relaterad till förfrågningar.

![Bladet för Application Insights-fel](./media/app-insights-connector/server-requests-failures-drill-search.png)

När du klickar någonstans på bladet **undantag** visas en visualisering som är anpassad till undantag.

![Bladet för Application Insights-undantag](./media/app-insights-connector/exceptions-blade-drill-search.png)

Oavsett om du klickar på något av **Application Insights-anslutningsprogram** -instrumentpanelen på själva **Sök** sidan, visas Application Insights perspektivet i en Application Insights fråga. Om du till exempel visar Application Insights data, visar en **&#42;** fråga även fliken perspektiv som följande bild:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektivet komponenter uppdateras beroende på frågan. Det innebär att du kan filtrera resultatet med hjälp av valfri sökfältet som ger dig möjlighet att se data från:

- Alla dina program
- Ett enda valda program
- En grupp av program

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Växla över till en app i Azure portal

Application Insights-anslutningsprogram blad är utformade för att du ska kunna pivotera till den valda Application Insights-appen *när du använder Azure Portal*. Du kan använda lösningen övervakningsplattform på hög nivå som hjälper dig att felsöka en app. När du ser ett potentiellt problem i någon av dina anslutna program, kan du antingen fördjupa dig i den i Log Analytics-sökningen eller du kan växla över direkt till appen Application Insights.

Du kan pivotera genom att klicka på ellipserna ( **...** ) som visas i slutet av varje rad och välja **Öppna i Application Insights**.

>[!NOTE]
>**Öppna i Application Insights** är inte tillgänglig i Azure Portal.

![Öppna i Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Exemplet korrigeras data

Application Insights tillhandahåller *[provtagnings korrigering](../../azure-monitor/app/sampling.md)* som hjälper till att minska telemetri trafiken. När du aktiverar sampling i Application Insights-app kan få du ett lägre antal poster som lagras både i Application Insights och Log Analytics. Medan data konsekvens bevaras på **Application Insights-anslutningsprogram** sida och perspektiv, bör du manuellt korrigera exempel data för dina anpassade frågor.

Här är ett exempel på sampling korrigering i en sökfråga i loggen:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Fältet **antal exempel** finns i alla poster och visar antalet data punkter som posten representerar. Om du aktiverar sampling för din Application Insights-app är **antalet samplingar** större än 1. Om du vill räkna det faktiska antalet poster som ditt program genererar, summerar du antalet **exempel antal** .

Sampling påverkar endast det totala antalet poster som ditt program genererar. Du behöver inte korrigera sampling för mått fält som **RequestDuration** eller **AvailabilityDuration** eftersom dessa fält visar medelvärdet för de poster som visas.

## <a name="input-data"></a>Indata

Lösningen tar emot följande telemetrityper av data från dina anslutna Application Insights-appar:

- Tillgänglighet
- Undantag
- Begäranden
- Sidvisningar – att ta emot sidvyer-arbetsytan måste du konfigurera dina appar för att samla in informationen. Mer information finns i [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Anpassade händelser – att ta emot anpassade händelser-arbetsytan måste du konfigurera dina appar för att samla in informationen. Mer information finns i [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Data tas emot av Log Analytics från Application Insights så fort de blir tillgängliga.

## <a name="output-data"></a>Utdata

En post med en *typ* av *ApplicationInsights* skapas för varje typ av indata. ApplicationInsights poster har egenskaper som visas i följande avsnitt:

### <a name="generic-fields"></a>Allmän fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tid för posten |
| ApplicationId | Instrumenteringsnyckeln för Application Insights-app |
| ApplicationName | Namnet på Application Insights app |
| RoleInstance | ID för server-värd |
| Enhetstyp | Klientenheten |
| ScreenResolution |   |
| Kontinent | Kontinent som begäran kom från |
| Land/region | Land/region där begäran har sitt ursprung |
| Län | Provins, tillstånd eller nationella inställningar som begäran kom från |
| Ort | Stad som begäran kom från |
| isSynthetic | Anger om begäran har skapats av en användare eller med automatiserad metod. Sant = automatisk metod eller falskt = genererad användare |
| SamplingRate | Den procentandel av telemetri som genereras av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Till exempel 4 =&gt; 25% |
| IsAuthenticated | Sant eller falskt |
| OperationID | Objekt som har samma åtgärd ID som visas som relaterade objekt i portalen. Vanligtvis begäran-ID |
| ParentOperationID | ID för den överordnade åtgärden |
| OperationName |   |
| SessionId | GUID för unik identifiering session där den skapades |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Tillgänglighet-specifika fält

| Egenskap | Beskrivning |
| --- | --- |
| TelemetryType | Tillgänglighet |
| AvailabilityTestName | Namnet på webbtestet |
| AvailabilityRunLocation | Geografisk källan för http-begäran |
| AvailabilityResult | Visar resultatet av webbtestet lyckades |
| AvailabilityMessage | Meddelandet som är kopplade till webbtestet |
| AvailabilityCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 eller 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| AvailabilityDuration | Tid i millisekunder för web testets varaktighet |
| AvailabilityDurationCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
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
| ExceptionCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| ExceptionMessage | Meddelande för undantaget |
| ExceptionStack | Fullständig stack av undantag |
| ExceptionHasStack | SANT om undantag har en stack |



### <a name="request-specific-fields"></a>Begäran-specifika fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Förfrågan |
| ResponseCode | HTTP-svar som skickas till klienten |
| RequestSuccess | Anger lyckad eller misslyckad. SANT eller FALSKT. |
| RequestID | ID för att unikt identifiera begäran |
| RequestName | GET/POST + URL-bas |
| RequestDuration | Tid i sekunder, för varaktigheten för begäran |
| URL | URL för begäran inte inklusive värden |
| Värd | Web server-värden |
| URLBase | Fullständiga URL: en för begäran |
| ApplicationProtocol | Typ av protokoll som används av programmet |
| RequestCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Till exempel 4 =&gt; 25% |
| RequestDurationMin | Fältet visar varaktigheten för minsta begäran (millisekunder) för representeras datapunkter för provade poster. |
| RequestDurationMax | Det här fältet visas för provade poster i maximal varaktighet för begäran (millisekunder) för representeras datapunkter |
| RequestDurationStdDev | Det här fältet visas för provade poster standardavvikelsen mellan alla begäran varaktigheter (millisekunder) för representeras datapunkter |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Den här lösningen har inte en uppsättning exempel på loggsökningar visas på instrumentpanelen. Exempel på loggs öknings frågor med beskrivningar visas i avsnittet [visa Application Insights-anslutningsprogram information](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Ta bort anslutningen med PowerShell
Med OMS-portalen är det inte möjligt att konfigurera och ta bort befintliga anslutningar från portalen. Du kan ta bort befintliga anslutningar med följande PowerShell-skript. Du måste vara ägare eller deltagare i arbets ytan och läsaren för Application Insights resurs för att utföra den här åtgärden.

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

Du kan hämta en lista över program med hjälp av följande PowerShell-skript som anropar ett REST API-anrop. 

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
Det här skriptet kräver en Bearer-autentiseringstoken för autentisering mot Azure Active Directory. Ett sätt att hämta denna token använder en artikel i [REST API dokumentations webbplats](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Klicka på **testa** och logga in på din Azure-prenumeration. Du kan kopiera Bearer-token från **förfrågnings förhands granskningen** enligt följande bild.


![Bearer-token](media/app-insights-connector/bearer-token.png)


Du kan också hämta en lista över program som använder en logg fråga:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Nästa steg

- Använd [loggs ökningen](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerad information om dina Application Insights-appar.
