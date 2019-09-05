---
title: Använd Azure Datautforskaren-anslutaren för Apache Spark för att flytta data mellan Azure Datautforskaren och Spark-kluster.
description: Det här avsnittet visar hur du flyttar data mellan Azure-Datautforskaren och Apache Spark-kluster.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383051"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure Datautforskaren Connector för Apache Spark (för hands version)

[Apache Spark](https://spark.apache.org/) är en enhetlig analys motor för storskalig data bearbetning. Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys för real tids analys av stora mängder data. 

Azure Datautforskaren Connector för Spark implementerar data källa och data mottagare för att flytta data mellan Azure Datautforskaren och Spark-kluster för att använda båda funktionerna. Med Azure Datautforskaren och Apache Spark kan du bygga snabba och skalbara program som riktar sig mot data drivna scenarier, till exempel Machine Learning (ML), Extract-Transform-load (ETL) och Log Analytics. Skrivning till Azure-Datautforskaren kan göras i batch-och strömnings läge.
Läsning från Azure Datautforskaren stöder kolumn rensning och predikat mottagnings, vilket minskar mängden överförda data genom att filtrera bort data i Azure Datautforskaren.

Azure Datautforskaren Spark Connector är ett [projekt med öppen källkod](https://github.com/Azure/azure-kusto-spark) som kan köras på alla Spark-kluster. Azure Datautforskaren Spark-anslutaren gör Azure Datautforskaren ett giltigt data lager för standard Spark-källa och mottagar åtgärder som Skriv-, Läs-och writeStream. 

> [!NOTE]
> Även om några av exemplen nedan refererar till ett [Azure Databricks](https://docs.azuredatabricks.net/) Spark-kluster, tar Azure datautforskaren Spark-anslutaren inte direkta beroenden för Databricks eller någon annan Spark-distribution.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett Azure Datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal) 
* Skapa ett Spark-kluster
* Installera Azure Datautforskaren Connector-biblioteket och bibliotek som visas i [beroenden](https://github.com/Azure/azure-kusto-spark#dependencies) , inklusive följande [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -bibliotek:
    * [Kusto data klient](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto-klient](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Färdiga bibliotek för [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Så här skapar du Spark-anslutningen

Spark-anslutningen kan skapas från [källor](https://github.com/Azure/azure-kusto-spark) som beskrivs nedan.

> [!NOTE]
> Det här steget är valfritt. Om du använder färdiga bibliotek går du till konfiguration av [Spark-kluster](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Versions krav

* Java 1,8 SDK installerat
* [Maven 3. x](https://maven.apache.org/download.cgi) installerat
* Apache Spark version 2.4.0 eller senare

> [!TIP]
> 2.3. x-versioner stöds också, men kan kräva vissa ändringar i Pom. XML-beroenden.

För Scala/Java-program med Maven-projekt definitioner länkar du ditt program med följande artefakt (den senaste versionen kan skilja sig):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> Vi rekommenderar att du använder den senaste versionen av Azure Datautforskaren Spark Connector när du utför följande steg:

1. Ange följande inställningar för Spark-kluster baserat på Azure Databricks kluster med Spark 2,4 och Scala 2,11: 

    ![Databricks kluster inställningar](media/spark-connector/databricks-cluster.png)

1. Importera Azure Datautforskaren Connector-biblioteket:

    ![Importera Azure Datautforskaren-bibliotek](media/spark-connector/db-create-library.png)

1. Lägg till ytterligare beroenden (behövs inte om de används från maven):

    ![Lägg till beroenden](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Rätt Java-version för varje spark-utgåva finns [här](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Kontrol lera att alla nödvändiga bibliotek är installerade:

    ![Verifiera installerade bibliotek](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Med Azure Datautforskaren Spark Connector kan du autentisera med Azure Active Directory (Azure AD) med hjälp av ett [Azure AD-program](#azure-ad-application-authentication), [Azure AD-åtkomsttoken](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [enhetsautentisering](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (för icke-produktions scenarier) eller [Azure-nyckel Valvet](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Användaren måste installera Azure-proxy-paketet och ange programautentiseringsuppgifter för att få åtkomst till Key Vault resursen.

### <a name="azure-ad-application-authentication"></a>Azure AD Application Authentication

Den enklaste och vanliga autentiseringsmetoden. Den här metoden rekommenderas för användning av Azure Datautforskaren Spark-anslutning.

|properties  |Beskrivning  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   ID för Azure AD-program (klient).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-autentiseringsprovider. ID för Azure AD-katalog (klient).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-programnyckel för-klienten.     |

### <a name="azure-data-explorer-privileges"></a>Azure Datautforskaren-behörigheter

Följande behörigheter måste beviljas i ett Azure Datautforskaren-kluster:

* För läsning (data källa) måste Azure AD-programmet ha *visnings* privilegier för mål databasen eller *Administratörs* behörighet för mål tabellen.
* För att skriva (data mottagare) måste Azure AD-programmet *ha* inmatnings behörigheter för mål databasen. Det måste också ha *användar* behörighet på mål databasen för att skapa nya tabeller. Om mål tabellen redan finns kan *Administratörs* behörighet för mål tabellen konfigureras.
 
Mer information om Azure Datautforskaren huvud roller finns i [rollbaserad auktorisering](/azure/kusto/management/access-control/role-based-authorization). Information om hur du hanterar säkerhets roller finns i [hantering av säkerhets roller](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-mottagare: Skriver till Azure Datautforskaren

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
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Källa: Läser från Azure Datautforskaren

1. När du läser små mängder data definierar du data frågan:

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

1. Vid läsning av stora mängder data måste tillfälliga blob-lagring tillhandahållas. Ange SAS-nyckel för lagrings behållare, eller lagrings konto namn, konto nyckel och behållar namn. Det här steget krävs endast för den aktuella för hands versionen av Spark-anslutaren.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    I exemplet ovan kommer vi inte åt Key Vault med hjälp av kopplings gränssnittet. Alternativt använder vi en enklare metod för att använda Databricks hemligheter.

1. Läs från Azure Datautforskaren:

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
