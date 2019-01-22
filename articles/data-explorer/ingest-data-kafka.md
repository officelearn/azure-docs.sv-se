---
title: 'Snabbstart: Mata in data från Kafka i Azure Data Explorer'
description: I den här snabbstarten får du lära dig hur du matar in (load) data i Azure Data Explorer från Kafka.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/19/2018
ms.openlocfilehash: b53c26f265cc5d944c8e15ae5bf436e8f71dcc2f
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352741"
---
# <a name="quickstart-ingest-data-from-kafka-into-azure-data-explorer"></a>Snabbstart: Mata in data från Kafka i Azure Data Explorer
 
Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från Kafka. Kafka är en distribuerad strömningsplattform som gör det möjligt att skapa realtidsbaserade datapipelines som på ett tillförlitligt sätt flyttar data mellan system och program.
 
## <a name="prerequisites"></a>Nödvändiga komponenter
 
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. 
 
* [Ett testkluster och en databas](create-cluster-database-portal.md)
 
* [En exempelapp](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) som genererar data och skickar dem till Kafka

* [Visual Studio 2017 version 15.3.2 eller senare](https://www.visualstudio.com/vs/) för att köra exempelappen
 
## <a name="kafka-connector-setup"></a>Konfiguration av Kafka-anslutningsappen

Kafka Connect är ett verktyg för skalbar och tillförlitlig dataströmning mellan Apache Kafka och andra system. Det gör det enkelt att snabbt definiera anslutningsappar som skickar stora datasamlingar till och från Kafka. ADX Kafka Sink fungerar som anslutningsapp från Kafka.
 
### <a name="bundle"></a>Paket

Kafka kan läsa in en `.jar` som ett plugin-program som fungerar som en anpassad anslutningsapp. För att skapa en sådan `.jar` klonar vi koden lokalt och skapar med hjälp av Maven. 

#### <a name="clone"></a>Klona

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Utveckla

Skapa lokalt med Maven för att bilda en `.jar` komplett med beroenden.

* [Ladda ned](https://www.oracle.com/technetwork/java/javase/downloads/index.html) JDK >= 1.8
* [Ladda ned](https://maven.apache.org/install.html) Maven
 

I rotkatalogen *kafka-sink-azure-kusto* kör du:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Distribuera 

Läs in plugin-programmet i Kafka. Ett distributionsexempel med docker finns på [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Detaljerad dokumentation om Kafka-anslutningsappar och hur du distribuerar dem finns på [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Exempelkonfiguration 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Skapa en måltabell i ADX
 
Skapa en tabell i ADX som Kafka kan skicka data till. Skapa tabellen i det kluster och den databas som etablerades i **Förutsättningar**.
 
1. På Azure-portalen går du till ditt kluster och väljer **Fråga**.
 
    ![Frågeprogramlänk](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Kopiera följande kommando till fönstret och välj **Kör**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Kör genereringsfråga](media/ingest-data-event-hub/run-create-query.png)
 
1. Kopiera följande kommando till fönstret och välj **Kör**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Det här kommandot mappar inkommande JSON-data till kolumnnamnen och datatyperna för tabellen (TestTable).


## <a name="generate-sample-data"></a>Generera exempeldata

Nu när Kafka-klustret har anslutits till ADX använder du den [exempelapp](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) som du laddade ned för att generera data.

### <a name="clone"></a>Klona

Klona exempelappen lokalt:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Kör appen

1. Öppna exempelapplösningen i Visual Studio.

1. I filen `Program.cs` uppdaterar du konstanten `connectionString` till Kafka-anslutningssträngen.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skapa och kör appen. Appen skickar meddelanden till Kafka-klustret och skriver ut sin status var tionde sekund.

1. När appen har skickat några meddelanden går du vidare till nästa steg.
 
## <a name="query-and-review-the-data"></a>Köra frågor mot och granska data

1. Se till att inga fel inträffar under datainmatningen:

    ```Kusto
    .show ingestion failures
    ```

1. Visa nyligen inmatade data:

    ```Kusto
    TestTable 
    | count
    ```

1. Visa innehållet i meddelandena:
 
    ```Kusto
    TestTable
    ```
 
    Resultatuppsättningen bör se ut så här:
 
    ![Meddelanderesultat](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
> [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)
