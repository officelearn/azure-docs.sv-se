---
title: Använd Azure Data Explorer-kopplingen för Apache Spark för att flytta data mellan Azure Data Explorer och Spark-kluster.
description: Det här avsnittet visar hur du flyttar data mellan Azure Data Explorer och Apache Spark-kluster.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441345"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure Explorer datakoppling för Apache Spark (förhandsversion)

[Apache Spark](https://spark.apache.org/) är en enhetlig analytisk motor för storskalig databearbetning. Azure Data Explorer är en analystjänst för snabba, fullständigt hanterade data för realtidsanalys på stora mängder data. 

Azure Data Explorer-anslutningsapp för Spark implementerar datakälla och mellanlagringsplats för att flytta data mellan Azure Data Explorer och Spark-kluster för att använda båda deras funktioner. Du kan använda Datautforskaren i Azure och Apache Spark, för att skapa snabba, skalbara program som riktar in sig på datadriven scenarier, till exempel machine learning (ML), extrahering, transformering och laddning (ETL) och Log Analytics. Skrivning till Datautforskaren i Azure kan göras i batch- och strömmande läge.
Läsning från Azure Data Explorer stöder kolumnen rensning och predikat pushdown, vilket minskar mängden överförda data genom att filtrera bort data i Datautforskaren i Azure.

Azure Data Explorer Spark-anslutningsappen är en [projekt med öppen källkod](https://github.com/Azure/azure-kusto-spark) som kan köras på alla Spark-kluster.

> [!NOTE]
> Även om vissa av exemplen nedan avser en [Azure Databricks](https://docs.azuredatabricks.net/) Spark-kluster, Azure Data Explorer Spark connector tar inte direkta beroenden på Databricks eller andra Spark-distribution.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en Azure Data Explorer-kluster och databas](/azure/data-explorer/create-cluster-database-portal) 
* Skapa ett Spark-kluster
* Installera kopplingsbibliotek i Datautforskaren i Azure och bibliotek som anges i [beroenden](https://github.com/Azure/azure-kusto-spark#dependencies) inklusive följande [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) bibliotek:
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto mata in klienten](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Färdiga bibliotek för [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Skapa Spark-anslutningsappen

Spark-Anslutningsappen kan byggas från [källor](https://github.com/Azure/azure-kusto-spark) enligt beskrivning nedan.

> [!NOTE]
> Det här steget är valfritt. Om du använder fördefinierade bibliotek gå till [konfiguration av Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Skapa krav

* Java 1.8 SDK är installerat
* [Maven 3.x](https://maven.apache.org/download.cgi) installerad
* Apache Spark-version 2.4.0 eller högre

> [!TIP]
> 2.3.x versioner stöds också, men kan kräva att vissa ändringar i pom.xml-beroenden.

För Scala/Java-program med hjälp av Maven-projekt definitioner länka ditt program med följande artefakten (senaste versionen kan skilja sig):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Skapa kommandon

Att skapa JAR-filen och kör alla test:

```
mvn clean package
```

Kör alla test för att skapa jar och installera JAR-filen till din lokala lagringsplats med Maven:

```
mvn clean install
```

Mer information finns i [connector användning](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfiguration av Spark

> [!NOTE]
> Det rekommenderas att använda den senaste versionen av Azure Data Explorer Spark connector när du utför följande steg:

1. Ange följande Spark klusterinställningar, baserat på Azure Databricks-kluster med hjälp av Spark 2.4 och Scala 2.11: 

    ![Inställningar för Databricks-klustret](media/spark-connector/databricks-cluster.png)

1. Importera kopplingsbibliotek i Datautforskaren i Azure:

    ![Importera Datautforskaren i Azure-bibliotek](media/spark-connector/db-create-library.png)

1. Lägg till ytterligare beroenden:

    ![Lägga till beroenden](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Det går att hitta rätt java-versionen för varje Spark-version [här](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Kontrollera att alla nödvändiga bibliotek är installerade:

    ![Kontrollera bibliotek som är installerade](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Autentisering

Azure Data Explorer Spark-anslutningsappen kan du autentisera med Azure Active Directory (Azure AD) med hjälp av en [Azure AD-program](#azure-ad-application-authentication), [Azure AD-åtkomsttoken](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [enhetsautentisering ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (för icke-produktionsscenarion), eller [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Användaren måste installera azure keyvault-paketet och ange autentiseringsuppgifter för åtkomst till Key Vault-resursen.

### <a name="azure-ad-application-authentication"></a>Azure AD-autentisering

De flesta enkla och vanliga autentiseringsmetod. Den här metoden rekommenderas för användning av Azure Data Explorer Spark connector.

|Egenskaper  |Beskrivning  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identifierare för Azure AD-programmet (klient).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-autentisering-utfärdare. Azure AD-katalog (klient)-ID.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-programnyckel för klienten.     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer privilegier

Följande behörigheter måste beviljas på ett Azure Data Explorer-kluster:

* För att läsa (datakälla), Azure AD-program måste ha *viewer* privilegier i måldatabasen eller *admin* privilegier i måltabellen.
* För att skriva (data-mottagare) Azure AD-program måste ha *lösning* privilegier i måldatabasen. Det måste också ha *användaren* behörighet för måldatabasen att skapa nya tabeller. Om det finns redan i måltabellen, *admin* privilegier i måltabellen kan konfigureras.
 
Mer information om Azure Data Explorer huvudnamn roller finns i [rollbaserad auktorisering](/azure/kusto/management/access-control/role-based-authorization). Hantera säkerhetsroller finns i [roller säkerhetshantering](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-mottagare: Skriva till Azure Data Explorer

1. Ställa in parametrar för kopieringsmottagare:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Skriva Spark DataFrame till Datautforskaren i Azure-kluster som batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Skriva strömmande data:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-källa: Läsning från Azure Data Explorer

1. Vid läsning av små mängder data, definiera datafrågan:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Vid läsning av stora mängder data, måste tillfälligt blob-lagring anges. Ange storage container SAS-nyckel, eller lagringskontonamn, nyckel och behållarnamn. Detta krävs för den aktuella förhandsversionen av Spark-anslutningsappen.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    I exemplet ovan inte vi åtkomst till Key Vault med hjälp av gränssnittet connector. Vi använder också en enklare metod för att använda Databricks-hemligheter.

1. Läsa från Azure Data Explorer:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
