---
title: Apache Spark & Apache Kafka med Cosmos DB - Azure HDInsight
description: Lär dig hur du använder Apache Spark Structured Streaming för att läsa data från Apache Kafka och sedan lagra den i Azure Cosmos DB. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406161"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Använd Apache Spark Structured Streaming med Apache Kafka och Azure Cosmos DB

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) för att läsa data från [Apache Kafka](https://kafka.apache.org/) på Azure HDInsight och sedan lagra data i Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databas med flera modeller. I det här exemplet används en SQL API-databasmodell. Mer information finns i välkommen [till Azure Cosmos DB-dokument.](../cosmos-db/introduction.md)

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. Mer information om strukturerad strömning finns i [programguiden för strukturerad strömning](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]  
> I det här exemplet användes Spark 2.2 på HDInsight 3.6.
>
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare över det offentliga internet. Allt som talar med Kafka måste finnas i samma virtuella Azure-nätverk som noderna i Kafka-klustret. I det här exemplet finns både Kafka- och Spark-kluster i ett virtuellt Azure-nätverk. Följande diagram visar hur kommunikationen flödar mellan klustren:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Även om du kan skapa ett virtuellt Azure-nätverk, Kafka och Spark-kluster manuellt, är det enklare att använda en Azure Resource Manager-mall. Följ följande steg för att distribuera ett virtuella Azure-nätverk, Kafka och Spark-kluster till din Azure-prenumeration.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager-mallen finns i GitHub-databasen för det här projektet ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Den här mallen skapar följande resurser:

   * En Kafka på HDInsight 3.6-klustret.

   * Ett Spark på HDInsight 3.6-kluster.

   * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren. Det virtuella nätverk som skapas av mallen använder adressutrymmet 10.0.0.0/16.

   * En Azure Cosmos DB SQL API-databas.

    > [!IMPORTANT]  
    > Anteckningsboken för Structured Streaming som används i det här exemplet kräver Apache Spark i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

1. Använd följande information för att fylla i posterna i avsnittet **Anpassad distribution:**

    |Egenskap |Värde |
    |---|---|
    |Prenumeration|Välj din Azure-prenumeration.|
    |Resursgrupp|Skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.|
    |Namn på kosmos DB-konto|Det här värdet används som namn för Cosmos DB-konto. Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |Namn på baskluster|Det här värdet används som basnamn för Spark- och Kafka-kluster. Om du till exempel anger **myhdi** skapas ett Spark-kluster med namnet __spark-myhdi__ och ett Kafka-kluster med namnet **kafka-myhdi**.|
    |Klusterversion|HDInsight-klusterversionen. Det här exemplet testas med HDInsight 3.6 och kanske inte fungerar med andra klustertyper.|
    |Användarnamn för klusterinloggning|Administratörens användarnamn för Spark- och Kafka-kluster.|
    |Lösenord för klusterinloggning|Administratörsanvändarlösenordet för Spark- och Kafka-kluster.|
    |Ssh-användarnamn|SSH-användaren som ska skapas för Spark- och Kafka-kluster.|
    |Ssh lösenord|Lösenordet för SSH-användaren för Spark- och Kafka-kluster.|

    ![Anpassade distributionsvärden för HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

1. Slutligen väljer du **Köp**. Det kan ta upp till 45 minuter att skapa kluster, virtuellt nätverk och Cosmos DB-konto.

## <a name="create-the-cosmos-db-database-and-collection"></a>Skapa Cosmos DB-databas och samling

Projektet som används i det här dokumentet lagrar data i Cosmos DB. Innan du kör koden måste du först skapa en _databas_ och _samling_ i cosmos DB-instansen. Du måste också hämta dokumentslutpunkten och _nyckeln_ som används för att autentisera begäranden till Cosmos DB.

Ett sätt att göra detta är att använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Följande skript skapar en `kafkadata` databas med `kafkacollection`namnet och en samling med namnet . Den returnerar sedan primärnyckeln.

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
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Dokumentslutpunkten och primärnyckelinformationen liknar följande text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Spara slutpunkten och nyckelvärdena, eftersom de behövs i Jupyter-anteckningsböckerna.

## <a name="get-the-notebooks"></a>Hämta anteckningsböckerna

Koden för det exempel som beskrivs [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)i det här dokumentet finns på .

## <a name="upload-the-notebooks"></a>Ladda upp anteckningsböckerna

Så här laddar du upp anteckningsböckerna från projektet till Spark i HDInsight-klustret:

1. Anslut till Jupyter Notebook i Apache Spark-klustret i din webbläsare. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

2. Från den övre högra sidan av sidan använder du __knappen Ladda upp__ för att ladda upp filen __Stream-taxi-data-to-kafka.ipynb__ till klustret. Välj __Öppna__ för att påbörja uppladdningen.

3. Leta reda på posten __Stream-taxi-data-to-kafka.ipynb__ i listan över anteckningsböcker och välj __knappen Ladda upp__ bredvid den.

4. Upprepa steg 1-3 för att läsa in den __strömma-data-från-Kafka-till-Cosmos-DB.ipynb-anteckningsboken.__

## <a name="load-taxi-data-into-kafka"></a>Ladda taxidata i Kafka

När filerna har laddats upp väljer du posten __Stream-taxi-data-to-kafka.ipynb__ för att öppna anteckningsboken. Följ stegen i anteckningsboken för att läsa in data i Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Bearbeta taxidata med Spark Structured Streaming

På startsidan för [Jupyter-anteckningsbok](https://jupyter.org/) väljer du posten __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Följ stegen i anteckningsboken för att strömma data från Kafka och till Azure Cosmos DB med Spark Structured Streaming.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Spark Structured Streaming kan du läsa följande dokument om du vill veta mer om hur du arbetar med Apache Spark, Apache Kafka och Azure Cosmos DB:

* [Så här använder du Apache Spark streaming (DStream) med Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter Notebook och Apache Spark på HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md)
