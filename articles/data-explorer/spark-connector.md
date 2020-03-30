---
title: Använd Azure Data Explorer-kopplingen för Apache Spark för att flytta data mellan Azure Data Explorer- och Spark-kluster.
description: Det här avsnittet visar hur du flyttar data mellan Azure Data Explorer och Apache Spark-kluster.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208629"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer Connector för Apache Spark

[Apache Spark](https://spark.apache.org/) är en enhetlig analysmotor för storskalig databehandling. Azure Data Explorer är en snabb och fullständigt hanterad dataanalystjänst för realtidsanalys på stora mängder data. 

Azure Data Explorer-kopplingen för Spark är ett [projekt med öppen källkod](https://github.com/Azure/azure-kusto-spark) som kan köras på alla Spark-kluster. Den implementerar datakälla och datamottagare för att flytta data över Azure Data Explorer och Spark-kluster. Med Hjälp av Azure Data Explorer och Apache Spark kan du skapa snabba och skalbara program som riktar sig till datadrivna scenarier. Maskininlärning (ML), ETL (Extract-Transform-Load) och Logganalys. Med kopplingen blir Azure Data Explorer ett giltigt datalager för standard spark-käll- och sink-åtgärder, till exempel skriva, läsa och writeStream.

Du kan skriva till Azure Data Explorer i antingen batch- eller direktuppspelningsläge. Läsning från Azure Data Explorer stöder kolumnskärning och predikat pushdown, som filtrerar data i Azure Data Explorer, vilket minskar mängden överförda data.

I det här avsnittet beskrivs hur du installerar och konfigurerar Azure Data Explorer Spark-kopplingen och flyttar data mellan Azure Data Explorer och Apache Spark-kluster.

> [!NOTE]
> Även om vissa av exemplen nedan refererar till ett [Azure Databricks](https://docs.azuredatabricks.net/) Spark-kluster, tar Azure Data Explorer Spark-anslutning inte direkt beroenden av Databricks eller någon annan Spark-distribution.

## <a name="prerequisites"></a>Krav

* [Skapa ett Azure Data Explorer-kluster och -databas](/azure/data-explorer/create-cluster-database-portal) 
* Skapa ett Spark-kluster
* Installera Azure Data Explorer-anslutningsbibliotek:
    * Färdiga bibliotek för [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) installerat

> [!TIP]
> 2.3.x versioner stöds också, men kan kräva vissa ändringar i pom.xml-beroenden.

## <a name="how-to-build-the-spark-connector"></a>Så här bygger du Spark-kontakten

> [!NOTE]
> Det här steget är valfritt. Om du använder färdiga bibliotek går du till [Spark-klusterinställningar .](#spark-cluster-setup)

### <a name="build-prerequisites"></a>Bygg förutsättningar

1. Installera biblioteken i [beroenden,](https://github.com/Azure/azure-kusto-spark#dependencies) inklusive följande [Kusto Java SDK-bibliotek:](/azure/kusto/api/java/kusto-java-client-library)
    * [Kusto-dataklient](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Kund](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Se [den här källan](https://github.com/Azure/azure-kusto-spark) för att skapa Spark Connector.

1. För Scala/Java-program som använder Maven-projektdefinitioner, länka ditt program med följande artefakt (den senaste versionen kan skilja sig åt):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Skapa kommandon

Så här bygger du burken och kör alla tester:

```
mvn clean package
```

Om du vill skapa burk kör du alla tester och installerar burken i den lokala Maven-lagringsplatsen:

```
mvn clean install
```

Mer information finns i [anslutningsanvändning](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfigurera sparkkluster

> [!NOTE]
> Vi rekommenderar att du använder den senaste Versionen av Azure Data Explorer Spark-anslutningsversionen när du utför följande steg.

1. Konfigurera följande Spark-klusterinställningar, baserat på Azure Databricks-kluster med Spark 2.4.4 och Scala 2.11:

    ![Inställningar för Databricks-kluster](media/spark-connector/databricks-cluster.png)
    
1. Installera det senaste spark-kusto-connector-biblioteket från Maven:
    
    ![Importera bibliotek](media/spark-connector/db-libraries-view.png) ![Välj Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Kontrollera att alla nödvändiga bibliotek är installerade:

    ![Verifiera installerade bibliotek](media/spark-connector/db-libraries-view.png)

1. Kontrollera att ytterligare beroenden har installerats för installation med en JAR-fil:

    ![Lägga till beroenden](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Autentisering

Med Azure Data Explorer Spark-anslutningsappen kan du autentisera med Azure Active Directory (Azure AD) med någon av följande metoder:
* Ett [Azure AD-program](#azure-ad-application-authentication)
* En [Azure AD-åtkomsttoken](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Enhetsautentisering](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (för scenarier som inte är produktionsscenarier)
* Ett [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) För att komma åt Key Vault-resursen installerar du azure-keyvault-paketet och tillhandahåller programautentiseringsuppgifter.

### <a name="azure-ad-application-authentication"></a>Autentisering av Azure AD-program

Azure AD-programautentisering är den enklaste och vanligaste autentiseringsmetoden och rekommenderas för Azure Data Explorer Spark-kopplingen.

|Egenskaper  |Beskrivning  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD-program (klient) identifierare.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-autentiseringsutfärdaren. Azure AD Directory (klient) ID.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-programnyckel för klienten.     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer-privilegier

Bevilja följande privilegier i ett Azure Data Explorer-kluster:

* För läsning (datakälla) måste Azure *viewer* AD-identiteten ha visningsbehörighet i måldatabasen eller administratörsbehörighet i måltabellen. *admin*
* För att skriva (datamottagare) måste Azure AD-identiteten ha *ingestor-behörigheter* i måldatabasen. Den måste *user* också ha användarbehörighet i måldatabasen för att kunna skapa nya tabeller. Om måltabellen redan finns *admin* måste du konfigurera administratörsbehörighet i måltabellen.
 
Mer information om huvudroller i Azure Data Explorer finns i [rollbaserad auktorisering](/azure/kusto/management/access-control/role-based-authorization). Hur du hanterar säkerhetsroller finns i [hantering av säkerhetsroller](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark sink: skriva till Azure Data Explorer

1. Ställ in sink-parametrar:

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

1. Skriv Spark DataFrame till Azure Data Explorer-kluster som batch:

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
    
   Eller använd den förenklade syntaxen:
   
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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-källa: läsning från Azure Data Explorer

1. När du läser [små mängder data](/azure/kusto/concepts/querylimits)definierar du datafrågan:

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

1. Valfritt: Om **du** anger tillfällig blob-lagring (och inte Azure Data Explorer) skapas blobbar under anroparens ansvar. Detta inkluderar etablering av lagring, roterande åtkomstnycklar och borttagning av tillfälliga artefakter. 
    Modulen KustoBlobStorageUtils innehåller hjälpfunktioner för att ta bort blobbar baserat på antingen konto- och behållarkoordinater och kontoautentiseringsuppgifter, eller en fullständig SAS-URL med skriv-, läs- och listbehörigheter. När motsvarande RDD inte längre behövs lagras transienta blobartefakter i en separat katalog. Den här katalogen fångas in som en del av informationsloggar för lästransaktioner som rapporteras på spark-drivrutinsnoden.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    I exemplet ovan nås inte Nyckelvalvet med anslutningsgränssnittet. en enklare metod för att använda Databricks hemligheter används.

1. Läs från Azure Data Explorer.

    * Om **du** anger tillfällig blob-lagring läser du från Azure Data Explorer på följande sätt:

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

    * Om **Azure Data Explorer** tillhandahåller tillfällig blob-lagring läser du från Azure Data Explorer på följande sätt:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Data Explorer Spark Connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Exempelkod för Java och Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
