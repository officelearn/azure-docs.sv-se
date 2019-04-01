---
title: 'Självstudier: Mata in data för diagnostik- och aktivitetsloggar i Azure Data Explorer utan en enda kodrad'
description: I den här självstudien lär du dig att mata in data till Azure Data Explorer utan en enda kodrad samt att köra frågor mot dessa data.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 3/14/2019
ms.openlocfilehash: 5d6b595b442b645f57454e317e6535645f643598
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756849"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Självstudier: Mata in data i Azure Data Explorer utan en enda kodrad

I den här självstudien lär du dig att mata in data från diagnostikloggar och aktivitetsloggar till ett Azure Data Explorer-kluster utan att skriva kod. Med den här enkla inmatningsmetoden kan du snabbt börja köra frågor mot Azure Data Explorer för dataanalys.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa tabeller och inmatningsmappning i en Azure Data Explorer-databas.
> * Formatera inmatade data med hjälp av en uppdateringsprincip.
> * Skapa en [händelsehubb](/azure/event-hubs/event-hubs-about) och ansluta den till Azure Data Explorer.
> * Strömma data till en händelsehubb från [Azure Monitor-diagnostikloggar](/azure/azure-monitor/platform/diagnostic-logs-overview) och [Azure Monitor-aktivitetsloggar](/azure/azure-monitor/platform/activity-logs-overview).
> * Köra frågor mot inmatade data med hjälp av Azure Data Explorer.

> [!NOTE]
> Skapa alla resurser på samma Azure-plats eller region. Det här är ett krav för Azure Monitor-diagnostikloggar.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md). I den här självstudien är databasnamnet *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Azure Monitor-dataleverantör – diagnostikloggar och aktivitetsloggar

Visa och förstå de data som tillhandahålls av Azure Monitor diagnostik- och loggarna nedan. Vi skapar en inmatningspipeline baserat på dessa datascheman. Observera att varje händelse i en logg har en matris med poster. Den här matrisen med poster som ska delas senare under kursen.

### <a name="diagnostic-logs-example"></a>Exempel på diagnostikloggar

Azure-dianostikloggar är mått som genereras av en Azure-tjänst som tillhandahåller data om användningen av den tjänsten. Data aggregeras med ett tidsintervall på 1 minut. Här följer ett exempel på ett måtthändelseschema för Azure Data Explorer om frågevaraktighet:

```json
{
    "records": [
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
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>Exempel på aktivitetslogg

Azure-aktivitetsloggar finns prenumerationsnivå loggar som ger inblick i de åtgärder som utförs på resurser i din prenumeration. Här är ett exempel på en aktivitetsloggshändelse för att kontrollera åtkomst:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurera en inmatningspipeline i Azure Data Explorer

Konfiguration av en Azure Data Explorer-pipeline omfattar flera steg, däribland [tabellskapande och datainmatning](/azure/data-explorer/ingest-sample-data#ingest-data). Du kan även ändra, mappa och uppdatera data.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Ansluta till webbgränssnittet för Azure Data Explorer

I din Azure Data Explorer-databas *AzureMonitoring* väljer du **Query** (Fråga) för att öppna webbgränssnittet för Azure Data Explorer.

![Frågesida](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Skapa måltabellerna

Strukturen för Azure Monitor-loggar är inte tabular. Du manipulera data och expandera varje händelse till en eller flera poster. Rådata ska matas in i en mellanliggande tabell med namnet *ActivityLogsRawRecords* aktivitetsloggar och *DiagnosticLogsRawRecords* för diagnostikloggar. Då ändras och expanderas data. Med hjälp av en princip, utökade data kommer sedan att matas in i den *ActivityLogsRecords* tabell aktivitetsloggar och *DiagnosticLogsRecords* för diagnostikloggar. Det innebär att du måste skapa två separata tabeller för att föra in aktivitetsloggar och två separata tabeller för att föra in diagnostikloggar.

Använd webbgränssnittet för Azure Data Explorer till att skapa måltabellerna i Azure Data Explorer-databasen.

#### <a name="the-diagnostic-logs-table"></a>Tabellen för diagnostikloggar

1. I databasen *TestDatabase* skapar du en tabell med namnet *DiagnosticLogsRecords* för att lagra diagnostikloggposterna. Ange följande `.create table`-kontrollkommando:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Välj **Kör** för att skapa tabellen.

    ![Kör frågan](media/ingest-data-no-code/run-query.png)

1. Skapa mellanliggande tabellen med namnet *DiagnosticLogsRawRecords* i den *TestDatabase* databas för datamanipulering med följande fråga. Välj **Kör** för att skapa tabellen.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>Tabellerna för aktivitetsloggar

1. Skapa en tabell med namnet *ActivityLogsRecords* i databasen *TestDatabase* för att ta emot aktivitetsloggposter. Skapa tabellen genom att köra följande Azure Data Explorer-fråga:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Skapa den mellanliggande datatabellen med namnet *ActivityLogsRawRecords* i databasen *TestDatabase* för datamanipulering:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Skapa tabellmappningar

 Eftersom dataformatet är `json` krävs datamappning. `json`-mappningen matchar varje json-sökväg till ett kolumnnamn i tabellen.

#### <a name="table-mapping-for-diagnostic-logs"></a>Tabellmappning för diagnostikloggar

Mappa diagnostikloggarnas data till tabellen med hjälp av följande fråga:

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Tabellmappning för aktivitetsloggar

Mappa aktivitetsloggarnas data till tabellen med hjälp av följande fråga:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>Skapa uppdateringsprincip för loggdata

#### <a name="activity-log-data-update-policy"></a>Aktivitetsloggdata uppdateringsprincip

1. Skapa en [funktionen](/azure/kusto/management/functions) som utökar uppsättning loggposter för aktiviteten så att varje värde i samlingen tar emot en separat rad. Använd operatorn [`mvexpand`](/azure/kusto/query/mvexpandoperator):

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
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Lägg till [uppdateringsprincipen](/azure/kusto/concepts/updatepolicy) i måltabellen. Den här policyn kör automatiskt frågan på nyligen inmatade data i den mellanliggande datatabellen *ActivityLogsRawRecords* och matar in resultatet i tabellen *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>Diagnostiklogg data uppdateringsprincip

1. Skapa en [funktionen](/azure/kusto/management/functions) som utökar en samling diagnostiklogg poster så att varje värde i samlingen tar emot en separat rad. Använd operatorn [`mvexpand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. Lägg till [uppdateringsprincipen](/azure/kusto/concepts/updatepolicy) i måltabellen. Den här principen körs automatiskt frågan på alla nyligen insamlade data i den *DiagnosticLogsRawRecords* mellanliggande datatabell och mata in resultaten i den *DiagnosticLogsRecords* tabell:

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Skapa en Event Hubs-namnrymd

Azure-diagnostikloggar möjliggör export av mått till ett lagringskonto eller en händelsehubb. I den här självstudien skickar vi måtten via en händelsehubb. Du skapar en namnrymd för en händelsehubb och en händelsehubb för diagnostikloggarna med hjälp av följande steg. Azure Monitor skapar händelsehubben *insights-operational-logs* för aktivitetsloggarna.

1. Skapa en händelsehubb med hjälp av en Azure Resource Manager-mall i Azure-portalen. Följ resten av stegen i den här artikeln genom att högerklicka på knappen **Distribuera till Azure** och sedan välja **Öppna i nytt fönster**. Knappen **Distribuera till Azure** tar dig till Azure-portalen.

    [![Knappen Distribuera till Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Skapa en Event Hubs-namnrymd och en händelsehubb för diagnostikloggarna.

    ![Skapande av händelsehubb](media/ingest-data-no-code/event-hub.png)

1. Fyll i formuläret med följande information. För alla inställningar som inte visas i följande tabell använder du standardvärdena.

    **Inställning** | **Föreslaget värde** | **Beskrivning**
    |---|---|---|
    | **Prenumeration** | *Din prenumeration* | Välj den Azure-prenumeration som ska användas för händelsehubben.|
    | **Resursgrupp** | *test-resource-group* | Skapa en ny resursgrupp. |
    | **Plats** | Välj den region som bäst uppfyller dina behov. | Skapa Event Hubs-namnrymden på samma plats som andra resurser.
    | **Namn på namnrymd** | *AzureMonitoringData* | Välj ett unikt namn som identifierar namnområdet.
    | **Namn på händelsehubb** | *DiagnosticLogsData* | Händelsehubben finns under namnområdet, som tillhandahåller en unik omfångscontainer. |
    | **Namn på konsumentgrupp** | *adxpipeline* | Skapa ett konsumentgruppsnamn. Konsumentgrupper gör att flera konsumerande program kan ha en separat vy över händelseströmmen. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Ansluta Azure Monitor-loggar till händelsehubben

Nu behöver du ansluta dina diagnostikloggar och aktivitetsloggar till händelsehubben.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Ansluta diagnostikloggar till händelsehubben

Välj en resurs som du vill exportera mått från. Fler resurstyper stödjer export av diagnostikloggar, däribland Event Hubs-namnrymd, Azure Key Vault, Azure IoT Hub samt Azure Data Explorer-kluster. I den här självstudien använder vi ett Azure Data Explorer-kluster som resurs.

1. Välj ditt Kusto-kluster i Azure-portalen.
1. Välj **diagnostikinställningar** och välj sedan länken **Slå på diagnostik**. 

    ![Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings.png)

1. Fönstret **Diagnostikinställningar** öppnas. Utför följande steg:
   1. Ge dina diagnostikloggdata namnet *ADXExportedData*.
   1. Under **METRIC** (Mått) markerar du kryssrutan **AllMetrics** (valfritt).
   1. Markera kryssrutan **Strömma till en händelsehubb**.
   1. Välj **Konfigurera**.

      ![Fönstret Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings-window.png)

1. I fönstret **Välj händelsehubb** konfigurerar du hur data ska exporteras från diagnostikloggar till den händelsehubb som du skapade:
    1. I listan **Select event hub namespace** (Välj namnrymd för händelsehubb) väljer du *AzureMonitoringData*.
    1. I listan **Select event hub name** (Välj namn på händelsehubb) väljer du *diagnosticlogsdata*.
    1. I listan **Select event hub policy name** (Välj namn på händelsehubbsprincip) väljer du **RootManagerSharedAccessKey**.
    1. Välj **OK**.

1. Välj **Spara**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Ansluta aktivitetsloggar till händelsehubben

1. På den vänstra menyn i Azure-portalen väljer du **Aktivitetslogg**.
1. Fönstret **Aktivitetslogg** öppnas. Välj **Exportera till händelsehubb**.

    ![Fönstret Aktivitetslogg](media/ingest-data-no-code/activity-log.png)

1. Fönstret **Exportera aktivitetslogg** öppnas:
 
    ![Fönstret Exportera aktivitetslogg](media/ingest-data-no-code/export-activity-log.png)

1. I fönstret **Exportera aktivitetslogg** utför du följande steg:
      1. Välj din prenumeration.
      1. I listan **Regioner** väljer du **Markera alla**.
      1. Markera kryssrutan **Exportera till en händelsehubb**.
      1. Välj **Select a service bus namespace** (Välj en Service Bus-namnrymd) för att öppna fönstret **Välj händelsehubb**.
      1. I fönstret **Välj händelsehubb** väljer du din prenumeration.
      1. I listan **Select event hub namespace** (Välj namnrymd för händelsehubb) väljer du *AzureMonitoringData*.
      1. I listan **Select event hub policy name** (Välj namn på händelsehubbsprincip) väljer du standardnamnet på händelsehubbsprincip.
      1. Välj **OK**.
      1. I fönstrets övre vänstra hörn väljer du **Spara**.
   En händelsehubb med namnet *insights-operational-logs* skapas.

### <a name="see-data-flowing-to-your-event-hubs"></a>Se data som flödar till dina händelsehubbar

1. Vänta några minuter tills anslutningen har definierats och exporten av aktivitetslogg till händelsehubben är klar. Gå till din Event Hubs-namnrymd för att se de händelsehubbar som du skapade.

    ![Skapade händelsehubbar](media/ingest-data-no-code/event-hubs-created.png)

1. Se data som flödar till din händelsehubb:

    ![Data för händelsehubb](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Ansluta en händelsehubb till Azure Data Explorer

Nu behöver du skapa dataanslutningarna för diagnostikloggarna och aktivitetsloggarna.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Skapa dataanslutningen för diagnostikloggar

1. I det Azure Data Explorer-kluster som heter *kustodocs* väljer du **Databaser** på den vänstra menyn.
1. I fönstret **Databaser** väljer du databasen *TestDatabase*.
1. Välj **Datainmatning** på menyn till vänster.
1. I fönstret **Datainmatning** klickar du på **+ Lägg till dataanslutning**.
1. I fönstret **Dataanslutning** anger du följande information:

    ![Dataanslutning för händelsehubb](media/ingest-data-no-code/event-hub-data-connection.png)

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Namn på dataanslutning** | *DiagnosticsLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | **Namnrymd för händelsehubb** | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | **Händelsehubb** | *diagnosticlogsdata* | Händelsehubben som du skapade. |
    | **Konsumentgrupp** | *adxpipeline* | Konsumentgruppen som definierades i hubben som du skapade. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, dataformat och mappning. Låt **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerad.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Tabell** | *DiagnosticLogsRawRecords* | Den tabell som du skapade i databasen *TestDatabase*. |
    | **Dataformat** | *JSON* | Det format som används i tabellen. |
    | **Kolumnmappning** | *DiagnosticLogsRecordsMapping* | Den mappning som du skapade i databasen *TestDatabase*, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i tabellen *DiagnosticLogsRecords*.|
    | | |

1. Välj **Skapa**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Skapa dataanslutningen för aktivitetsloggar

Upprepa stegen i avsnittet Skapa dataanslutningen för diagnostikloggar för att skapa dataanslutningen för aktivitetsloggarna.

1. Använd följande inställningar i fönstret **Dataanslutning**:

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Namn på dataanslutning** | *ActivityLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | **Namnrymd för händelsehubb** | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | **Händelsehubb** | *insights-operational-logs* | Händelsehubben som du skapade. |
    | **Konsumentgrupp** | *$Default* | Den förinställda konsumentgruppen. Om det behövs kan du skapa en annan konsumentgrupp. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, dataformat och mappning. Låt **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerad.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Tabell** | *ActivityLogsRawRecords* | Den tabell som du skapade i databasen *TestDatabase*. |
    | **Dataformat** | *JSON* | Det format som används i tabellen. |
    | **Kolumnmappning** | *ActivityLogsRawRecordsMapping* | Den mappning som du skapade i databasen *TestDatabase*, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i tabellen *ActivityLogsRawRecords*.|
    | | |

1. Välj **Skapa**.  

## <a name="query-the-new-tables"></a>Köra frågor mot de nya tabellerna

Du har nu en pipeline med dataflöde. Inmatning via klustret tar 5 minuter som standard, så låt data flöda i några minuter innan du börjar köra frågor.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Ett exempel på frågekörning mot tabellen för diagnostikloggar

Följande fråga analyserar frågevaraktighetsdata från diagnostikloggposter i Azure Data Explorer:

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

### <a name="an-example-of-querying-the-activity-logs-table"></a>Ett exempel på frågekörning mot tabellen för aktivitetsloggar

Följande fråga analyserar data från aktivitetsloggposter i Azure Data Explorer:

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
