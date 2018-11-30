---
title: Apache Spark Structured Streaming från Apache Kafka till Azure Cosmos DB – Azure HDInsight
description: Lär dig hur du använder Apache Spark Structured Streaming för att läsa data från Apache Kafka och sedan lagra den i Azure Cosmos DB. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: b1a4354db23cdfdc6201decbb793a3f9a3ad8206
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496161"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Använda Apache Spark Structured Streaming med Apache Kafka och Azure Cosmos DB

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) att läsa data från [Apache Kafka](https://kafka.apache.org/) på Azure HDInsight och sedan lagra data i Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databas för flera datamodeller. Det här exemplet används en SQL API-databasmodell. Mer information finns i den [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md) dokumentet.

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. Läs mer om Structured Streaming, den [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]
> Det här exemplet används Apache Spark 2.2 på HDInsight 3.6.
>
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som pratar med Kafka måste finnas i samma Azure-nätverket som noder i Kafka-klustret. I det här exemplet finns både Kafka och Spark-kluster i Azure-nätverk. Följande diagram visar hur kommunikation flödar mellan kluster:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Du kan skapa ett Azure-nätverk, Kafka och Spark-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk, Kafka och Spark-kluster på Azure-prenumerationen.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Azure Resource Manager-mallen finns i GitHub-lagringsplatsen för det här projektet ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Den här mallen skapar följande resurser:

    * En Kafka på HDInsight 3.6-klustret.

    * En Apache Spark på HDInsight 3.6-klustret.

    * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren.

        > [!NOTE]
        > Det virtuella nätverket som skapas av mallen använder adressutrymmet 10.0.0.0/16.

    * En Azure Cosmos DB SQL API-databas.

    > [!IMPORTANT]
    > Anteckningsboken för Structured Streaming som används i det här exemplet kräver Apache Spark i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

2. Använd följande information för att fylla i posterna i den **anpassad distribution** avsnittet:
   
    ![Anpassade HDInsight-distribution](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Prenumeration**: Välj din Azure-prenumeration.
   
    * **Resursgrupp**: skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Cosmos DB-kontonamn**: det här värdet används som namn på Cosmos DB-kontot.

    * **Basera klusternamnet**: det här värdet används som det grundläggande namnet för Spark och Kafka-kluster. Till exempel innebär **myhdi** skapar ett Spark-kluster med namnet __spark-myhdi__ och ett Kafka-kluster med namnet **kafka myhdi**.

    * **Cluster Version**: The HDInsight-kluster av version.

        > [!IMPORTANT]
        > Det här exemplet har testats med HDInsight 3.6 och kanske inte fungerar med andra klustertyper.

    * **Kluster-användarnamnet för inloggningen**: administratörsanvändarnamn för Spark och Kafka-kluster.

    * **Kluster inloggningslösenordet**: ett administratörslösenord för Spark och Kafka-kluster.

    * **SSH-användarnamn**: SSH-användaren skapar för Spark och Kafka-kluster.

    * **SSH-lösenord**: lösenordet för SSH-användare för Spark och Kafka-kluster.

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Välj slutligen **köp**. Det tar cirka 20 minuter för att skapa kluster.

> [!IMPORTANT]
> Det kan ta upp till 45 minuter att skapa kluster, virtuella nätverk och Cosmos DB-konto.

## <a name="create-the-cosmos-db-database-and-collection"></a>Skapa Cosmos DB-databas och samling

Projektet används i det här dokumentet lagrar data i Cosmos DB. Innan du kör koden, måste du först skapa en _databasen_ och _samling_ i din Cosmos DB-instans. Måste du också hämta dokumentet slutpunkten och _nyckel_ används för att autentisera begäranden till Cosmos DB. 

Ett sätt att göra detta är att använda den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Följande skript skapar en databas med namnet `kafkadata` och en samling som heter `kafkacollection`. Den returnerar sedan den primära nyckeln.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

Dokumentet slutpunkt och primära nyckelinformationen som genereras liknar följande text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> Spara slutpunkt och nyckelvärden, eftersom de behövs i Jupyter-anteckningsböcker.

## <a name="get-the-apache-kafka-brokers"></a>Hämta Apache Kafka-meddelandeköer

Koden i det här exemplet ansluter till Kafka broker värdar i Kafka-klustret. Använd följande PowerShell- eller Bash-exempel för att hitta adresserna till de två Kafka broker värdarna:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]
> Bash-exempel förväntar sig `$CLUSTERNAME` som innehåller namnet på Kafka-klustret.
>
> Det här exemplet används den [jq](https://stedolan.github.io/jq/) verktyg för att parsa data från JSON-dokumentet.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

När du uppmanas, anger du lösenordet för klusterinloggningskontot (admin)

De utdata som genereras liknar följande text:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Spara den här informationen eftersom den används i följande avsnitt i det här dokumentet.

## <a name="get-the-notebooks"></a>Hämta de bärbara datorerna

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Ladda upp anteckningsböckerna

Använd följande steg för att ladda upp anteckningsböckerna från projektet till din Apache Spark på HDInsight-kluster:

1. Anslut till Jupyter Notebook i Apache Spark-klustret i din webbläsare. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

2. Använd från längst upp till höger på sidan, den __överför__ knappen för att ladda upp den __Stream-taxi-data-till-kafka.ipynb__ till klustret. Välj __Öppna__ för att påbörja uppladdningen.

3. Hitta den __Stream-taxi-data-till-kafka.ipynb__ posten i listan med anteckningsböcker och välj __överför__ knappen bredvid den.

4. Upprepa steg 1 – 3 för att läsa in den __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ anteckningsboken.

## <a name="load-taxi-data-into-kafka"></a>Läs in taxi-data i Kafka

När filerna har överförts, väljer den __Stream-taxi-data-till-kafka.ipynb__ post för att öppna anteckningsboken. Följ stegen i anteckningsboken för att läsa in data i Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Bearbeta taxi-data med Spark Structured Streaming

Från den [Jupyter Notebook](https://jupyter.org/) startsida, väljer den __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ posten. Följ stegen i anteckningsboken för att strömdata från Kafka och Azure Cosmos DB med Spark Structured Streaming.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Spark Structured Streaming, finns i följande dokument att lära dig mer om hur du arbetar med Apache Spark, Apache Kafka och Azure Cosmos DB:

* [Hur du använder Apache Spark-strömning (DStream) med Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter Notebook och Apache Spark i HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md)
