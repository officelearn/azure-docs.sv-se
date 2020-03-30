---
title: Förena flera Azure Monitor Application Insights-resurser | Microsoft-dokument
description: Den här artikeln innehåller information om hur du använder en funktion i Azure Monitor Loggar för att fråga flera Application Insights-resurser och visualisera dessa data.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137500"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Förena flera Azure Monitor Application Insights-resurser 
I den här artikeln beskrivs hur du frågar och visar alla dina Application Insights-loggdata på ett ställe, även när de finns i olika Azure-prenumerationer, som en ersättning för utfasningen av Application Insights Connector. Antalet Application Insights-resurser som du kan inkludera i en enda fråga är begränsat till 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Rekommenderad metod för att fråga flera application insights-resurser 
Det kan vara besvärligt och svårt att underhålla flera Application Insights-resurser i en fråga. I stället kan du använda funktionen för att skilja frågelogiken från programens omfång.  

Det här exemplet visar hur du kan övervaka flera Application Insights-resurser och visualisera antalet misslyckade begäranden efter programnamn.

Skapa en funktion med hjälp av unionsoperatorn med listan över program och spara sedan frågan på arbetsytan som funktion med *aliasprogrammenScoping*. 

Du kan när som helst ändra de listade programmen i portalen genom att navigera till Frågeutforskaren på `SavedSearch` arbetsytan och välja funktionen för redigering och sedan spara eller använda PowerShell-cmdlet. 

>[!NOTE]
>Den här metoden kan inte användas med loggaviseringar eftersom åtkomstvalidering av aviseringsregelresurserna, inklusive arbetsytor och program, utförs vid tidpunkten för att skapa aviseringar. Det går inte att lägga till nya resurser i funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resursomfång i loggaviseringar måste du redigera aviseringsregeln i portalen eller med en Resource Manager-mall för att uppdatera de begränsade resurserna. Du kan också inkludera listan över resurser i loggaviseringsfrågan.

Kommandot `withsource= SourceApp` lägger till en kolumn i resultaten som anger det program som skickade loggen. Parsa-operatorn är valfri i det här exemplet och används för att extrahera programnamnet från SourceApp-egenskapen. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Du är nu redo att använda programScoping-funktionen i resursövergripande frågan:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Frågan använder programinsiktsschemat, även om frågan körs på arbetsytan eftersom funktionen programsScoping returnerar datastrukturen Application Insights. Funktionsaliaset returnerar union av begäranden från alla definierade program. Frågan filtrerar sedan efter misslyckade begäranden och visualiserar trenderna efter program.

![Exempel på resultat kryss](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Fråga över flera resurser](../log-query/cross-workspace-query.md) i loggaviseringar stöds i det nya [schemalagdaQueryRules API:et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor det [äldre Log Analytics Alert API](../platform/api-alerts.md) för att skapa nya loggaviseringsregler från Azure-portalen, såvida du inte växlar från äldre [loggvarningar API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API:et standard för nya varningsregler i Azure-portalen och du kan skapa regler för frågeloggar mellan resurser. Du kan skapa frågeloggvarningsregler för flera resurser utan att växla genom att använda [ARM-mallen för scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här aviseringsregeln är hanterbar men [schemalagdQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure-portalen. [cross-resource query](../log-query/cross-workspace-query.md)

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Skillnader i programinsikter och logganalysarbetsyta
I följande tabell visas schemaskillnaderna mellan Log Analytics och Application Insights.  

| Egenskaper för logganalysarbetsyta| Resursegenskaper för Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Appname|
| ApplicationTypeVersion (ApplicationTypeVersion) | application_Version |
| Tillgänglighetskonto | Itemcount |
| TillgänglighetUndergång | varaktighet |
| TillgänglighetMessage | meddelande |
| TillgänglighetRunLocation | location |
| TillgänglighetTestId | id |
| TillgänglighetTestNamn | namn |
| TillgänglighetTimestamp | timestamp |
| Webbläsare | client_browser |
| Ort | client_city |
| KlientIP | client_IP |
| Dator | cloud_RoleInstance | 
| Land/region | client_CountryOrRegion | 
| CustomEventCount | Itemcount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | namn | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| Undantagsräkning | Itemcount | 
| ExceptionHandledAt | hanterasPå |
| UndantagMessage | meddelande | 
| Undantagstyp | typ |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Operativsystem | client_OS | 
| PageViewCount (SidaVisningskonto) | Itemcount |
| SidvisningUnderslag | varaktighet | 
| PageViewName (PageViewName) | namn | 
| ParentOperationID | operation_Id | 
| Begäranrring | Itemcount | 
| BegäranDuration | varaktighet | 
| RequestID | id | 
| RequestName (RequestName) | namn | 
| Begäranstrå | lyckades | 
| Svarskod | resultCode (resultatKod) | 
| Roll | cloud_RoleName |
| RollIntance | cloud_RoleInstance |
| Sessionid | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetriTYpe | typ |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Nästa steg

Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerad information för dina Application Insights-appar.
