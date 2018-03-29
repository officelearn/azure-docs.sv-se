---
title: Apache Väck strukturerade direktuppspelning från Kafka till Azure Cosmos DB - Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder Apache Spark strukturerade strömning för att läsa data från Apache Kafka och sedan lagra den i Azure Cosmos DB. I det här exemplet strömma data med hjälp av en Jupyter-anteckningsbok från Spark på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 7346a45cf04b50369cc7b853b985a8b0bc865493
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Använda Spark strukturerad strömning med Kafka och Azure Cosmos DB

Lär dig hur du använder Spark strukturerade strömning att läsa data från Apache Kafka på Azure HDInsight och lagra data i Azure Cosmos DB.

Azure Cosmos-DB är en global och flera olika modeller databas. Det här exemplet används en databasmodell SQL API. Mer information finns i [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md) dokumentet.

Spark strukturerad strömning är en dataström bearbetningsmotor som bygger på Spark SQL. Det kan du express strömmande beräkningar samma som batch beräkningar på statiska data. Läs mer strukturerade direktuppspelning av [strukturerade strömning Programmeringsguide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]
> Det här exemplet används 2.2 Spark på HDInsight 3,6.
>
> Stegen i det här dokumentet skapa ett Azure-resursgrupp som innehåller både en Spark i HDInsight och en Kafka på HDInsight-kluster. Dessa kluster finns både i ett Azure Virtual Network, vilket innebär att Spark-klustret kan kommunicera direkt med Kafka kluster.
>
> Kom ihåg att ta bort kluster för att undvika överdriven avgifter när du är klar med steg i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare via det offentliga internet. Allt som kommunicerar Kafka måste finnas i samma virtuella Azure-nätverket som noder i klustret Kafka. I det här exemplet finns både Kafka och Spark-kluster i Azure-nätverk. Följande diagram visar hur kommunikation som flödar mellan kluster:

![Diagram över Spark och Kafka kluster i Azure-nätverk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Tjänsten Kafka är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via internet. Mer information om de offentliga portarna som är tillgängliga med HDInsight finns [portar och URI: er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Du kan skapa en Azure-nätverk, Kafka, och Spark-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk, Kafka, och vidtar kluster på Azure-prenumerationen.

1. Använd knappen följande för att logga in på Azure och öppna mallen i Azure-portalen.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Azure Resource Manager-mallen finns i GitHub-lagringsplatsen för det här projektet ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Den här mallen skapas i följande resurser:

    * En Kafka på 3,6 HDInsight-klustret.

    * En Spark på HDInsight 3,6 klustret.

    * Ett Azure Virtual Network, som innehåller HDInsight-kluster.

        > [!NOTE]
        > Det virtuella nätverket som skapas av mallen använder adressutrymmet 10.0.0.0/16.

    * En Azure SQL DB-API Cosmos-databas.

    > [!IMPORTANT]
    > Strukturerade strömmande anteckningsboken som används i det här exemplet kräver Spark i HDInsight 3,6. Om du använder en tidigare version av Spark på HDInsight får du fel när du använder den bärbara datorn.

2. Använd följande information för att fylla i posterna på den **anpassad distribution** avsnitt:
   
    ![HDInsight anpassad distribution](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Prenumeration**: Välj din Azure-prenumeration.
   
    * **Resursgruppen**: skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.

    * **Cosmos DB kontonamn**: det här värdet används som namnet på kontot Cosmos DB.

    * **Basera klusternamnet**: det här värdet används som det grundläggande namnet på Spark och Kafka kluster. Ange till exempel **myhdi** skapar ett Spark-kluster med namnet __spark myhdi__ och ett Kafka kluster med namnet **kafka myhdi**.

    * **Klustret Version**: I HDInsight-kluster av version.

        > [!IMPORTANT]
        > Det här exemplet har testats med HDInsight 3,6 och fungerar inte med andra typer av klustret.

    * **Klustrets inloggningsnamn**: admin användarnamn för Spark och Kafka kluster.

    * **Klustret inloggningslösenordet**: administratörslösenord för Spark och Kafka kluster.

    * **SSH-användarnamn**: SSH-användare skapa för Spark och Kafka kluster.

    * **SSH-lösenordet**: lösenord för SSH-användare för Spark och Kafka kluster.

3. Läs den **villkor**, och välj sedan **jag samtycker till villkoren som anges ovan**.

4. Kontrollera slutligen **fäst på instrumentpanelen** och välj sedan **inköp**. Det tar ungefär 20 minuter för att skapa kluster.

> [!IMPORTANT]
> Det kan ta upp till 45 minuter att skapa kluster, virtuella nätverk och Cosmos-DB-konto.

## <a name="create-the-cosmos-db-database-and-collection"></a>Skapa Cosmos-DB-databas och samling

Projektet i det här dokumentet lagrar data i Cosmos-databasen. Innan du kör koden, måste du först skapa en _databasen_ och _samling_ i din Cosmos-DB-instans. Måste du också hämta dokumentet slutpunkten och _nyckeln_ används för att autentisera förfrågningar till Cosmos DB. 

Ett sätt att göra detta är att använda den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Följande skript skapar en databas med namnet `kafkadata` och en samling med namnet `kafkacollection`. Den returnerar sedan den primära nyckeln.

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

Dokumentet slutpunkt och primära nyckelinformationen liknar följande:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> Spara slutpunkt och nyckelvärden, eftersom de behövs i Jupyter-anteckningsböcker.

## <a name="get-the-kafka-brokers"></a>Hämta Kafka mäklare

Koden i det här exemplet ansluter till Kafka broker värdar i klustret Kafka. Använd följande PowerShell- eller Bash exempel för att hitta adresser för de två Kafka broker värdarna:

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
> Bash-exempel räknar `$CLUSTERNAME` som innehåller namnet på klustret Kafka.
>
> Det här exemplet används den [jq](https://stedolan.github.io/jq/) verktyg för att analysera data från JSON-dokumentet.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

När du uppmanas, ange lösenordet för kluster-inloggningskonto (admin)

De utdata som genereras liknar följande text:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Spara den här informationen eftersom den används i följande avsnitt i det här dokumentet.

## <a name="get-the-notebooks"></a>Hämta de bärbara datorerna

Koden för det exempel som beskrivs i det här dokumentet finns på [ https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb ](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Överför de bärbara datorerna

Använd följande steg för att överföra anteckningsböcker från projektet till din Spark på HDInsight-kluster:

1. Anslut till Jupyter notebook i Spark-kluster i din webbläsare. I följande URL, ersätter `CLUSTERNAME` med namnet på din __Spark__ klustret:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    När du uppmanas ange klusterinloggning (admin) och lösenord som används när du skapade klustret.

2. Använd från det övre högra hörnet på sidan, den __överför__ för att överföra den __Stream-taxi-data-till-kafka.ipynb__ filen till klustret. Välj __öppna__ så startas guiden Överför.

3. Hitta de __Stream-taxi-data-till-kafka.ipynb__ post i listan över anteckningsböcker och välj __överför__ bredvid den.

4. Upprepa steg 1 – 3 för att läsa in den __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ bärbar dator.

## <a name="load-taxi-data-into-kafka"></a>Läs in taxi data i Kafka

När filerna har överförts, välja den __Stream-taxi-data-till-kafka.ipynb__ post för att öppna den bärbara datorn. Följ stegen i anteckningsboken för att läsa in data till Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processen taxi data med hjälp av Spark strukturerade strömning

Sidan Jupyter-anteckningsbok, Välj den __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ transaktionen. Följ stegen i den bärbara datorn till dataströmmen data från Kafka och i Azure Cosmos-databasen med Spark strukturerade strömning.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Spark strukturerade strömning, finns i följande dokument vill lära dig mer om hur du arbetar med Spark, Kafka och Azure Cosmos DB:

* [Hur du använder Spark streaming (DStream) med Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter-anteckningsbok och Spark i HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md)
