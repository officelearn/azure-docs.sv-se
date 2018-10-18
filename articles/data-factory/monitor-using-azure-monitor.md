---
title: Övervaka datafabriker med hjälp av Azure Monitor | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att övervaka Data Factory-pipelines genom att aktivera diagnostiska loggar med information från Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2018
ms.author: shlo
ms.openlocfilehash: 2e8c5b3d9624d3a622f16d770f68bc8614993d36
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387490"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Varna och övervaka datafabriker med hjälp av Azure Monitor
Molnprogram är komplexa med alla rörliga delar. Övervakning ger data så att ditt program fungerar och körs i ett felfritt tillstånd. Det hjälper dig också att stave potentiella problem eller felsöka tidigare sådana. Du kan dessutom använda övervakningsdata för att få djupa insikter om ditt program. Den här kunskapen kan hjälpa dig att förbättra programmets prestanda och underhåll eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure Monitor innehåller basnivå infrastruktur mått och loggar för de flesta tjänster i Microsoft Azure. Mer information finns i [Övervakningsöversikt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure diagnostikloggar är loggar som genereras av en resurs som tillhandahåller omfattande, frekventa data om användningen av den här resursen. Data Factory matar ut diagnostikloggar i Azure Monitor.

## <a name="persist-data-factory-data"></a>Bevara Data Factory-Data
Data Factory lagrar bara data för pipelinekörning 45 dagar. Om du vill bevara pipelinekörning data i mer än 45 dagar med Azure Monitor, du kan inte bara vidarebefordra diagnostikloggar för analys, du kan även spara dem till ett lagringskonto så att du har factory information för hela valfritt.

## <a name="diagnostic-logs"></a>Diagnostikloggar

* Sparar dem till en **Lagringskonto** för granskning eller manuell granskning. Du kan ange kvarhållningstid (i dagar) med hjälp av diagnostikinställningarna.
* Stream att **Händelsehubbar** för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
* Analysera dem med **Log Analytics**

Du kan använda ett lagring eller event hub-namnområde som inte är i samma prenumeration som den resurs som loggarna. Den användare som konfigurerar inställningen måste ha lämplig åtkomst med rollbaserad åtkomstkontroll (RBAC) åtkomst till båda prenumerationerna.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

### <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostikloggar för icke-beräkningsresurser konfigureras med hjälp av diagnostikinställningar. Diagnostikinställningar för en resurskontroll:

* Diagnostikloggar mottagna (Storage-konto, Händelsehubbar och/eller Log Analytics).
* Vilka loggkategorier skickas.
* Hur länge varje loggkategori ska behållas i ett lagringskonto
* En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
* Om principerna för kvarhållning har angetts men lagra loggar i ett lagringskonto är inaktiverad (till exempel bara Event Hubs eller Log Analytics alternativ har valts) principerna för kvarhållning har ingen effekt.
* Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Aktivera diagnostikloggar via REST API: er

Skapa eller uppdatera en diagnostikinställning i Azure Monitor REST API

**Förfrågan**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera diagnostikinställningar. Mer information [använda resursgrupper för att hantera dina Azure-resurser](../azure-resource-manager/resource-group-portal.md).
* Ange den `Content-Type` sidhuvud till `application/json`.
* Ange auktoriseringsrubriken till en JSON-webbtoken som hämtas från Azure Active Directory. Mer information finns i [autentisering av förfrågningar](../active-directory/develop/authentication-scenarios.md).

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| StorageAccountId |Sträng | Resurs-ID för det lagringskonto som du vill skicka diagnostikloggar |
| serviceBusRuleId |Sträng | Service bus regeln ID för service bus-namnområde som du vill ha Händelsehubbar som skapats för direktuppspelning diagnostikloggar. Regel-ID är i formatet ”: {service bus resurs-ID} /authorizationrules/ {namn}”.|
| WorkspaceId | Komplex typ | Matris med mått tid kärnor och deras bevarandeprinciper. Den här egenskapen är för närvarande är tom. |
|metrics| Parametervärdena för pipelinekörningen om du vill ska skickas till anropad pipeline| Ett JSON-objekt som mappar parameternamn till argumentvärden |
| loggar| Komplex typ| Namnet på en diagnostiklogg kategori för en resurstyp. Du kan hämta listan över diagnostiklogg kategorier för en resurs genom att utföra en åtgärd för hämtning av diagnostikinställningar först. |
| category| Sträng| Loggkategorier och deras bevarandeprinciper |
| timeGrain | Sträng | Granulariteten för mått som har hämtats i ISO 8601-format för varaktighet. Måste vara PT1M (en minut)|
| aktiverad| Boolesk | Anger om insamling av mått eller log kategorin har aktiverats för den här resursen|
| retentionPolicy| Komplex typ| Beskriver bevarandeprincipen för ett mått eller log kategori. Används för endast lagringskonto visas.|
| dagar| Int| Antal dagar att behålla mått eller loggar. Värdet 0 behåller loggarna på obestämd tid. Används för endast lagringskonto visas. |

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

Hämta information om hur du diagnostik i Azure Monitor REST API

**Förfrågan**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med resurs-ID för den resurs som du vill redigera diagnostikinställningar. Mer information groups med hjälp av Resource för att hantera dina Azure-resurser.
* Ange den `Content-Type` sidhuvud till `application/json`.
* Ange auktoriseringsrubriken till en JSON Web Token som du har fått från Azure Active Directory. Mer information finns i autentisering begäranden.

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

## <a name="schema-of-logs--events"></a>Schemat för loggar och händelser

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

| Egenskap  | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivån på diagnostikloggarna. Nivå 4 är alltid fallet för aktivitetskörning loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i tiden, UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Sträng| ID för aktiviteten kör | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Sträng| ID för pipelinekörningen | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Tillhörande resurs-ID för data factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”aktivitetskörningen” | `ActivityRuns` |
|nivå| Sträng | Nivån på diagnostikloggarna. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på aktivitet med statusen. Om statusen är start-pulsslag, är det `MyActivity -`. Om statusen är slut pulsslag, är det `MyActivity - Succeeded` med slutlig status | `MyActivity - Succeeded` |
|pipelineName| Sträng | Namn på pipelinen | `MyPipeline` |
|activityName| Sträng | Namn på aktiviteten | `MyActivity` |
|start| Sträng | Början av den aktivitet som körs i tiden, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|slut| Sträng | Slutet av den aktivitet som körs i tiden, UTC-format. Om aktiviteten inte har avslutats ännu (Diagnostisk loggning för att starta en aktivitet), används värdet `1601-01-01T00:00:00Z` har angetts.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Pipeline-körning loggar attribut

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

| Egenskap  | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivån på diagnostikloggarna. Nivå 4 gäller för aktivitetskörning loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i tiden, UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Sträng| ID för pipelinekörningen | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sträng | Tillhörande resurs-ID för data factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”PipelineRuns” | `PipelineRuns` |
|nivå| Sträng | Nivån på diagnostikloggarna. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på pipelinen med status. ”Pipeline - lyckades” med slutlig status när pipelinekörningen har slutförts| `MyPipeline - Succeeded` |
|pipelineName| Sträng | Namn på pipelinen | `MyPipeline` |
|start| Sträng | Början av den aktivitet som körs i tiden, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|slut| Sträng | Slutet av aktiviteten körs i tiden, UTC-format. Om aktiviteten inte har avslutats ännu (Diagnostisk loggning för att starta en aktivitet), används värdet `1601-01-01T00:00:00Z` har angetts.  | `2017-06-26T20:55:29.5007959Z` |
|status| Sträng | Slutlig status för pipelinekörningen (lyckades eller misslyckades) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Utlösarkörning loggar attribut

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

| Egenskap  | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| Nivå |Sträng | Nivån på diagnostikloggarna. Inställd på nivå 4 för aktivitetskörning loggar. | `4`  |
| correlationId |Sträng | Unikt ID för att spåra en viss begäran slutpunkt till slutpunkt | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sträng | Tid för händelse i tiden, UTC-format | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|Utlösarens ID| Sträng| ID för utlösaren kör | `08587023010602533858661257311` |
|resourceId| Sträng | Tillhörande resurs-ID för data factory-resursen | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sträng | Kategori för diagnostikloggar. Ange den här egenskapen till ”PipelineRuns” | `PipelineRuns` |
|nivå| Sträng | Nivån på diagnostikloggarna. Ange den här egenskapen till ”information” | `Informational` |
|operationName| Sträng |Namnet på utlösaren med slutlig status om den har utlösts. ”MyTrigger - lyckades” om pulsslag lyckades| `MyTrigger - Succeeded` |
|Utlösarnamn| Sträng | Namnet på utlösaren | `MyTrigger` |
|triggerType| Sträng | Typen av utlösare (manuell utlösare eller Schemautlösare) | `ScheduleTrigger` |
|triggerEvent| Sträng | Händelse för utlösaren | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Sträng | Början av utlösaren brand i tiden, UTC-format | `2017-06-26T20:55:29.5007959Z`|
|status| Sträng | Slutgiltiga statusen om åtgärden har utlösts (lyckades eller misslyckades) | `Succeeded`|

## <a name="metrics"></a>Mått

Azure Monitor kan du använda telemetri för att få insyn i prestanda och hälsa för arbetsbelastningar på Azure. Viktigaste är Azure telemetridata mått (kallas även prestandaräknare) skickas från de flesta Azure-resurser. Azure Monitor innehåller flera olika sätt att konfigurera och använda de här måtten för övervakning och felsökning.

ADFV2 genererar följande mått

| **Mått**           | **Metrisk visningsnamn**         | **Enhet** | **Sammansättningstyp** | **Beskrivning**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Lyckades mått för pipeline-körningar | Antal    | Totalt                | Totalt antal pipelines arbetsflödeskörningar inom ett |
| PipelineFailedRuns   | Det gick inte mått för pipeline-körningar    | Antal    | Totalt                | Totalt antal pipelines körs misslyckade inom ett    |
| ActivitySucceededRuns | Lyckades aktivitetsmått för körningar | Antal    | Totalt                | Totalt antal aktiviteten körs lyckades inom ett  |
| ActivityFailedRuns   | Det gick inte aktivitetsmått för körningar    | Antal    | Totalt                | Totalt antal aktiviteten körs misslyckade inom ett     |
| TriggerSucceededRuns | Lyckades utlösaren körs mått  | Antal    | Totalt                | Totalt antal utlösaren körs lyckades inom ett   |
| TriggerFailedRuns    | Det gick inte utlösaren körs mått     | Antal    | Totalt                | Totalt antal utlösaren körs misslyckade inom ett      |

För att komma åt mätvärden, följer du anvisningarna i artikeln- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Övervaka Data Factory mått med Azure Monitor

Du kan använda Azure Data Factory-integrering med Azure Monitor för att vidarebefordra data till Azure Monitor. Den här integreringen är användbart i följande scenarier:

1.  Vill du skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory till Azure Monitor. Du kan också skapa anpassade varningar på dessa frågor via Azure Monitor.

2.  Du vill övervaka i datafabriker. Du kan dirigera data från flera olika datafabriker för till en enda Azure Monitor-arbetsyta.

Titta på följande videoklipp för en sju minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurera diagnostikinställningar och arbetsyta

Aktivera diagnostikinställningar för din datafabrik.

1.  Välj **Azure Monitor** -> **diagnostikinställningar** -> Välj data factory -> Aktivera diagnostik.

    ![Övervakare-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Ange diagnostikinställningar inklusive konfiguration av arbetsytan.

    ![Övervakare-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installera Azure Data Factory Analytics från Azure Marketplace

![Övervakare-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Övervakare-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klicka på **skapa** och välj arbetsyta och arbetsytan inställningar.

![Övervakare-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Övervaka Datafabriksmått

Installera **Azure Data Factory Analytics** skapar en standarduppsättning med vyer som möjliggör följande mått:

- ADF körningar-1) Pipeline-körningar av Data Factory

- Aktivitetskörningar för ADF körningar-2) av Data Factory

- ADF körningar – 3) Trigger Runs av Data Factory

- ADF fel-1) översta 10 Pipeline-fel i Data Factory

- ADF fel-2) översta 10 Aktivitetskörningar av Data Factory

- ADF fel – 3) översta 10 utlösaren fel av Data Factory

- ADF statistik-1) Aktivitetskörningar efter typ

- ADF statistik-2) Trigger Runs efter typ

- ADF statistik – 3) Pipelinekörningar Max varaktighet

![Övervakare-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![Övervakare-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Du kan visualisera måtten som ovan, titta på frågorna som bakom de här måtten, redigera frågor, skapa aviseringar och så vidare.

![Övervakare-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar på mått som stöds i Data Factory. Klicka på den **aviseringar** knappen i Data Factory **övervakaren** sidan.

![Alternativ för aviseringar](media/monitor-using-azure-monitor/alerts_image1.png)

Då kommer du till den **aviseringar** sidan.

![Sidan varningar](media/monitor-using-azure-monitor/alerts_image2.png)

Du kan också logga in på Azure-portalen och klicka på **skärm –&gt; aviseringar** att nå den **aviseringar** sidan direkt.

![Aviseringar i portalen menyn](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Skapa aviseringar

1.  Klicka på **+ ny aviseringsregel** att skapa en ny avisering.

    ![Ny aviseringsregel](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definiera den **Avisera villkor**.

    > [!NOTE]
    > Se till att välja **alla** i den **filtrera efter resurstyp**.

    ![Avisera villkor, skärmen 1 av 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Avisera villkor, skärmen 2 av 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Avisera villkor, skärmen 3 av 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definiera den **aviseringsinformation**.

    ![Aviseringsinformation](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definiera den **åtgärdsgrupp**.

    ![Åtgärdsgrupp, skärm 1 av 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Åtgärdsgrupp skärmen 2 av 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Åtgärdsgrupp skärmen 3 av 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Åtgärdsgrupp skärmen 4 av 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Nästa steg
Se [övervaka och hantera pipelines programmässigt](monitor-programmatically.md) du lär dig om att övervaka och hantera pipelines genom att köra.
