---
title: 'Självstudier: Mata in data för diagnostik- och aktivitetsloggar i Azure Data Explorer utan en enda kodrad'
description: I den här självstudien lär du dig att mata in data till Azure Data Explorer utan en enda kodrad samt att köra frågor mot dessa data.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 39019c4b11d055aa8f550928bd677e4ce33d6252
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885351"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Självstudier: Mata in data i Azure Data Explorer utan en enda kodrad

I den här självstudien får du lära dig att mata in data för diagnostik- och aktivitetsloggar till ett Azure Data Explorer-kluster utan en enda kodrad. Med den här enkla inmatningsmetoden kan du snabbt börja köra frågor mot Azure Data Explorer för dataanalys.

I den här kursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa tabeller och inmatningsmappning i en Azure Data Explorer-databas.
> * Formatera inmatade data med hjälp av en uppdateringsprincip.
> * Skapa en [händelsehubb](/azure/event-hubs/event-hubs-about) och ansluta den till Azure Data Explorer.
> * Strömma data till en händelsehubb från [Azure Monitor-diagnostikloggar](/azure/azure-monitor/platform/diagnostic-logs-overview) och [Azure Monitor-aktivitetsloggar](/azure/azure-monitor/platform/activity-logs-overview).
> * Köra frågor mot inmatade data med hjälp av Azure Data Explorer.

> [!NOTE]
> Skapa alla resurser på samma Azureplats/-region. Det här är ett krav för Azure Monitor-diagnostikloggar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md). I den här självstudien är databasnamnet *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Azure Monitoring-dataprovider – diagnostik- och aktivitetsloggar

Visa och förstå de data som tillhandahålls av diagnostik- och aktivitetsloggar för Azure Monitoring. Vi skapar en inmatningspipeline baserat på dessa datascheman.

### <a name="diagnostic-logs-example"></a>Exempel på diagnostikloggar

Azure-dianostikloggar är mått som genereras av en Azure-tjänst som tillhandahåller data om användningen av den tjänsten. Data aggregeras med ett tidsintervall på 1 minut. Varje diagnostikloggshändelse innehåller en post. Nedan följer ett exempel på ett måtthändelseschema för Azure Data Explorer om frågevaraktighet:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Exempel på aktivitetslogg

Azure-aktivitetsloggar är loggar på prenumerationsnivå som innehåller en uppsättning poster. Loggarna ger insikt om de åtgärder som utförs på resurser i din prenumeration. Till skillnad från diagnostikloggar har en aktivitetsloggshändelse en matris med poster. Vi behöver dela den här matrisen med poster senare i självstudien. Följande är ett exempel på en aktivitetsloggshändelse för att kontrollera åtkomst:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurera inmatningspipeline i Azure Data Explorer 

Pipelineinstallationen för Azure Data Explorer innehåller olika steg som omfattar [tabellskapande och datainmatning](/azure/data-explorer/ingest-sample-data#ingest-data). Du kan även ändra, mappa och uppdatera data.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Ansluta till webbgränssnittet för Azure Data Explorer

1. I din Azure Data Explorer *AzureMonitoring*-databas väljer du **Query** (Fråga), vilket öppnar webbgränssnittet för Azure Data Explorer.

    ![Söka i data](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Skapa måltabeller

Använd webbgränssnittet för Azure Data Explorer till att skapa måltabellerna i Azure Data Explorer-databasen.

#### <a name="diagnostic-logs-table"></a>Tabell för diagnostikloggar

1. Skapa tabellen *DiagnosticLogsRecords* i databasen *AzureMonitoring* som ska ta emot diagnostikloggsposterna med hjälp av kontrollkommandot `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Välj **Kör** för att skapa tabellen.

    ![Köra fråga](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tabeller för aktivitetsloggar

Eftersom aktivitetloggens struktur inte har tabellformat behöver du ändra data och expandera varje händelse till en eller flera poster. Rådata matas in i en mellanliggande tabell, *ActivityLogsRawRecords*. Då ändras och expanderas data. Utökade data matas sedan in i tabellen *ActivityLogsRecords* med hjälp av en uppdateringsprincip. Därför behöver du skapa två separata tabeller för inmatning av aktivitetsloggar.

1. Skapa tabellen *ActivityLogsRecords* i den *AzureMonitoring*-databas som ska ta emot aktivitetsloggsposter. Kör följande Azure Data Explorer-fråga för att skapa tabellen:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Skapa den mellanliggande datatabellen *ActivityLogsRawRecords* i *AzureMonitoring*-databasen för datamanipulering:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Skapa tabellmappningar

 Dataformatet är `json`, och därför krävs datamappning. `json`-mappningen matchar varje json-sökväg till ett kolumnnamn i tabellen.

#### <a name="diagnostic-logs-table-mapping"></a>Tabellmappning för diagnostikloggar

Använd följande fråga för att mappa data till tabellen:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Tabellmappning för aktivitetsloggar

Använd följande fråga för att mappa data till tabellen:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Skapa uppdateringsprincip

1. Skapa en [funktionen](/azure/kusto/management/functions) som utökar samlingen med poster så att varje värde i samlingen tar emot en separat rad. Använd operatorn [`mvexpand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events["identity.authorization"],
            IdentityClaims = events["identity.claims"],
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Lägg till en [uppdateringsprincip](/azure/kusto/concepts/updatepolicy) i måltabellen. Den kör automatiskt frågan på nyligen inmatade data i den mellanliggande datatabellen *ActivityLogsRawRecords* och matar in resultatet i tabellen *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Skapa en namnrymd för en händelsehubb

Azure-diagnostikloggar möjliggör export av mått till ett lagringskonto eller en händelsehubb. I den här självstudien skickar vi mått via en händelsehubb. Du skapar en namnrymd för en händelsehubb och en händelsehubb för diagnostikloggar med hjälp av följande steg. Azure Monitoring skapar händelsehubben *insights-operational-logs* för aktivitetsloggar.

1. Skapa en händelsehubb med hjälp av en Azure Resource Manager-mall i Azure-portalen. Använd följande knapp för att starta distributionen. Högerklicka och välj **Öppna i nytt fönster** så att du kan följa resten av stegen i den här artikeln. Knappen **Distribuera till Azure** tar dig till Azure-portalen.

    [![Distribuera till Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Skapa en namnrymd för en händelsehubb och en händelsehubb för diagnostikloggarna.

    ![Skapande av händelsehubb](media/ingest-data-no-code/event-hub.png)

    Fyll i formuläret med följande information. Använd standardinställningarna för alla inställningar som inte visas i följande tabell.

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för händelsehubben.|
    | Resursgrupp | *test-resource-group* | Skapa en ny resursgrupp. |
    | Plats | Välj den region som bäst uppfyller dina behov. | Skapa händelsehubbens namnrymd på samma plats som andra resurser.
    | Namn på namnområde | *AzureMonitoringData* | Välj ett unikt namn som identifierar namnområdet.
    | Namn på händelsehubb | *DiagnosticLogsData* | Händelsehubben finns under namnområdet, som tillhandahåller en unik omfångscontainer. |
    | Konsumentgruppens namn | *adxpipeline* | Skapa ett konsumentgruppsnamn. Det gör att flera konsumerande program kan ha en separat vy över händelseströmmen. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Ansluta Azure Monitoring-loggar till händelsehubben

### <a name="diagnostic-logs-connection-to-event-hub"></a>Anslutning mellan diagnostikloggar och händelsehubb

Välj en resurs som du vill exportera mått från. Det finns flera resurstyper som möjliggör export av diagnostikloggar, däribland namnrymd för händelsehubb, KeyVault, IoT Hub och Azure Data Explorer-kluster. I den här självstudien använder vi Azure Data Explorer-klustret som resurs.

1. Välj ditt Kusto-kluster i Azure-portalen.

    ![Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings.png)

1. Välj **Diagnostikinställningar** på den vänstra menyn.
1. Klicka på länken **Slå på diagnostik**. Fönstret **Diagnostikinställningar**.

    ![Fönstret Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings-window.png)

1. I fönstret **Diagnostikinställningar**:
    1. Namnge dina diagnostikloggsdata: *ADXExportedData*
    1. Markera kryssrutan **AllMetrics** (valfritt).
    1. Markera kryssrutan **Strömma till en händelsehubb**.
    1. Klicka på **Konfigurera**

1. I fönstret **Välj händelsehubb** konfigurerar du export till den händelsehubb som du skapade:
    1. **Select event hub namespace** (Välj namnrymd för händelsehubb) *AzureMonitoringData* från listrutan.
    1. **Select event hub name** (Välj namn på händelsehubb) *diagnosticlogsdata* från listrutan.
    1. **Select event hub policy name** (Välj namn på händelsehubbsprincip) från listrutan.
    1. Klicka på **OK**.

1. Klicka på **Spara**. Händelsehubbens namnrymd, namn och principnamn visas i fönstret.

    ![Spara diagnostikinställningar](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Anslutning mellan aktivitetsloggar och händelsehubb

1. På den vänstra menyn i Azure-portalen väljer du **Aktivitetslogg**
1. Fönstret **Aktivitetslogg** öppnas. **Klicka på Exportera till händelsehubb**

    ![Aktivitetslogg](media/ingest-data-no-code/activity-log.png)

1. I fönstret **Exportera aktivitetslogg**:
 
    ![Exportera aktivitetslogg](media/ingest-data-no-code/export-activity-log.png)

    1. Välj din prenumeration.
    1. I listrutan **Regioner** väljer du **Markera alla**
    1. Markera kryssrutan **Exportera till en händelsehubb**.
    1. Klicka på **Select a service bus namespace** (Välj en Service Bus-namnrymd) för att öppna fönstret **Välj händelsehubb**.
    1. I fönstret **Välj händelsehubb** väljer du från listrutorna: din prenumeration, händelsens namnrymd *AzureMonitoringData* samt standardnamnet för händelsehubbens princip.
    1. Klicka på **OK**.
    1. Klicka på **Spara** längst upp på höger sida i fönstret. En händelsehubb med namnet *insights-operational-logs* skapas.

### <a name="see-data-flowing-to-your-event-hubs"></a>Se data som flödar till dina händelsehubbar

1. Vänta några minuter tills anslutningen har definierats och exporten av aktivitetslogg till händelsehubb har slutförts. Gå till händelsehubbens namnrymd för att se de händelsehubbar du skapade.

    ![Skapade händelsehubbar](media/ingest-data-no-code/event-hubs-created.png)

1. Se data som flödar till din händelsehubb:

    ![Händelsehubbsdata](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Ansluta händelsehubb till Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Dataanslutning för diagnostikloggar

1. I Azure Data Explorer-klustret *kustodocs* väljer du **Databaser** på den vänstra menyn.
1. I fönstret **Databaser** väljer du databasnamnet *AzureMonitoring*
1. Välj **Datainmatning** på menyn till vänster
1. I fönstret **Datainmatning** klickar du på **+ Lägg till dataanslutning**
1. I fönstret **Dataanslutning** anger du följande information:

    ![Händelsehubbsanslutning](media/ingest-data-no-code/event-hub-data-connection.png)

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *DiagnosticsLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | Namnområde för händelsehubb | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | Händelsehubb | *diagnosticlogsdata* | Händelsehubben som du skapade. |
    | Konsumentgrupp | *adxpipeline* | Konsumentgruppen som definierades i hubben som du skapade. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, filformat och mappning. Låt därför **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerat.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *DiagnosticLogsRecords* | Den tabell som du skapade i *AzureMonitoring*-databasen. |
    | Dataformat | *JSON* | Format i tabellen. |
    | Kolumnmappning | *DiagnosticLogsRecordsMapping* | Den mappning som du skapade i *AzureMonitoring*-databasen, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i *DiagnosticLogsRecords*.|
    | | |

1. Klicka på **Skapa**  

### <a name="activity-logs-data-connection"></a>Dataanslutning för aktivitetsloggar

Upprepa stegen i avsnittet [Dataanslutning för diagnostikloggar](#diagnostic-logs-data-connection) för att skapa dataanslutningen för aktivitetsloggar.

1. Infoga följande inställningar i fönstret **Dataanslutning**:

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *ActivityLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | Namnområde för händelsehubb | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | Händelsehubb | *insights-operational-logs* | Händelsehubben som du skapade. |
    | Konsumentgrupp | *$Default* | Den förinställda konsumentgruppen. Om det behövs kan du skapa en annan konsumentgrupp. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, filformat och mappning. Låt därför **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerat.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *ActivityLogsRawRecords* | Den tabell som du skapade i *AzureMonitoring*-databasen. |
    | Dataformat | *JSON* | Format i tabellen. |
    | Kolumnmappning | *ActivityLogsRawRecordsMapping* | Den mappning som du skapade i *AzureMonitoring*-databasen, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i *ActivityLogsRawRecords*.|
    | | |

1. Klicka på **Skapa**  

## <a name="query-the-new-tables"></a>Köra frågor mot de nya tabellerna

Du har en pipeline med dataflöde. Inmatning via klustret tar 5 minuter som standard, så låt data flöda i några minuter innan du börjar köra frågor.

### <a name="diagnostic-logs-table-query-example"></a>Exempelfråga för tabell för diagnostikloggar

Följande fråga analyserar frågevaraktighetsdata från Azure Data Explorer-diagnostikloggarnas poster:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Frågeresultat:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>Exempelfråga för tabell för aktivitetsloggar

Följande fråga analyserar data från Azure Data Explorer-aktivitetsloggarnas poster:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Frågeresultat:
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Nästa steg

Lär dig att skriva många fler frågor för de data som du extraherade från Azure Data Explorer med hjälp av följande artikel:

> [!div class="nextstepaction"]
> [Skriva frågor för Azure Data Explorer](write-queries.md)
