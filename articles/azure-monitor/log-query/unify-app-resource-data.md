---
title: Förena flera Azure Monitor Application Insights resurser | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder en funktion i Azure Monitor loggar för att fråga flera Application Insights resurser och visualisera dessa data.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 07dd4c96ba51b1ac1e0cb2807c9e26df87a6daa7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364976"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Förena flera Azure Monitor Application Insights resurser 
I den här artikeln beskrivs hur du frågar och visar alla dina Application Insights loggdata på ett ställe, även om de finns i olika Azure-prenumerationer, som ersättning för utfasningen av Application Insights-anslutningsprogram. Antalet Application Insights-resurser som du kan ta med i en enskild fråga är begränsat till 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Rekommenderad metod för att fråga flera Application Insights resurser 
Att visa flera Application Insights resurser i en fråga kan vara besvärligt och svårt att underhålla. I stället kan du använda funktionen för att avgränsa fråge logiken från program omfånget.  

Det här exemplet visar hur du kan övervaka flera Application Insights resurser och visualisera antalet misslyckade förfrågningar efter program namn. Innan du börjar ska du köra den här frågan på arbets ytan som är ansluten till Application Insights resurser för att hämta listan över anslutna program: 

```
ApplicationInsights
| summarize by ApplicationName
```

Skapa en funktion med operatorn union med listan över program och spara sedan frågan i arbets ytan som funktion med aliaset *applicationsScoping*. 

Du kan ändra de program som visas när som helst i portalen genom att gå till Utforskaren på arbets ytan och välja funktionen för att redigera och sedan Spara eller använda `SavedSearch` PowerShell-cmdleten. 

>[!NOTE]
>Den här metoden kan inte användas med logg aviseringar eftersom åtkomst verifieringen av aviserings regel resurserna, inklusive arbets ytor och program, utförs när aviseringen skapas. Det går inte att lägga till nya resurser till funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resurs omfång i logg aviseringar måste du redigera aviserings regeln i portalen eller med en Resource Manager-mall för att uppdatera de omfångs resurserna. Du kan också inkludera listan över resurser i logg aviserings frågan.

Kommandot `withsource= SourceApp` lägger till en kolumn i resultatet som anger det program som skickade loggen. Operatorn parse är valfri i det här exemplet och använder för att extrahera program namnet från egenskapen SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Du är nu redo att använda funktionen applicationsScoping i frågan över resurser:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Frågan använder Application Insights schema, även om frågan körs i arbets ytan eftersom funktionen applicationsScoping returnerar Application Insights data strukturen. Funktions Ali Aset returnerar union av begär Anden från alla definierade program. Frågan filtrerar sedan efter misslyckade förfrågningar och visualiserar trender efter program.

![Exempel på resultat över frågor](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Fråga över Application Insights resurser och data för arbets yta 
När du stoppar anslutningen och behöver utföra frågor under ett tidsintervall som har trimmats av Application Insights data kvarhållning (90 dagar), måste du utföra [kors resurs frågor](../../azure-monitor/log-query/cross-workspace-query.md) på arbets ytan och Application Insights resurser för en mellanliggande period. Detta är förrän dina program data ackumuleras per den nya Application Insights data kvarhållning som anges ovan. Frågan kräver vissa modifieringar eftersom scheman i Application Insights och arbets ytan är olika. Se tabellen senare i det här avsnittet och markera schema skillnaderna. 

>[!NOTE]
>[Frågan över resurser](../log-query/cross-workspace-query.md) i logg aviseringar stöds i det nya [scheduledQueryRules-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor den [äldre Log Analytics varnings-API: n](../platform/api-alerts.md) för att skapa nya logg aviserings regler från Azure Portal, såvida du inte växlar från [äldre API för logg aviseringar](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API: t standardvärdet för nya varnings regler i Azure Portal och du kan skapa frågor om aviserings regler för kors resurs. Du kan skapa [frågor om kors resursfrågor](../log-query/cross-workspace-query.md) utan att göra växeln med hjälp av arm- [mallen för scheduledQueryRules-API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här varnings regeln kan hanteras även om [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure Portal.

Om anslutningen till exempel slutade fungera på 2018-11-01, när du frågar efter loggar över Application Insights resurser och program data på arbets ytan, skulle din fråga konstrueras som i följande exempel:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Schema skillnader för Application Insights och Log Analytics arbets yta
I följande tabell visas schema skillnaderna mellan Log Analytics och Application Insights.  

| Egenskaper för Log Analytics arbets yta| Application Insights resurs egenskaper|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | meddelande |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | namn |
| AvailabilityTimestamp | timestamp |
| Webbläsare | client_browser |
| Ort | client_city |
| ClientIP | client_IP |
| Dator | cloud_RoleInstance | 
| Land | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | namn | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | meddelande | 
| ExceptionType | typ |
| Åtgärds-ID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | namn | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| Begärande-ID | id | 
| RequestName | namn | 
| RequestSuccess | lyckades | 
| ResponseCode | resultCode | 
| Roll | cloud_RoleName |
| Rollinstans | cloud_RoleInstance |
| sessions-ID | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | typ |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Nästa steg

Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad information för Application Insights-appar.
