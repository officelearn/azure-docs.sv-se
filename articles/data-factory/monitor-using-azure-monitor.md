---
title: Övervaka data fabriker med Azure Monitor | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att övervaka Data Factory pipelines genom att aktivera diagnostikloggar med information från Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 6bad74d33f5d50bb7a35de69927bf97daad07798
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326846"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Varna och övervaka data fabriker med hjälp av Azure Monitor
Moln program är komplexa med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet hålls igång i felfritt tillstånd. Det hjälper dig också att Stave av potentiella problem eller att felsöka tidigare. Dessutom kan du använda övervaknings data för att få djupgående insikter om ditt program. Den här kunskapen kan hjälpa dig att förbättra programmets prestanda eller hanterbarhet eller automatisera åtgärder som annars skulle kräva manuell åtgärd.

Azure Monitor tillhandahåller infrastruktur mått och loggar på bas nivå för de flesta tjänster i Microsoft Azure. Mer information finns i [Översikt över övervakning](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure-diagnostikloggar är loggar som genereras av en resurs som ger omfattande, frekventa data om driften av resursen. Data Factory utdata från diagnostikloggar i Azure Monitor.

## <a name="persist-data-factory-data"></a>Behåll Data Factory data
Data Factory lagrar endast pipeline-körnings data i 45 dagar. Om du vill spara pipelines körnings data i mer än 45 dagar Azure Monitor, kan du inte bara vidarebefordra diagnostikloggar för analys, du kan spara dem i ett lagrings konto så att du har en fabriks information under den tid som du väljer.

## <a name="diagnostic-logs"></a>Diagnostikloggar

* Spara dem till ett **lagrings konto** för granskning eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av diagnostikinställningar.
* Strömma dem till **Event Hubs** för inmatning av en tjänst från tredje part eller en anpassad analys lösning som Power BI.
* Analysera dem med **Log Analytics**

Du kan använda ett lagrings konto eller ett namn område för Event Hub som inte finns i samma prenumeration som den resurs som avger loggar. Den användare som konfigurerar inställningen måste ha rätt rollbaserad åtkomst kontroll (RBAC) till båda prenumerationerna.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

### <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostikloggar för icke-Compute-resurser konfigureras med diagnostiska inställningar. Diagnostikinställningar för en resurs kontroll:

* Var diagnostikloggar skickas (lagrings konto, Event Hubs eller Azure Monitor loggar).
* Vilka logg kategorier som skickas.
* Hur länge varje logg kategori ska behållas i ett lagrings konto.
* En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
* Om bevarande principer har ställts in men lagring av loggar i ett lagrings konto är inaktiverat (till exempel endast Event Hubs eller Azure Monitor logg alternativ har marker ATS) har bevarande principerna ingen inverkan.
* Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Aktivera diagnostikloggar via REST-API: er

Skapa eller uppdatera en diagnostisk inställning i Azure Monitor REST API

**Förfrågan**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera diagnostikinställningar för. För mer information [använder du resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/manage-resource-groups-portal.md).
* Ange sidhuvudet till `application/json`. `Content-Type`
* Ange Authorization-huvudet till en JSON-webbtoken som du får från Azure Active Directory. Mer information finns i [autentisera begär Anden](../active-directory/develop/authentication-scenarios.md).

**Brödtext**
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
| storageAccountId |Sträng | Resurs-ID för det lagrings konto som du vill skicka diagnostikloggar till |
| serviceBusRuleId |Sträng | Service Bus-regelns ID för Service Bus-namnområdet som du vill ha Event Hubs skapat för för strömnings diagnostikloggar. Regel-ID: t har formatet {Service Bus Resource ID}/authorizationrules/{Key Name}.|
| workspaceId | Komplex typ | Mat ris tids kornig het och deras bevarande principer. Den här egenskapen är för närvarande tom. |
|metrics| Parameter värden för pipeline-körningen som ska skickas till den anropade pipelinen| Ett JSON-objekt som mappar parameter namn till argument värden |
| logs| Komplex typ| Namnet på en diagnostisk logg kategori för en resurs typ. Om du vill hämta en lista över diagnostiska logg kategorier för en resurs, utför först en Hämta diagnostiska inställningar-åtgärd. |
| category| Sträng| Matris med logg kategorier och deras bevarande principer |
| timeGrain | Sträng | Granularitet för mått som samlas in i ISO 8601-varaktighets format. Måste vara PT1M (en minut)|
| enabled| Boolesk | Anger om samlingen av detta mått eller vilken logg kategori som är aktive rad för den här resursen|
| Retention Policy| Komplex typ| Beskriver bevarande principen för en mått-eller logg kategori. Används endast för lagrings konto alternativ.|
| days| Int| Antal dagar att behålla måtten eller loggarna. Värdet 0 behåller loggarna oändligt. Används endast för lagrings konto alternativ. |

**Svar**

200 OK


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

Hämta information om diagnostikinställningar i Azure Monitor REST API

**Förfrågan**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera diagnostikinställningar för. För mer information använder du resurs grupper för att hantera dina Azure-resurser.
* Ange sidhuvudet till `application/json`. `Content-Type`
* Ange Authorization-huvudet till ett JSON Web Token som du får från Azure Active Directory. Mer information finns i autentisera begär Anden.

**Svar**

200 OK

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
[Mer information här](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schema för loggar & händelser

### <a name="azure-monitor-schema"></a>Azure Monitor schema

#### <a name="activity-run-logs-attributes"></a>Attribut för aktivitets körnings loggar

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
| Nivå |Sträng | Nivå för diagnostikloggar. Nivå 4 är alltid fallet för aktivitets körnings loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran från slut punkt till slut punkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelsen i TimeSpan, UTC-format`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Sträng| ID för aktivitets körning | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Sträng| ID för pipeline-körningen | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Associerat resurs-ID för Data Factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till "ActivityRuns" | `ActivityRuns` |
|level| Sträng | Nivå för diagnostikloggar. Ange den här egenskapen till "information" | `Informational` |
|operationName| Sträng |Namn på aktiviteten med status. Om statusen är start pulsslaget är `MyActivity -`det. Om statusen är slut pulsslaget är `MyActivity - Succeeded` det med slutlig status | `MyActivity - Succeeded` |
|pipelineName| Sträng | Namn på pipelinen | `MyPipeline` |
|activityName| Sträng | Namn på aktiviteten | `MyActivity` |
|Starta| Sträng | Start av aktiviteten körs i TimeSpan, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|end| Sträng | Slutet av aktiviteten körs i TimeSpan, UTC-format. Om aktiviteten inte har avslut ATS än (diagnostisk logg för en aktivitet som startas), anges standardvärdet `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Attribut för kör loggar för pipeline

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
| Nivå |Sträng | Nivå för diagnostikloggar. Nivå 4 är fallet för aktivitets körnings loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran från slut punkt till slut punkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelsen i TimeSpan, UTC-format`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Sträng| ID för pipeline-körningen | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Associerat resurs-ID för Data Factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till "PipelineRuns" | `PipelineRuns` |
|level| Sträng | Nivå för diagnostikloggar. Ange den här egenskapen till "information" | `Informational` |
|operationName| Sträng |Namn på pipelinen med status. "Pipelinen – lyckades" med slutgiltig status när pipeline-körningen har slutförts| `MyPipeline - Succeeded` |
|pipelineName| Sträng | Namn på pipelinen | `MyPipeline` |
|Starta| Sträng | Start av aktiviteten körs i TimeSpan, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|end| Sträng | Aktivitetens slut körs i TimeSpan, UTC-format. Om aktiviteten inte har avslut ATS än (diagnostisk logg för en aktivitet som startas), anges standardvärdet `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|status| Sträng | Slut status för pipeline-körningen (lyckades eller misslyckades) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Utlös körning av loggar-attribut

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
| Nivå |Sträng | Nivå för diagnostikloggar. Ange till nivå 4 för aktivitets körnings loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran från slut punkt till slut punkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelsen i TimeSpan, UTC-format`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Sträng| ID för utlösarens körning | `08587023010602533858661257311` |
|resourceId| Sträng | Associerat resurs-ID för Data Factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till "PipelineRuns" | `PipelineRuns` |
|level| Sträng | Nivå för diagnostikloggar. Ange den här egenskapen till "information" | `Informational` |
|operationName| Sträng |Namnet på utlösaren med slutgiltig status om den har startats. "Untrigger-lyckades" om pulsslaget lyckades| `MyTrigger - Succeeded` |
|triggerName| Sträng | Namn på utlösaren | `MyTrigger` |
|triggerType| Sträng | Typ av utlösare (manuell utlösare eller schema utlösare) | `ScheduleTrigger` |
|triggerEvent| Sträng | Händelse av utlösaren | `ScheduleTime - 2017-07-06T01:50:25Z` |
|Starta| Sträng | Start av Utlös ande brand i TimeSpan, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|status| Sträng | Slutgiltig status för huruvida utlösaren lyckades (lyckades eller misslyckades) | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics ärver schemat från Azure Monitor med följande undantag:

* Den första bokstaven i varje kolumn namn skrivs med versaler, till exempel *correlationId* i Azure Monitor är *correlationId* i Log Analytics.
* Kolumn *nivån* kommer att tas bort.
* *Egenskaperna* för dynamiskt kolumn kommer att bevaras som nedanstående dynamiska JSON-Blob-typ:

    | Azure Monitor kolumn | Log Analytics kolumn | type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamisk |
    | $. Properties. Anteckningar | Anteckningar | Dynamisk |
    | $. Properties. Inleveranstransport | Indata | Dynamisk |
    | $. Properties. Utdataparametrar | Output | Dynamisk |
    | $. Properties. Fel. felkod | ErrorCode | int |
    | $. Properties. Fel. meddelande | ErrorMessage | sträng |
    | $. Properties. Fels | Fel | Dynamisk |
    | $. Properties. Föregångarna | Föregångarna | Dynamisk |
    | $. Properties. Komponentparametrar | Parametrar | Dynamisk |
    | $. Properties. SystemParameters | SystemParameters | Dynamisk |
    | $. Properties. Taggen | Tags | Dynamisk |
    
## <a name="metrics"></a>Mått

Med Azure Monitor kan du använda telemetri för att få insyn i dina arbets belastningar prestanda och hälsa på Azure. Den viktigaste typen av Azure-telemetridata är måtten (kallas även prestanda räknare) som har genererats av de flesta Azure-resurser. Azure Monitor tillhandahåller flera olika sätt att konfigurera och använda dessa mått för övervakning och fel sökning.

ADFV2 avger följande mått:

| **Mått**           | **Mått visnings namn**         | **Processor** | **Sammansättnings typ** | **Beskrivning**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Slutförd pipeline kör mått | Count    | Totalt                | Totalt antal pipelines har körts i ett minut fönster |
| PipelineFailedRuns   | Misslyckad pipeline kör mått    | Count    | Totalt                | Totalt antal pipeliner som körs misslyckades inom ett minut fönster    |
| ActivitySucceededRuns | Genomförd aktivitet kör mått | Count    | Totalt                | Totalt antal misslyckade aktiviteter i ett minut fönster  |
| ActivityFailedRuns   | Misslyckad aktivitet kör mått    | Count    | Totalt                | Det totala antalet misslyckade aktiviteter i ett minut fönster     |
| TriggerSucceededRuns | Lyckade utlösare kör mått  | Count    | Totalt                | Totalt antal utlösare som körts lyckades inom ett minut fönster   |
| TriggerFailedRuns    | Misslyckad utlösare kör mått     | Count    | Totalt                | Totalt antal Utlösar körningar misslyckades inom ett minut fönster      |

Följ anvisningarna i [Azure Monitor data plattform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)för att få åtkomst till måtten.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Övervaka Data Factory mått med Azure Monitor

Du kan använda Azure Data Factory integration med Azure Monitor för att dirigera data till Azure Monitor. Den här integrationen är användbar i följande scenarier:

1.  Du vill skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory till Azure Monitor. Du kan också skapa anpassade aviseringar för dessa frågor via Azure Monitor.

2.  Du vill övervaka över data fabriker. Du kan dirigera data från flera data fabriker till en enda Azure Monitor-arbetsyta.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurera diagnostikinställningar och arbets yta

Aktivera diagnostikinställningar för din data fabrik.

1. I portalen navigerar du till Azure Monitor och klickar på **diagnostikinställningar** på menyn **Inställningar** .

2. Välj den data fabrik som du vill ange en diagnostisk inställning för.
    
3. Om det inte finns några inställningar på den data fabrik du har valt uppmanas du att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Om det finns befintliga inställningar på data fabriken visas en lista över inställningar som redan har kon figurer ATS på den här data fabriken. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Ange ett namn för inställningen och markera kryss rutan **Skicka till Log Analytics**och välj sedan en Log Analytics arbets yta.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Klicka på **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här data fabriken, och diagnostikloggar strömmas till den arbets ytan så snart nya händelse data genereras. Det kan finnas upp till 15 minuter mellan när en händelse genereras och när den visas i Log Analytics.

> [!NOTE]
> På grund av en explicit begränsning av en viss Azure logg tabell som inte har fler än 500 kolumner, **rekommenderar vi starkt att du använder resurs specifikt läge**. Mer information finns i [Log Analytics kända begränsningar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installera Azure Data Factory Analytics från Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klicka på **skapa** och välj Inställningar för arbets yta och arbets yta.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Övervaka Data Factory mått

När du installerar **Azure Data Factory Analytics** skapas en standard uppsättning vyer som möjliggör följande mått:

- ADF-körningar-1) pipeline körs med Data Factory

- ADF-körningar – 2) aktiviteter körs genom Data Factory

- ADF-körningar-3) utlösare körs av Data Factory

- ADF-fel-1) de 10 vanligaste pipeline-felen per Data Factory

- ADF-fel – 2) topp 10-aktivitet körs genom Data Factory

- ADF-fel-3) de 10 vanligaste utlösarna fel per Data Factory

- ADF-statistik-1) aktivitets körningar efter typ

- ADF-statistik – 2) utlösare körs efter typ

- ADF-statistik-3) maximal pipeline kör varaktighet

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Du kan visualisera ovanstående mått, titta på frågorna bakom dessa mått, redigera frågorna, skapa aviseringar och så vidare.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Aviseringar

Logga in på Azure Portal och klicka på **övervaka** > **aviseringar** för att skapa aviseringar.

![Aviseringar på Portal-menyn](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Skapa aviseringar

1.  Klicka på **+ ny varnings regel** för att skapa en ny avisering.

    ![Ny varnings regel](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definiera **varnings villkoret**.

    > [!NOTE]
    > Se till att markera **alla** i **resurs typen filtrera efter**.

    ![Varnings villkor, skärm 1 av 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Aviserings villkor, skärm 2 av 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Aviserings villkor, skärm 3 av 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definiera **aviserings informationen**.

    ![Aviseringsinformation](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definiera **Åtgärds gruppen**.

    ![Åtgärds grupp, skärm 1 av 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Åtgärds grupp, skärm 2 av 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Åtgärds grupp, skärm 3 av 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Åtgärds grupp, skärm 4 av 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och hantering av pipeliner med kod finns i artikeln [övervaka och hantera pipelines](monitor-programmatically.md) .
