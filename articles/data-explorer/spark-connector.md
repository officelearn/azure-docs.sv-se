---
title: Använd Azure Datautforskaren-anslutaren för Apache Spark för att flytta data mellan Azure Datautforskaren och Spark-kluster.
description: Det här avsnittet visar hur du flyttar data mellan Azure-Datautforskaren och Apache Spark-kluster.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208629"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Datautforskaren Connector för Apache Spark

[Apache Spark](https://spark.apache.org/) är en enhetlig analys motor för storskalig data bearbetning. Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys för real tids analys av stora mängder data. 

Azure Datautforskaren Connector för Spark är ett [projekt med öppen källkod](https://github.com/Azure/azure-kusto-spark) som kan köras på alla Spark-kluster. Den implementerar data källan och data mottagaren för att flytta data mellan Azure Datautforskaren och Spark-kluster. Med Azure Datautforskaren och Apache Spark kan du bygga snabba och skalbara program som riktar sig mot data drivna scenarier. Till exempel Machine Learning (ML), Extract-Transform-load (ETL) och Log Analytics. Med anslutnings tjänsten blir Azure Datautforskaren ett giltigt data lager för standard-Spark-källa och mottagar åtgärder, till exempel Skriv-, Läs-och writeStream.

Du kan skriva till Azure Datautforskaren i antingen batch-eller strömnings läge. Läsning från Azure Datautforskaren stöder kolumn rensning och predikat mottagnings, som filtrerar data i Azure Datautforskaren, vilket minskar mängden överförda data.

I det här avsnittet beskrivs hur du installerar och konfigurerar Azure Datautforskaren Spark Connector och flyttar data mellan Azure Datautforskaren och Apache Spark kluster.

> [!NOTE]
> Även om några av exemplen nedan refererar till ett [Azure Databricks](https://docs.azuredatabricks.net/) Spark-kluster, tar Azure datautforskaren Spark-anslutaren inte direkta beroenden för Databricks eller någon annan Spark-distribution.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett Azure Datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal) 
* Skapa ett Spark-kluster
* Installera Azure Datautforskaren anslutnings bibliotek:
    * Färdiga bibliotek för [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven lagrings platsen](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) installerat

> [!TIP]
> 2.3. x-versioner stöds också, men kan kräva vissa ändringar i Pom. XML-beroenden.

## <a name="how-to-build-the-spark-connector"></a>Så här skapar du Spark-anslutningen

> [!NOTE]
> Det här är valfritt. Om du använder färdiga bibliotek går du till konfiguration av [Spark-kluster](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Versions krav

1. Installera biblioteken som listas i [beroenden](https://github.com/Azure/azure-kusto-spark#dependencies) , inklusive följande [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -bibliotek:
    * [Kusto data klient](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto-klient](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Referera till [den här källan](https://github.com/Azure/azure-kusto-spark) för att skapa Spark-anslutningsprogrammet.

1. För Scala/Java-program med Maven-projekt definitioner länkar du ditt program med följande artefakt (den senaste versionen kan skilja sig):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Build-kommandon

För att skapa jar och köra alla tester:

```
mvn clean package
```

För att skapa jar, kör alla tester och installera jar på din lokala maven-lagringsplats:

```
mvn clean install
```

Mer information finns i [kopplings användning](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfiguration av Spark-kluster

> [!NOTE]
> Vi rekommenderar att du använder den senaste versionen av Azure Datautforskaren Spark Connector när du utför följande steg.

1. Konfigurera följande inställningar för Spark-kluster baserat på Azure Databricks kluster med Spark 2.4.4 och Scala 2,11:

    ![Databricks kluster inställningar](media/spark-connector/databricks-cluster.png)
    
1. Installera det senaste Spark-kusto-Connector-biblioteket från maven:
    
    ![importera bibliotek](media/spark-connector/db-libraries-view.png) ![väljer Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Kontrol lera att alla nödvändiga bibliotek är installerade:

    ![Verifiera installerade bibliotek](media/spark-connector/db-libraries-view.png)

1. Om du vill installera med en JAR-fil kontrollerar du att ytterligare beroenden har installerats:

    ![Lägg till beroenden](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Autentisering

Med Azure Datautforskaren Spark Connector kan du autentisera med Azure Active Directory (Azure AD) med någon av följande metoder:
* Ett [Azure AD-program](#azure-ad-application-authentication)
* En [Azure AD-åtkomsttoken](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Enhetsautentisering](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (för scenarier utan produktion)
* En [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) för att få åtkomst till Key Vault-resursen, installera paketet Azure-proxy och ange autentiseringsuppgifter för programmet.

### <a name="azure-ad-application-authentication"></a>Azure AD Application Authentication

Azure AD Application Authentication är den enklaste och vanligaste autentiseringsmetoden och rekommenderas för Azure Datautforskaren Spark-anslutaren.

|Egenskaper  |Beskrivning  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   ID för Azure AD-program (klient).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-autentiseringsprovider. ID för Azure AD-katalog (klient).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-programnyckel för-klienten.     |

### <a name="azure-data-explorer-privileges"></a>Azure Datautforskaren-behörigheter

Ge följande behörigheter för ett Azure Datautforskaren-kluster:

* För läsning (data källa) måste Azure AD-identiteten ha *visnings* privilegier för mål databasen eller *Administratörs* behörighet för mål tabellen.
* För att skriva (data mottagare) måste Azure AD-identiteten ha *insugnings* behörighet för mål databasen. Det måste också ha *användar* behörighet på mål databasen för att skapa nya tabeller. Om mål tabellen redan finns måste du konfigurera *Administratörs* behörighet för mål tabellen.
 
Mer information om Azure Datautforskaren huvud roller finns i [rollbaserad auktorisering](/azure/kusto/management/access-control/role-based-authorization). Information om hur du hanterar säkerhets roller finns i [hantering av säkerhets roller](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-mottagare: skriva till Azure Datautforskaren

1. Ställ in mottagar parametrar:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Skriv Spark-DataFrame till Azure Datautforskaren-kluster som batch:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Eller Använd den förenklade syntaxen:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Skriv strömmande data:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Källa: läser från Azure Datautforskaren

1. När du läser [små mängder data](/azure/kusto/concepts/querylimits)definierar du data frågan:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Valfritt: om **du** anger den tillfälliga blob-lagringen (och inte Azure datautforskaren) skapas Blobbarna under anroparens ansvar. Detta omfattar etablering av lagring, rotation av åtkomst nycklar och borttagning av tillfälliga artefakter. 
    KustoBlobStorageUtils-modulen innehåller hjälp funktioner för att ta bort blobar baserat på konto-och container-koordinater och kontoautentiseringsuppgifter, eller en fullständig SAS-URL med behörigheterna Skriv, läsa och lista. När motsvarande RDD inte längre behövs lagrar varje transaktion tillfälliga BLOB-artefakter i en separat katalog. Den här katalogen samlas in som en del av informationen om Läs transaktioner som rapporteras i noden Spark-drivrutin.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    I exemplet ovan går det inte att komma åt Key Vault med hjälp av kopplings gränssnittet. en enklare metod för att använda Databricks hemligheter används.

1. Läs från Azure Datautforskaren.

    * Om **du** anger den tillfälliga blob-lagringen läser du från Azure datautforskaren enligt följande:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Om **azure datautforskaren** tillhandahåller den tillfälliga blob-lagringen läser du från Azure datautforskaren enligt följande:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure datautforskaren Spark-anslutaren](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Exempel kod för Java och python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
