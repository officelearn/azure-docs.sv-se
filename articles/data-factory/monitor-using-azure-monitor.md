---
title: Övervaka datafabriker med Azure Monitor
description: Lär dig hur du använder Azure Monitor för att övervaka /Azure Data Factory-pipelines genom att aktivera diagnostikloggar med information från Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 8325b4ef6b89a76eeec418386cec4922cb5916b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979152"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Varna och övervaka datafabriker med hjälp av Azure Monitor

Molnprogram är komplexa och har många rörliga delar. Övervakare tillhandahåller data som hjälper dig att se till att dina program förblir igång på ett felfritt sätt. Bildskärmar hjälper dig också att undvika potentiella problem och felsöka tidigare.

Du kan använda övervakningsdata för att få djupa insikter om dina program. Den här kunskapen hjälper dig att förbättra programmets prestanda och underhåll. Det hjälper dig också att automatisera åtgärder som annars kräver manuella åtgärder.

Azure Monitor tillhandahåller infrastrukturmått och loggar på basnivå för de flesta Azure-tjänster. Azure-diagnostikloggar genereras av en resurs och tillhandahåller omfattande, frekventa data om driften av den resursen. Och Azure Data Factory skriver diagnostikloggar i Monitor.

Mer information finns i [Översikt över Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Behålla Azure Data Factory-data

Data Factory lagrar pipeline-run data för endast 45 dagar. Använd Monitor om du vill behålla dessa data under en längre tid. Med Monitor kan du dirigera diagnostikloggar för analys. Du kan också behålla dem i ett lagringskonto så att du har fabriksinformation för din valda varaktighet.

## <a name="diagnostic-logs"></a>Diagnostikloggar

* Spara dina diagnostikloggar på ett lagringskonto för granskning eller manuell inspektion. Du kan använda diagnostikinställningarna för att ange kvarhållningstiden i dagar.
* Strömma loggarna till Azure Event Hubs. Loggarna matas in i en partnertjänst eller till en anpassad analyslösning som Power BI.
* Analysera loggarna med Log Analytics.

Du kan använda ett lagringskonto eller ett namnområde för händelsehubben som inte finns i prenumerationen på resursen som avger loggar. Användaren som konfigurerar inställningen måste ha lämplig rollbaserad åtkomstkontroll (RBAC) åtkomst till båda prenumerationerna.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

### <a name="diagnostic-settings"></a>Diagnostikinställningar

Använd diagnostikinställningar för att konfigurera diagnostikloggar för icke-kompromerade resurser. Inställningarna för en resurskontroll har följande funktioner:

* De anger var diagnostikloggar skickas. Exempel är ett Azure-lagringskonto, en Azure-händelsehubb eller övervakarloggar.
* De anger vilka loggkategorier som ska skickas.
* De anger hur länge varje loggkategori ska förvaras i ett lagringskonto.
* En kvarhållning på noll dagar innebär att loggar bevaras för alltid. Annars kan värdet vara valfritt antal dagar från 1 till 2 147 483 647.
* Om bevarandeprinciper har angetts men lagring av loggar i ett lagringskonto är inaktiverat har bevarandeprinciperna ingen effekt. Det här villkoret kan till exempel inträffa när endast alternativ för händelsehubbar eller övervakarloggar är markerade.
* Bevarandeprinciper tillämpas per dag. Gränsen mellan dagar inträffar vid midnatt Coordinated Universal Time (UTC). I slutet av en dag tas loggar från dagar som ligger utanför bevarandeprincipen bort. Om du till exempel har en bevarandeprincip på en dag tas loggarna från tidigare igår bort i början av dagen.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Aktivera diagnostikloggar via AZURE Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Skapa eller uppdatera en diagnostikinställning i REST-API:et för övervakare

##### <a name="request"></a>Förfrågan

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Rubriker

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID:t för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [Använda resursgrupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ställ `Content-Type` in `application/json`sidhuvudet på .
* Ange auktoriseringshuvudet till JSON-webbtoken som du fick från Azure Active Directory (Azure AD). Mer information finns i [Autentiseringsbegäranden](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Innehåll

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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
| **lagringKontoId** |String | Resurs-ID:t för det lagringskonto som du vill skicka diagnostikloggar till. |
| **serviceBusRuleId** |String | Tjänstbussregel-ID för tjänstbussnamnområdet där du vill att eventhubbar ska skapas för diagnostikloggar för direktuppspelning. Regel-ID har `{service bus resource ID}/authorizationrules/{key name}`formatet .|
| **workspaceId** | Komplex typ | En matris med måtttidskorn och deras bevarandeprinciper. Egenskapens värde är tomt. |
|**metrics**| Parametervärden för pipelinekörningen som ska skickas till den anropade pipelinen| Ett JSON-objekt som mappar parameternamn till argumentvärden. |
| **Loggar**| Komplex typ| Namnet på en diagnostikloggkategori för en resurstyp. Om du vill hämta listan över diagnostikloggkategorier för en resurs utför du en GET-diagnostikinställningsåtgärd. |
| **Kategori**| String| En matris med loggkategorier och deras bevarandeprinciper. |
| **tidGrain** | String | Granulariteten för mått, som fångas i ISO 8601 varaktighet format. Egenskapsvärdet måste `PT1M`vara , vilket anger en minut. |
| **Aktiverat**| Boolean | Anger om insamling av mått- eller loggkategorin är aktiverad för den här resursen. |
| **retentionPolicy**| Komplex typ| Beskriver bevarandeprincipen för ett mått eller en loggkategori. Den här egenskapen används endast för lagringskonton. |
|**Dagar**| Int| Antalet dagar för att behålla mått eller loggar. Om egenskapsvärdet är 0 sparas loggarna för alltid. Den här egenskapen används endast för lagringskonton. |

##### <a name="response"></a>Svar

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Få information om diagnostikinställningar i REST-API:et för övervakare

##### <a name="request"></a>Förfrågan

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Rubriker

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID:t för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [Använda resursgrupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ställ `Content-Type` in `application/json`sidhuvudet på .
* Ange auktoriseringshuvudet till en JSON-webbtoken som du fick från Azure AD. Mer information finns i [Autentiseringsbegäranden](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Svar

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
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
Mer information finns i [Diagnostikinställningar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema för loggar och händelser

### <a name="monitor-schema"></a>Schema för bildskärm

#### <a name="activity-run-log-attributes"></a>Aktivitetskörningsloggattribut

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
   "properties":
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
| **Nivå** |String | Nivån på diagnostikloggarna. För aktivitetskörningsloggar anger du egenskapsvärdet till 4. | `4` |
| **correlationId (korrelationId)** |String | Det unika ID:et för att spåra en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tid** | String | Tiden för händelsen i tidsintervallet `YYYY-MM-DDTHH:MM:SS.00000Z`UTC-format . | `2017-06-28T21:00:27.3534352Z` |
|**aktivitetRunId**| String| ID:et för aktivitetskörningen. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| ID:et för pipelinekörningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId (resourceId)**| String | ID:et som är associerat med datafabriksresursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| String | Kategorin för diagnostikloggarna. Ange egenskapsvärdet `ActivityRuns`till . | `ActivityRuns` |
|**Nivå**| String | Nivån på diagnostikloggarna. Ange egenskapsvärdet `Informational`till . | `Informational` |
|**operationName**| String | Namnet på aktiviteten med dess status. Om aktiviteten är startpulsen är `MyActivity -`egenskapsvärdet . Om aktiviteten är slutpulsen är `MyActivity - Succeeded`egenskapsvärdet . | `MyActivity - Succeeded` |
|**pipelineName (pipelineName)**| String | Namnet på pipelinen. | `MyPipeline` |
|**aktivitetsnamn**| String | Namnet på aktiviteten. | `MyActivity` |
|**start**| String | Starttiden för aktiviteten körs i tidspans UTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**Slutet**| String | Sluttiden för aktiviteten körs i tidsintervall UTC-format. Om diagnostikloggen visar att en aktivitet har startats `1601-01-01T00:00:00Z`men ännu inte avslutats, är egenskapsvärdet . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Loggattribut för Pipeline-körning

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
| **Nivå** |String | Nivån på diagnostikloggarna. För aktivitetskörningsloggar anger du egenskapsvärdet till 4. | `4` |
| **correlationId (korrelationId)** |String | Det unika ID:et för att spåra en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tid** | String | Tiden för händelsen i tidsintervallet `YYYY-MM-DDTHH:MM:SS.00000Z`UTC-format . | `2017-06-28T21:00:27.3534352Z` |
|**runId (på)**| String| ID:et för pipelinekörningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId (resourceId)**| String | ID:et som är associerat med datafabriksresursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| String | Kategorin för diagnostikloggarna. Ange egenskapsvärdet `PipelineRuns`till . | `PipelineRuns` |
|**Nivå**| String | Nivån på diagnostikloggarna. Ange egenskapsvärdet `Informational`till . | `Informational` |
|**operationName**| String | Namnet på pipelinen tillsammans med dess status. När pipelinekörningen är klar är `Pipeline - Succeeded`egenskapsvärdet . | `MyPipeline - Succeeded`. |
|**pipelineName (pipelineName)**| String | Namnet på pipelinen. | `MyPipeline` |
|**start**| String | Starttiden för aktiviteten körs i tidspans UTC-format. | `2017-06-26T20:55:29.5007959Z`. |
|**Slutet**| String | Sluttiden för aktiviteten körs i tidsintervall UTC-format. Om diagnostikloggen visar att en aktivitet har startats `1601-01-01T00:00:00Z`men ännu inte avslutats, är egenskapsvärdet .  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | Den slutliga statusen för pipelinekörningen. Möjliga egenskapsvärden är `Succeeded` och `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Loggattribut för utlösare kör

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
| **Nivå** |String | Nivån på diagnostikloggarna. För aktivitetskörningsloggar anger du egenskapsvärdet till 4. | `4` |
| **correlationId (korrelationId)** |String | Det unika ID:et för att spåra en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tid** | String | Tiden för händelsen i tidsintervallet `YYYY-MM-DDTHH:MM:SS.00000Z`UTC-format . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId (triggerId)**| String| ID:t för utlösarkörningen. | `08587023010602533858661257311` |
|**resourceId (resourceId)**| String | ID:et som är associerat med datafabriksresursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| String | Kategorin för diagnostikloggarna. Ange egenskapsvärdet `PipelineRuns`till . | `PipelineRuns` |
|**Nivå**| String | Nivån på diagnostikloggarna. Ange egenskapsvärdet `Informational`till . | `Informational` |
|**operationName**| String | Namnet på utlösaren med dess slutliga status, vilket anger om utlösaren har avfyrats. Om pulsslagen lyckades är `MyTrigger - Succeeded`egenskapsvärdet . | `MyTrigger - Succeeded` |
|**triggerName (triggerName)**| String | Namnet på utlösaren. | `MyTrigger` |
|**triggerType (triggerType)**| String | Typ av utlösare. Möjliga egenskapsvärden är `Manual Trigger` och `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Händelsen av utlösaren. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Starttiden för utlösaren som avfyras i tidsintervallUTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | Den slutliga statusen som visar om utlösaren har avfyrats. Möjliga egenskapsvärden är `Succeeded` och `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Logga Analytics-schema

Log Analytics ärver schemat från Monitor med följande undantag:

* Den första bokstaven i varje kolumnnamn har stora bokstäver. Kolumnnamnet "correlationId" i Monitor är till exempel "CorrelationId" i Log Analytics.
* Det finns ingen "Nivå"-kolumn.
* Kolumnen dynamiska egenskaper bevaras som följande dynamiska JSON-blobtyp.

    | Kolumnen Azure Monitor | Log Analytics-kolumn | Typ |
    | --- | --- | --- |
    | $.properties. UserProperties | UserProperties | Dynamisk |
    | $.properties. Anteckningar | Anteckningar | Dynamisk |
    | $.properties. Input | Indata | Dynamisk |
    | $.properties. Produktionen | Resultat | Dynamisk |
    | $.properties. Error.errorCode | Felkod | int |
    | $.properties. Error.message | Errormessage | sträng |
    | $.properties. Fel | Fel | Dynamisk |
    | $.properties. Föregångare | Föregångare | Dynamisk |
    | $.properties. Parametrar | Parametrar | Dynamisk |
    | $.properties. SystemParametrar | SystemParametrar | Dynamisk |
    | $.properties. Taggar | Taggar | Dynamisk |
    
## <a name="metrics"></a>Mått

Med Monitor kan du få insyn i prestanda och hälsa för dina Azure-arbetsbelastningar. Den viktigaste typen av övervakardata är måttet, som också kallas prestandaräknaren. Mått genereras av de flesta Azure-resurser. Monitor innehåller flera sätt att konfigurera och använda dessa mått för övervakning och felsökning.

Azure Data Factory version 2 avger följande mått.

| **Mått**           | **Namn på måttvisning**         | **Enhet** | **Sammansättningstyp** | **Beskrivning**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceedEdRuns | Efterföljande pipeline kör mått | Antal    | Totalt                | Det totala antalet pipeline-körningar som lyckades inom ett minutfönster. |
| PipelineFailedRuns   | Misslyckade pipelinekörningar mått    | Antal    | Totalt                | Det totala antalet pipeline-körningar som misslyckades inom ett minutfönster.    |
| ActivitySucceededRuns | Efterföljande aktivitet kör mått | Antal    | Totalt                | Det totala antalet aktivitetskörningar som lyckades inom ett minutfönster.  |
| ActivityFailedRuns   | Misslyckade aktivitet kör mått    | Antal    | Totalt                | Det totala antalet aktivitetskörningar som misslyckades inom ett minutfönster.     |
| TriggerSucceededRuns | Efterföljande utlösarkörningsmått  | Antal    | Totalt                | Det totala antalet utlösarkörningar som lyckades inom ett minutfönster.   |
| TriggerFailedRuns    | Misslyckade utlösarkörningsmått     | Antal    | Totalt                | Det totala antalet utlösarkörningar som misslyckades inom ett minutfönster.      |

Om du vill komma åt måtten slutför du instruktionerna i [Azure Monitor-dataplattformen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Endast slutförda, utlösta aktivitets- och pipelinekörningshändelser avges. Pågående och sandbox/debug körningar avges **inte.** 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Övervaka datafabriksmått med Azure Monitor

Du kan använda Data Factory-integrering med Monitor för att dirigera data till Övervaka. Den här integreringen är användbar i följande scenarier:

* Du vill skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory to Monitor. Du kan skapa anpassade aviseringar på dessa frågor via Monitor.

* Du vill övervaka över datafabriker. Du kan dirigera data från flera datafabriker till en enda monitorarbetsyta.

För en sju minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurera diagnostikinställningar och arbetsyta

Skapa eller lägg till diagnostikinställningar för datafabriken.

1. Gå till Övervaka i portalen. Välj **Inställningar** > **Diagnostikinställningar**.

1. Välj den datafabrik som du vill ange en diagnostikinställning för.

1. Om det inte finns några inställningar på den valda datafabriken uppmanas du att skapa en inställning. Välj **Aktivera diagnostik**.

   ![Skapa en diagnostikinställning om det inte finns några inställningar](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Om det finns befintliga inställningar på datafabriken visas en lista över inställningar som redan har konfigurerats på datafabriken. Välj **Lägg till diagnostikinställning**.

   ![Lägga till en diagnostikinställning om det finns inställningar](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Ge din inställning ett namn, välj **Skicka till Log Analytics**och välj sedan en arbetsyta från Log Analytics **Workspace**.

    ![Namnge dina inställningar och välj en logganalysarbetsyta](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Välj **Spara**.

Efter en stund visas den nya inställningen i listan med inställningar för den här datafabriken. Diagnostikloggar strömmas till arbetsytan så snart nya händelsedata genereras. Upp till 15 minuter kan förflyter mellan när en händelse avges och när den visas i Logganalys.

* I _resursspecifikt_ läge flödar diagnostikloggar från Azure Data Factory till _ADFPipelineRun,_ _ADFTriggerRun_och _ADFActivityRun-tabeller_
* I läget för _Azure-diagnostik_ skickas diagnostikloggarna till tabellen _AzureDiagnostics_

> [!NOTE]
> Eftersom en Azure-loggtabell inte kan ha fler än 500 kolumner rekommenderar vi starkt att du väljer resursspecifikt läge. Mer information finns i [Logga analytics kända begränsningar](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installera Azure Data Factory Analytics från Azure Marketplace

![Gå till "Azure Marketplace", ange "Analytics-filter" och välj "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Information om "Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Välj **Skapa** och välj sedan **INSTÄLLNINGAR FÖR OMS-arbetsyta** och **OMS-arbetsyta**.

![Skapa en ny lösning](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Övervaka datafabriksmått

Om du installerar Azure Data Factory Analytics skapas en standarduppsättning vyer så att följande mått aktiveras:

- ADF-körningar – 1) Pipeline körs av Data Factory
 
- ADF Runs-2) Aktivitet körs av Data Factory

- ADF Runs - 3) Utlösare körs av Data Factory

- ADF-fel - 1) Topp 10 Pipeline-fel av Data Factory

- ADF-fel - 2) Topp 10 Aktivitet körs av Data Factory

- ADF-fel - 3) Topp 10 utlösarfel av Data Factory

- ADF Statistik - 1) Aktivitet körs efter typ

- ADF Statistik - 2) Utlösa körs efter typ

- ADF-statistik - 3) Max pipeline körs varaktighet

![Fönster med "Arbetsböcker (förhandsversion)" och "AzureDataFactoryAnalytics" markerat](media/data-factory-monitor-oms/monitor-oms-image6.png)

Du kan visualisera föregående mått, titta på frågorna bakom dessa mått, redigera frågorna, skapa aviseringar och vidta andra åtgärder.

![Grafisk representation av rörledningar som drivs av datafabriken"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (förhandsversion) skickar diagnostikloggar till _resursspecifika_ måltabeller. Du kan skriva frågor mot följande tabeller: _ADFPipelineRun_, _ADFTriggerRun_och _ADFActivityRun_.

## <a name="alerts"></a>Aviseringar

Logga in på Azure-portalen och välj > **Övervakaraviseringar** för att skapa aviseringar. **Monitor**

![Aviseringar i portalmenyn](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Skapa aviseringar

1. Välj **+ Ny aviseringsregel** om du vill skapa en ny avisering.

    ![Ny varningsregel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definiera aviseringsvillkoret.

    > [!NOTE]
    > Se till att välja **Alla** i **listrutan Filter efter resurstyp.**

    !["Definiera varningsvillkor" > "Välj mål", som öppnar fönstret "Välj en resurs" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definiera varningsvillkor" >" Lägg till villkor", som öppnar fönstret Konfigurera signallogik](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Fönstret "Konfigurera signaltyp"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definiera aviseringsinformationen.

    ![Aviseringsinformation](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definiera åtgärdsgruppen.

    ![Skapa en regel med "Ny åtgärdsgrupp" markerad](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Skapa en ny åtgärdsgrupp](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurera e-post, SMS, push och röst](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definiera en åtgärdsgrupp](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Nästa steg
[Övervaka och hantera pipelines programmässigt](monitor-programmatically.md)
