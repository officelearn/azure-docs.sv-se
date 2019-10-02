---
title: Övervaka data fabriker med Azure Monitor | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att övervaka/Azure Data Factory pipelines genom att aktivera diagnostiska loggar med information från Data Factory.
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
ms.openlocfilehash: 2a707eda6a7e32a95666dd70e196c8da3c3b7834
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815946"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Varna och övervaka data fabriker med hjälp av Azure Monitor

Moln program är komplexa och har många rörliga delar. Övervakare tillhandahåller data för att se till att dina program håller sig igång i felfritt tillstånd. Övervakare hjälper dig också att undvika potentiella problem och felsöka tidigare.

Du kan använda övervaknings data för att få djupgående insikter om dina program. Den här informationen hjälper dig att förbättra programmets prestanda och hanterbarhet. Det hjälper dig också att automatisera åtgärder som annars kräver manuell åtgärd.

Azure Monitor tillhandahåller infrastruktur mått och loggar på bas nivå för de flesta Azure-tjänster. Azure Diagnostic-loggar genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Och Azure Data Factory skriver diagnostikloggar i övervakaren.

Mer information finns i [Azure Monitor översikt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Behålla Azure Data Factory data

Data Factory lagrar pipelinen – kör data endast i 45 dagar. Använd Övervakaren om du vill behålla dessa data under en längre tid. Med övervakare kan du vidarebefordra diagnostikloggar för analys. Du kan också behålla dem i ett lagrings konto så att du har en fabriks information för din valda varaktighet.

## <a name="diagnostic-logs"></a>Diagnostikloggar

* Spara dina diagnostikloggar till ett lagrings konto för granskning eller manuell kontroll. Du kan använda diagnostikinställningar för att ange Retentions tiden i dagar.
* Strömma loggarna till Azure Event Hubs. Loggarna blir inmatade i en partner tjänst eller till en anpassad analys lösning som Power BI.
* Analysera loggarna med Log Analytics.

Du kan använda ett lagrings konto eller ett namn område för händelse hubb som inte finns i prenumerationen på den resurs som avger loggar. Den användare som konfigurerar inställningen måste ha rätt rollbaserad åtkomst kontroll (RBAC) till båda prenumerationerna.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

### <a name="diagnostic-settings"></a>Diagnostikinställningar

Använd diagnostikinställningar för att konfigurera diagnostikloggar för resurser som inte är beräknings resurser. Inställningarna för en resurs kontroll har följande funktioner:

* De anger var diagnostikloggar ska skickas. Exempel på detta är ett Azure Storage-konto, en Azure Event Hub eller övervaknings loggar.
* De anger vilka logg kategorier som skickas.
* De anger hur länge varje logg kategori ska behållas i ett lagrings konto.
* En kvarhållning av noll dagar innebär loggar hålls alltid. Annars kan värdet vara ett valfritt antal dagar från 1 till 2 147 483 647.
* Om bevarande principer har angetts men lagring av loggar i ett lagrings konto är inaktive rad, har bevarande principerna ingen påverkan. Det här tillståndet kan till exempel inträffa när endast Event Hubs eller övervaka loggar alternativ är markerade.
* Bevarande principer tillämpas per dag. Gränsen mellan dagar sker vid midnatt Coordinated Universal Time (UTC). I slutet av en dag raderas loggar från dagar som överskrider bevarande principen. Om du till exempel har en bevarande princip på en dag tas loggarna från innan igår bort i början av i dag.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Aktivera diagnostikloggar via Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Skapa eller uppdatera en diagnostisk inställning i övervakaren REST API

##### <a name="request"></a>Förfrågan

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Huvuden

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/manage-resource-groups-portal.md).
* Ange sidhuvudet till `application/json`. `Content-Type`
* Ange Authorization-huvudet till den JSON-webbtoken som du fick från Azure Active Directory (Azure AD). Mer information finns i [autentisera begär Anden](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Body

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

| Egenskap | Type | Beskrivning |
| --- | --- | --- |
| **storageAccountId** |Sträng | Resurs-ID för det lagrings konto som du vill skicka diagnostikloggar till. |
| **serviceBusRuleId** |Sträng | Service Bus-regelns ID för det namn område för Service Bus som du vill ha Event Hubs skapat för för strömning av diagnostikloggar. Regel-ID: t har `{service bus resource ID}/authorizationrules/{key name}`formatet.|
| **workspaceId** | Komplex typ | En matris med mått tids kornig het och deras bevarande principer. Egenskapens värde är tomt. |
|**metrics**| Parameter värden för pipeline-körningen som ska skickas till den anropade pipelinen| Ett JSON-objekt som mappar parameter namn till argument värden. |
| **Transaktionslogg**| Komplex typ| Namnet på en diagnostisk loggnings kategori för en resurs typ. Hämta en lista med diagnostiska logg kategorier för en resurs genom att utföra en åtgärd för att hämta diagnostiska inställningar. |
| **Kategori**| Sträng| En matris med logg kategorier och deras bevarande principer. |
| **timeGrain** | Sträng | Måttets granularitet, som samlas in i ISO 8601-varaktighets format. Egenskap svärdet måste vara `PT1M`, vilket anger en minut. |
| **aktiva**| Boolesk | Anger om insamlingen av mått-eller logg kategori har Aktiver ATS för den här resursen. |
| **Retention Policy**| Komplex typ| Beskriver bevarande principen för en mått-eller logg kategori. Den här egenskapen används endast för lagrings konton. |
|**antalet**| Int| Antalet dagar att behålla måtten eller loggarna. Om egenskap svärdet är 0 sparas loggarna alltid. Den här egenskapen används endast för lagrings konton. |

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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Hämta information om diagnostikinställningar i övervakaren REST API

##### <a name="request"></a>Förfrågan

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Huvuden

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/manage-resource-groups-portal.md).
* Ange sidhuvudet till `application/json`. `Content-Type`
* Ange Authorization-huvudet till en JSON-webbtoken som du fick från Azure AD. Mer information finns i [autentisera begär Anden](../active-directory/develop/authentication-scenarios.md).

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
Mer information finns i [diagnostikinställningar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema för loggar och händelser

### <a name="monitor-schema"></a>Övervaka schema

#### <a name="activity-run-log-attributes"></a>Aktivitet-kör logg-attribut

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

| Egenskap | Type | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet `YYYY-MM-DDTHH:MM:SS.00000Z`i TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Sträng| ID för aktivitets körningen. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Sträng| ID för pipeline-körningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resurs-ID**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `ActivityRuns`. | `ActivityRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational`. | `Informational` |
|**OperationName**| Sträng | Namnet på aktiviteten med dess status. Om aktiviteten är start pulsslaget är `MyActivity -`egenskap svärdet. Om aktiviteten är slut pulsslaget är `MyActivity - Succeeded`egenskap svärdet. | `MyActivity - Succeeded` |
|**pipelineName**| Sträng | Namnet på pipelinen. | `MyPipeline` |
|**activityName**| Sträng | Aktivitetens namn. | `MyActivity` |
|**start**| Sträng | Start tiden för aktiviteten körs i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**ändamål**| Sträng | Slut tiden för aktiviteten körs i TimeSpan UTC-format. Om Diagnostic-loggen visar att en aktivitet har startat men ännu inte har avslut ATS, `1601-01-01T00:00:00Z`är egenskap svärdet. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Pipeline-kör logg-attribut

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

| Egenskap | Type | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet `YYYY-MM-DDTHH:MM:SS.00000Z`i TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Sträng| ID för pipeline-körningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resurs-ID**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `PipelineRuns`. | `PipelineRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational`. | `Informational` |
|**OperationName**| Sträng | Namnet på pipelinen tillsammans med dess status. När pipeline-körningen är färdig är `Pipeline - Succeeded`egenskap svärdet. | `MyPipeline - Succeeded`. |
|**pipelineName**| Sträng | Namnet på pipelinen. | `MyPipeline` |
|**start**| Sträng | Start tiden för aktiviteten körs i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`. |
|**ändamål**| Sträng | Slut tiden för aktiviteten körs i TimeSpan UTC-format. Om Diagnostic-loggen visar att en aktivitet har startat men ännu inte har avslut ATS `1601-01-01T00:00:00Z`, är egenskap svärdet.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| Sträng | Slut status för pipeline-körningen. Möjliga egenskaps värden `Succeeded` är `Failed`och. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Utlös läge-körning av log-attribut

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

| Egenskap | Type | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet `YYYY-MM-DDTHH:MM:SS.00000Z`i TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Sträng| ID: t för utlösarens körning. | `08587023010602533858661257311` |
|**Resurs-ID**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `PipelineRuns`. | `PipelineRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational`. | `Informational` |
|**OperationName**| Sträng | Namnet på utlösaren med dess slutgiltiga status, som anger om utlösaren har utlösts. Om pulsslaget lyckades är `MyTrigger - Succeeded`egenskap svärdet. | `MyTrigger - Succeeded` |
|**triggerName**| Sträng | Namnet på utlösaren. | `MyTrigger` |
|**triggerType**| Sträng | Typ av utlösare. Möjliga egenskaps värden `Manual Trigger` är `Schedule Trigger`och. | `ScheduleTrigger` |
|**triggerEvent**| Sträng | Händelse av utlösaren. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Sträng | Start tiden för utlösaren utlöses i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**status**| Sträng | Slutgiltig status visar om utlösaren har utlösts. Möjliga egenskaps värden `Succeeded` är `Failed`och. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics ärver schemat från övervakaren med följande undantag:

* Den första bokstaven i varje kolumn namn är kapitaliserad. Kolumn namnet "correlationId" i övervakaren är till exempel "CorrelationId" i Log Analytics.
* Det finns ingen "nivå"-kolumn.
* Den dynamiska "Properties"-kolumnen bevaras som följande dynamiska JSON-Blob-typ.

    | Azure Monitor kolumn | Log Analytics kolumn | type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamisk |
    | $. Properties. Anteckningar | Anteckningar | Dynamisk |
    | $. Properties. Inleveranstransport | Indata | Dynamisk |
    | $. Properties. Utdataparametrar | Output | Dynamisk |
    | $. Properties. Fel. felkod | Felkod | int |
    | $. Properties. Fel. meddelande | ErrorMessage | sträng |
    | $. Properties. Fels | Fel | Dynamisk |
    | $. Properties. Föregångarna | Föregångarna | Dynamisk |
    | $. Properties. Komponentparametrar | Parametrar | Dynamisk |
    | $. Properties. SystemParameters | SystemParameters | Dynamisk |
    | $. Properties. Taggen | Tags | Dynamisk |
    
## <a name="metrics"></a>Mått

Med övervakaren kan du få insyn i prestanda och hälsa för dina Azure-arbetsbelastningar. Den viktigaste typen av övervaknings data är måttet, som även kallas för prestanda räknaren. Måtten genereras av de flesta Azure-resurser. Övervakaren tillhandahåller flera olika sätt att konfigurera och använda dessa mått för övervakning och fel sökning.

Azure Data Factory version 2 avger följande mått.

| **Mått**           | **Mått visnings namn**         | **Processor** | **Sammansättnings typ** | **Beskrivning**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Slutförd pipeline kör mått | Count    | Totalt                | Det totala antalet pipeline-körningar som lyckades inom ett minut fönster. |
| PipelineFailedRuns   | Misslyckad pipeline kör mått    | Count    | Totalt                | Det totala antalet pipelines körningar som misslyckats inom ett minut fönster.    |
| ActivitySucceededRuns | Genomförd aktivitet kör mått | Count    | Totalt                | Det totala antalet aktivitets körningar som lyckades inom ett minut fönster.  |
| ActivityFailedRuns   | Misslyckad aktivitet kör mått    | Count    | Totalt                | Det totala antalet aktivitets körningar som misslyckades inom en minut period.     |
| TriggerSucceededRuns | Lyckade utlösare kör mått  | Count    | Totalt                | Det totala antalet Utlös ande körningar som lyckades inom en minut period.   |
| TriggerFailedRuns    | Misslyckad utlösare kör mått     | Count    | Totalt                | Det totala antalet Utlös ande körningar som misslyckats inom en minut period.      |

Följ anvisningarna i [Azure Monitor data plattform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)för att få åtkomst till måtten.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Övervaka Data Factory mått med Azure Monitor

Du kan använda Data Factory-integrering med övervakaren för att dirigera data som ska övervakas. Den här integrationen är användbar i följande scenarier:

* Du vill skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory som ska övervakas. Du kan skapa anpassade aviseringar för dessa frågor via övervakaren.

* Du vill övervaka över data fabriker. Du kan dirigera data från flera data fabriker till en arbets yta för en övervakare.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurera diagnostikinställningar och arbets yta

Skapa eller Lägg till diagnostikinställningar för din data fabrik.

1. I portalen går du till övervaka. Välj **Inställningar** > **diagnostikinställningar**.

1. Välj den data fabrik som du vill ange en diagnostisk inställning för.

1. Om det inte finns några inställningar på den valda data fabriken uppmanas du att skapa en inställning. Välj **slå på diagnostik**.

   ![Skapa en diagnostisk inställning om inga inställningar finns](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Om det finns befintliga inställningar på data fabriken visas en lista över inställningar som redan har kon figurer ATS på data fabriken. Välj **Lägg till diagnostisk inställning**.

   ![Lägg till en diagnostisk inställning om det finns inställningar](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Ange ett namn, Välj **Skicka till Log Analytics**och välj sedan en arbets yta från **Log Analytics arbets yta**.

    ![Namnge dina inställningar och välj en arbets yta för Log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Välj **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här data fabriken. Diagnostikloggar strömmas till den arbets ytan så snart som nya händelse data genereras. Upp till 15 minuter kan förflyta mellan när en händelse genereras och när den visas i Log Analytics.

* I _resursvyn_ läge loggar diagnostikloggar från Azure Data Factory flöda till _ADFPipelineRun_-, _ADFTriggerRun_-och _ADFActivityRun_ -tabeller
* I _Azure-diagnostikläge-_ läge flödar diagnostikloggar till _AzureDiagnostics_ -tabellen

> [!NOTE]
> Eftersom en Azure-loggfil inte kan ha fler än 500 kolumner rekommenderar vi starkt att du väljer resurs-/regionsspecifika läge. Mer information finns i [Log Analytics kända begränsningar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installera Azure Data Factory Analytics från Azure Marketplace

![Gå till "Azure Marketplace", ange "Analytics filter" och välj "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Information om "Azure Data Factory Analytics (för hands version)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Välj **skapa** och välj sedan **OMS-arbetsyta** och **Inställningar för OMS-arbetsyta**.

![Skapa en ny lösning](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Övervaka Data Factory mått

När du installerar Azure Data Factory Analytics skapas en standard uppsättning vyer så att följande mått blir aktiverade:

- ADF-körningar-1) pipeline körs med Data Factory
 
- ADF-körningar – 2) aktiviteter körs genom Data Factory

- ADF-körningar-3) utlösare körs av Data Factory

- ADF-fel-1) de 10 vanligaste pipeline-felen per Data Factory

- ADF-fel – 2) topp 10-aktivitet körs genom Data Factory

- ADF-fel-3) de 10 vanligaste utlösarna fel per Data Factory

- ADF-statistik-1) aktivitets körningar efter typ

- ADF-statistik – 2) utlösare körs efter typ

- ADF-statistik-3) maximal pipeline kör varaktighet

![Fönster med "arbets böcker (förhands granskning)" och "AzureDataFactoryAnalytics" markerat](media/data-factory-monitor-oms/monitor-oms-image6.png)

Du kan visualisera föregående mått, titta på frågorna bakom dessa mått, redigera frågorna, skapa aviseringar och vidta andra åtgärder.

![Grafisk representation av pipelines körs av Data Factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (för hands version) skickar diagnostikloggar till _resursbaserade_ mål tabeller. Du kan skriva frågor mot följande tabeller: _ADFPipelineRun_, _ADFTriggerRun_och _ADFActivityRun_.

## <a name="alerts"></a>Aviseringar

Logga in på Azure Portal och välj **övervaka** > **aviseringar** för att skapa aviseringar.

![Aviseringar på Portal-menyn](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Skapa aviseringar

1. Välj **+ ny varnings regel** för att skapa en ny avisering.

    ![Ny aviseringsregel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definiera varnings villkoret.

    > [!NOTE]
    > Se till att markera **alla** i list rutan **Filtrera efter resurs typ** .

    !["Definiera aviserings villkor" > "Välj mål", som öppnar fönstret "Välj en resurs" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definiera aviserings villkor" > Lägg till kriterier, som öppnar fönstret "Konfigurera signal logik"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Fönstret "Konfigurera signal typ"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definiera Aviseringsinformationen.

    ![Aviseringsinformation](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definiera åtgärds gruppen.

    ![Skapa en regel med "ny åtgärds grupp" markerad](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Skapa en ny åtgärds grupp](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurera e-post, SMS, push och röst](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definiera en åtgärds grupp](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Nästa steg
[Övervaka och hantera pipelines program mässigt](monitor-programmatically.md)
