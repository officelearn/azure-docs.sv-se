---
title: Apache Spark data från Apache Kafka till Azure Cosmos DB – Azure HDInsight
description: Lär dig hur du använder Apache Spark strukturerad strömning för att läsa data från Apache Kafka och sedan lagra dem i Azure Cosmos DB. I det här exemplet strömmas data med hjälp av en Jupyter Notebook från Apache Spark på HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: b95af36ed8f8b6c9749711e2ebbf6ef8aa88f001
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885893"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Använd Apache Spark strukturerad strömning med Apache Kafka och Azure Cosmos DB

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) [strukturerad strömning](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) för att läsa data från [Apache Kafka](https://kafka.apache.org/) i Azure HDInsight och sedan lagra data i Azure Cosmos dB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databas för flera modeller. I det här exemplet används en SQL-API-databas modell. Mer information finns i [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md) Document.

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. Mer information om strukturerad strömning finns i [programmerings guiden för strukturerad strömning](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]  
> I det här exemplet användes Spark 2,2 på HDInsight 3,6.
>
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som pratar med Kafka måste finnas i samma virtuella Azure-nätverk som noderna i Kafka-klustret. I det här exemplet finns både Kafka-och Spark-klustren i ett virtuellt Azure-nätverk. Följande diagram visar hur kommunikations flöden mellan klustren:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Även om du kan skapa ett virtuellt Azure-nätverk, Kafka och Spark-kluster manuellt, är det enklare att använda en Azure Resource Manager mall. Använd följande steg för att distribuera ett virtuellt Azure-nätverk, Kafka och Spark-kluster till din Azure-prenumeration.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager-mallen finns i GitHub-lagringsplatsen för det här projektet[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)().

    Den här mallen skapar följande resurser:

   * En Kafka på HDInsight 3.6-klustret.

   * Ett spark på HDInsight 3,6-kluster.

   * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren.

       > [!NOTE]  
       > Det virtuella nätverk som skapas av mallen använder adress utrymmet 10.0.0.0/16.

   * En Azure Cosmos DB SQL API-databas.

     > [!IMPORTANT]  
     > Anteckningsboken för Structured Streaming som används i det här exemplet kräver Apache Spark i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

2. Använd följande information för att fylla i posterna i avsnittet **Anpassad distribution** :
   
    ![Anpassad HDInsight-distribution](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

    * **Prenumeration**: Välj din Azure-prenumeration.
   
    * **Resursgrupp**: Skapa en grupp eller Välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Cosmos DB konto namn**: Det här värdet används som namn på det Cosmos DB kontot.

    * **Namn på bas kluster**: Det här värdet används som grund namn för Spark-och Kafka-klustren. Om du till exempel anger **myhdi** skapas ett Spark-kluster med namnet __Spark-Myhdi__ och ett Kafka-kluster med namnet **Kafka-myhdi**.

    * **Kluster version**: HDInsight-klustrets version.

        > [!IMPORTANT]  
        > Det här exemplet testas med HDInsight 3,6 och fungerar kanske inte med andra kluster typer.

    * **Användar namn för kluster inloggning**: Administratörs användar namnet för Spark-och Kafka-klustren.

    * **Lösen ord för kluster inloggning**: Administratörs användarens lösen ord för Spark-och Kafka-kluster.

    * **SSH-användar namn**: SSH-användaren som ska skapas för Spark-och Kafka-kluster.

    * **SSH-lösenord**: Lösen ordet för SSH-användaren för Spark-och Kafka-klustren.

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Välj slutligen **köp**. Det tar ungefär 20 minuter att skapa klustren.

> [!IMPORTANT]  
> Det kan ta upp till 45 minuter att skapa kluster, virtuellt nätverk och Cosmos DB konto.

## <a name="create-the-cosmos-db-database-and-collection"></a>Skapa Cosmos DB databas och samling

Projektet som används i det här dokumentet lagrar data i Cosmos DB. Innan du kör koden måste du först skapa en _databas_ och _samling_ i Cosmos DB-instansen. Du måste också hämta dokument slut punkten och _nyckeln_ som används för att autentisera begär anden till Cosmos dB. 

Ett sätt att göra detta är att använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Följande skript kommer att skapa en databas med `kafkadata` namnet och en samling `kafkacollection`med namnet. Den returnerar sedan den primära nyckeln.

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

Dokument slut punkten och primär nyckelinformation liknar följande text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Spara slut punkts-och nyckel värden eftersom de behövs i antecknings böckerna för Jupyter.

## <a name="get-the-apache-kafka-brokers"></a>Hämta Apache Kafka-utjämnare

Koden i det här exemplet ansluter till Kafka Broker-värdar i Kafka-klustret. Använd följande PowerShell-eller bash-exempel för att hitta adresserna för de två Kafka-Broker-värdarna:

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
> Bash-exemplet förväntar sig `$CLUSTERNAME` att innehålla namnet på Kafka-klustret.
>
> I det här exemplet används [JQ](https://stedolan.github.io/jq/) -verktyget för att parsa data från JSON-dokumentet.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

När du uppmanas till det anger du lösen ordet för kluster inloggnings kontot (admin)

De utdata som genereras liknar följande text:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Spara informationen som den används i följande avsnitt i det här dokumentet.

## <a name="get-the-notebooks"></a>Hämta antecknings böckerna

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Ladda upp anteckningsböckerna

Använd följande steg för att ladda upp antecknings böckerna från projektet till ditt Spark på HDInsight-kluster:

1. Anslut till Jupyter Notebook i Apache Spark-klustret i din webbläsare. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

2. Från den övre högra sidan av sidan använder du knappen __Ladda upp__ för att ladda upp data __-till-Kafka. ipynb-filen från Stream-taxi-data__ till klustret. Välj __Öppna__ för att påbörja uppladdningen.

3. Leta upp posten __Stream-taxi-data-till-Kafka. ipynb__ i listan över antecknings böcker och välj knappen __Ladda upp__ bredvid den.

4. Upprepa steg 1-3 för att läsa in __Stream-data-från-Kafka-till-Cosmos-dB. ipynb__ Notebook.

## <a name="load-taxi-data-into-kafka"></a>Läs in taxi data till Kafka

När filerna har laddats upp väljer du posten __Stream-taxi-data-till-Kafka. ipynb__ för att öppna antecknings boken. Följ stegen i antecknings boken för att läsa in data i Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Bearbeta taxi data med Spark-strukturerad strömning

På Start sidan för [Jupyter Notebook](https://jupyter.org/) väljer du posten __Stream-data-från-Kafka-till-Cosmos-dB. ipynb__ . Följ stegen i antecknings boken för att strömma data från Kafka och till Azure Cosmos DB använda Spark-strukturerad strömning.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Spark strukturerad strömning kan du läsa mer om hur du arbetar med Apache Spark, Apache Kafka och Azure Cosmos DB i följande dokument:

* [Använda Apache Spark streaming (DStream) med Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter Notebook och Apache Spark på HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md)
