---
title: "Övervaka datafabriker med hjälp av Azure-Monitor | Microsoft Docs"
description: "Lär dig hur du använder Azure-Monitor för att övervaka Data Factory pipelines genom att aktivera diagnostiska loggar med information från Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 3d9ec6325e25477bf4ee0475caeca64b75b1f89f
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Övervaka datafabriker med hjälp av Azure-Monitor  
Molnprogram är komplicerade med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet in och körs i ett felfritt tillstånd. Det hjälper dig också att stave ut potentiella problem eller felsöka tidigare viktiga. Du kan dessutom använda övervakningsdata och få djupa insikter om ditt program. Den här kunskapen kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure-Monitor innehåller basnivån infrastruktur mått och loggar för de flesta tjänster i Microsoft Azure. Mer information finns i [översikt över övervakning](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure diagnostikloggar är loggar orsakat av en resurs som innehåller omfattande, ofta data om användningen av den här resursen. Data Factory matar ut diagnostikloggar i Azure-Monitor. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [övervaka och hantera pipelines i Data Factory version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Diagnostikloggar

* Spara dem på en **Lagringskonto** för granskning eller Manuell kontroll. Du kan ange kvarhållningstiden (i dagar) med hjälp av inställningarna för diagnostik.
* Strömma dem till **Händelsehubbar** för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.
* Analysera dem med **Operations Management Suite (OMS) logganalys**

Du kan använda en storage-konto eller händelse hubb namnområde som inte är i samma prenumeration som den resurs som avger loggar. Den användare som konfigurerar inställningen måste ha åtkomstbehörighet för rollbaserad åtkomstkontroll (RBAC) åtkomst till båda prenumerationer.

## <a name="set-up-diagnostic-logs"></a>Ställ in diagnostikloggar

### <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostikloggar för icke-beräkningsresurser konfigureras med diagnostikinställningar. Diagnostikinställningar för en resurskontroll:

* Diagnostikloggar mottagna (Storage-konto, Event Hubs och/eller OMS logganalys).
* Vilka kategorier loggen skickas.
* Hur länge varje logg kategori ska behållas i ett lagringskonto
* En kvarhållning av noll dagar innebär loggar behålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
* Om bevarandeprinciper har angetts men lagring loggar i ett storage-konto har inaktiverats (till exempel endast Händelsehubbar eller OMS alternativ har valts) bevarandeprinciper har ingen effekt.
* Bevarandeprinciper är tillämpade per dag, så i slutet av dagen (UTC) loggar från den dagen är nu utöver kvarhållning princip tas bort. Till exempel om du har en bevarandeprincip på en dag skulle i början av dagen idag loggar från dag före igår tas bort.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Aktivera diagnostikloggar via REST API: er

Skapa eller uppdatera en inställning för diagnostik i Azure-Monitor REST-API

**Förfrågan**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Rubriker**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera inställningar för diagnostik. Mer information [med resursgrupper kan du hantera Azure-resurser](../azure-resource-manager/resource-group-portal.md).
* Ange den `Content-Type` sidhuvud till `application/json`.
* Ange authorization-huvud till en JSON web token som du har fått från Azure Active Directory. Mer information finns i [för autentisering av förfrågningar](../active-directory/develop/active-directory-authentication-scenarios.md).

**Brödtext**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| StorageAccountId |Sträng | Resurs-ID för det lagringskonto som du vill skicka diagnostiska loggar |
| serviceBusRuleId |Sträng | Service bus regeln ID för service bus-namnrymd där du vill ha Händelsehubbar som skapats för strömning diagnostikloggar. Regel-ID är i formatet ”: {service bus-resurs-ID} /authorizationrules/ {namn}”.|
| WorkspaceId | Komplex typ | Matris med mått tid kärnor och deras bevarandeprinciper. Den här egenskapen är för närvarande är tom. |
|metrics| Parametervärdena för pipelinen körs för att skickas till den anropade pipelinen| Ett JSON-objekt som mappar parameternamn till argumentvärden | 
| loggar| Komplex typ| Namnet på en kategori för diagnostiska loggen för en resurstyp. Om du vill hämta listan över diagnostiska loggen kategorier för en resurs du först göra en diagnostikinställningar för GET-åtgärd. |
| category| Sträng| Matris med loggen kategorier och deras bevarandeprinciper |
| Tidskorn | Sträng | Granulariteten för mått som har hämtats i ISO 8601-format för varaktighet. Måste vara PT1M (en minut)|
| aktiverad| Booleskt värde | Anger om samling mått eller loggfil kategorin är aktiverat för den här resursen|
| retentionPolicy| Komplex typ| Beskriver bevarandeprincipen för ett mått eller loggfil kategori. Används för kontot lagringsalternativ endast.|
| dagar| int| Antal dagar att behålla loggarna eller mått. Värdet 0 behåller loggarna på obestämd tid. Används för kontot lagringsalternativ endast. |

**Svar**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Hämta information om hur du diagnostik i Azure-Monitor REST-API

**Förfrågan**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Rubriker**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera inställningar för diagnostik. För mer information med resursgrupper kan du hantera Azure-resurser.
* Ange den `Content-Type` sidhuvud till `application/json`.
* Ange en JSON Web Token som du har fått från Azure Active Directory authorization-huvud. Mer information finns i Authenticating begäranden.

**Svar**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Mer information här] (https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Schemat för loggar & händelser

### <a name="activity-run-logs-attributes"></a>Aktiviteten kör loggar attribut

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivå av diagnostiska loggar. Nivå 4 är alltid fallet för aktiviteten kör loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i timespan UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Sträng| ID för aktiviteten kör | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Sträng| ID för den pipeline som kör | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Associerad resurs-ID för data factory-resurs | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”aktivitetskörningen” | `ActivityRuns` |
|nivå| Sträng | Nivå av diagnostiska loggar. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på aktiviteten med statusen. Om statusen är start pulsslag, är det `MyActivity -`. Om statusen är slut pulsslag, är det `MyActivity - Succeeded` med slutlig status | `MyActivity - Succeeded` |
|pipelineName| Sträng | Namnet på pipeline | `MyPipeline` |
|activityName| Sträng | Namnet på aktiviteten | `MyActivity` |
|start| Sträng | Början av den aktivitet som körs i timespan UTC-format | `2017-06-26T20:55:29.5007959Z`|
|End| Sträng | Ändar av aktiviteten körs i timespan UTC-format. Om aktiviteten inte har avslutats ännu (Diagnostisk loggning för att starta en aktivitet) standardvärdet `1601-01-01T00:00:00Z` har angetts.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Pipeline kör loggar attribut

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivå av diagnostiska loggar. Nivå 4 är fallet för aktiviteten kör loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i timespan UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Sträng| ID för den pipeline som kör | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Associerad resurs-ID för data factory-resurs | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”PipelineRuns” | `PipelineRuns` |
|nivå| Sträng | Nivå av diagnostiska loggar. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på rörledningen med status. ”Pipeline - lyckades” med slutlig status när pipeline kör har slutförts| `MyPipeline - Succeeded` |
|pipelineName| Sträng | Namnet på pipeline | `MyPipeline` |
|start| Sträng | Början av den aktivitet som körs i timespan UTC-format | `2017-06-26T20:55:29.5007959Z`|
|End| Sträng | Slutet av aktiviteten körs i timespan UTC-format. Om aktiviteten inte har avslutats ännu (Diagnostisk loggning för att starta en aktivitet) standardvärdet `1601-01-01T00:00:00Z` har angetts.  | `2017-06-26T20:55:29.5007959Z` |
|status| Sträng | Slutlig status i pipeline kör (lyckades eller misslyckades) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Utlösaren kör loggar attribut

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivå av diagnostiska loggar. Ange på nivå 4 för aktiviteten kör loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i timespan UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|Utlösarens ID| Sträng| ID för utlösaren kör | `08587023010602533858661257311` |
|resourceId| Sträng | Associerad resurs-ID för data factory-resurs | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”PipelineRuns” | `PipelineRuns` |
|nivå| Sträng | Nivå av diagnostiska loggar. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på utlösaren med slutlig status om det har utlösts. ”MyTrigger - lyckades” om pulsslag lyckades| `MyTrigger - Succeeded` |
|Utlösarnamn| Sträng | Namnet på utlösaren | `MyTrigger` |
|triggerType| Sträng | Typen av utlösare (manuell utlösare eller schema-utlösare) | `ScheduleTrigger` |
|triggerEvent| Sträng | Händelse för utlösaren | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Sträng | Start av utlösare brand i timespan UTC-format | `2017-06-26T20:55:29.5007959Z`|
|status| Sträng | Slutlig status om åtgärden har utlösts (lyckades eller misslyckades) | `Succeeded`|

### <a name="metrics"></a>Mått

Azure-Monitor kan du använda telemetri för att få insyn i prestanda och hälsotillståndet för dina arbetsbelastningar i Azure. Viktigaste är Azure telemetridata mätvärdena (kallas även prestandaräknare) sänds av mest Azure-resurser. Azure-Monitor finns flera sätt att konfigurera och använda de här måtten för övervakning och felsökning.

ADFV2 genererar följande mått

| **Mått**           | **Mått visningsnamn**         | **Enhet** | **Sammansättningstyp** | **Beskrivning**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Pipelinen körs mått är klar | Antal    | Totalt                | Totalt antal pipelines körs har slutförts inom en minut fönster |
| PipelineFailedRuns   | Det gick inte pipelinen körs mått    | Antal    | Totalt                | Totalt antal pipelines körs misslyckades inom en minut fönster    |
| ActiviySucceededRuns | Lyckades aktiviteten körs mått | Antal    | Totalt                | Totalt antal aktiviteten körs har slutförts inom en minut period  |
| ActivityFailedRuns   | Det gick inte aktiviteten körs mått    | Antal    | Totalt                | Totalt antal aktiviteten körs misslyckades inom en minut period     |
| TriggerSucceededRuns | Lyckades utlösaren körs mått  | Antal    | Totalt                | Totalt antal trigger kör har slutförts inom en minut period   |
| TriggerFailedRuns    | Det gick inte utlösaren körs mått     | Antal    | Totalt                | Totalt antal trigger kör misslyckades inom en minut period      |

För att komma åt mätvärdena som följer du anvisningarna i artikeln - https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>Nästa steg
Se [övervaka och hantera pipelines programmässigt](monitor-programmatically.md) artikeln innehåller information om att övervaka och hantera pipelines genom att köra. 