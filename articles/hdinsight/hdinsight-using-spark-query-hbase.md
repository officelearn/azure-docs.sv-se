---
title: Använd Spark för att läsa och skriva HBase-data – Azure HDInsight
description: Använda HBase Spark-Anslutningsappen för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 0ba61dc266add48577c3a382465ecb2cec9d2a05
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188041"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Använda Apache Spark för att läsa och skriva Apache HBase-data

Apache HBase är vanligtvis efterfrågas med dess lågnivå-API (genomsökningar, hämtar och placerar) eller med en SQL-syntax som använder Apache Phoenix. Apache innehåller också de Apache Spark HBase anslutningstjänsten, som är ett praktiskt och högpresterande alternativ för att fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Förutsättningar

* Två separata HDInsight-kluster, en HBase och en Spark med minst Spark 2.1 (HDInsight 3.6) installerat.
* Spark-klustret behöver för att kommunicera direkt med HBase-kluster med minimal svarstid, så den rekommenderade konfigurationen är att distribuera båda klustren i samma virtuella nätverk. Mer information finns i [skapa Linux-baserade kluster i HDInsight med Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* Den [URI-schema](/hdinsight-hadoop-linux-information#URI-and-scheme.md) för ditt kluster primär lagring. Detta skulle vara wasb: / / för Azure Blob Storage, abfs: / / för Azure Data Lake Storage Gen2 eller adl: / / för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Blob Storage eller Data Lake Storage Gen2, URI: N blir wasbs: / / eller abfss: / /, respektive Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Övergripande processen

Den övergripande processen för att aktivera ditt Spark-kluster att fråga ditt HDInsight-kluster är följande:

1. Förbered lite exempeldata i HBase.
2. Hämta filen hbase-site.xml från mappen HBase-kluster-konfiguration (/ conf/etc/hbase).
3. Placera en kopia av hbase-site.xml i mappen Spark-2-konfiguration (/ etc/spark2/conf).
4. Kör `spark-shell` refererar till Spark-Anslutningsappen för HBase av dess Maven koordinerar i den `packages` alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med hjälp av antingen RDD eller DataFrame APIs.

## <a name="prepare-sample-data-in-apache-hbase"></a>Förbereda exempeldata i Apache HBase

I det här steget ska du skapa och fylla i en enkel tabell i Apache HBase, som sedan kan du ställa frågor med Spark.

1. Ansluta till klustrets huvudnod HBase-kluster med SSH. Mer information finns i [Anslut till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  Redigera kommandot nedan genom att ersätta `HBASECLUSTER` med namnet på din HBase-kluster `sshuser` med det ssh användarens kontonamn och ange sedan kommandot.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Ange kommandot nedan för att starta HBase-gränssnittet:

        hbase shell

3. Ange kommandot nedan för att skapa en `Contacts` tabellen med kolumnserier `Personal` och `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Ange kommandona nedan för att läsa in några exempel rader med data:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Ange kommandot nedan för att avsluta HBase-gränssnittet:

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiera hbase-site.xml till Spark-kluster
Kopiera hbase-site.xml från lokal lagring till roten för ditt Spark-kluster standardlagring.  Redigera kommandot nedan för att återspegla din konfiguration.  Ange sedan kommandot från din öppna SSH-session till HBase-kluster:

| Syntaxvärde | Nytt värde|
|---|---|
|[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Ändra för att återspegla din lagring.  Syntaxen nedan är för blob storage med säker överföring.|
|`SPARK_STORAGE_CONTAINER`|Ersätt med standardnamnet på lagringsbehållaren används för Spark-klustret.|
|`SPARK_STORAGE_ACCOUNT`|Ersätt med standard lagringskontonamn används för Spark-klustret.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placera hbase-site.xml på Spark-kluster

1. Ansluta till huvudnoden för ditt Spark-kluster med SSH.

2. Ange kommandot nedan för att kopiera `hbase-site.xml` från standardlagring för ditt Spark-kluster till Spark 2 Konfigurationsmappen på klustrets lokal lagring:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Köra Spark-Shell som refererar till Spark HBase-Anslutningsappen

1. Ange kommandot nedan för att starta en spark-shell från din öppna SSH-session till Spark-kluster:

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Låt den här instansen av Spark-Shell öppet och fortsätta till nästa steg.

## <a name="define-a-catalog-and-query"></a>Definiera en katalog och fråga

I det här steget definierar du ett katalogobjekt som mappar schemat från Apache Spark till Apache HBase.  

1. Ange följande i din öppna Spark-Shell `import` instruktioner:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Ange kommandot nedan för att definiera en katalog för tabellen kontakter du skapade i HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Koden utför följande:  

     a. Definiera ett katalogschema för HBase-tabellen med namnet `Contacts`.  
     b. Identifiera rowkey som `key`, och mappa de kolumnnamn som använts i Spark till kolumnserie, kolumnnamn och kolumntyp i HBase.  
     c. Rowkey har också definieras i detalj som en namngiven kolumn (`rowkey`), som har en viss kolumnfamilj `cf` av `rowkey`.  

3. Ange kommandot nedan för att definiera en metod som ger en dataram runt dina `Contacts` tabellen i HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Skapa en instans av dataramen:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Nu när dataramen-fråga:

    ```scala
    df.show()
    ```

6. Du bör se två rader med data:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registrera en tillfällig tabell så att du kan ställa frågor med Spark SQL HBase-tabellen:

    ```scala
    df.createTempView("contacts")
    ```

8. Utfärda en SQL-fråga mot den `contacts` tabell:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Du bör se resultat som dessa:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Infoga nya data

1. Om du vill infoga en ny kontaktpost, definiera en `ContactRecord` klass:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Skapa en instans av `ContactRecord` och placera den i en matris:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Spara matris med nya data i HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Granska resultaten:

    ```scala  
    df.show()
    ```

5. Du bör se utdata som ser ut så här:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Nästa steg

* [Apache Spark HBase Connector](https://github.com/hortonworks-spark/shc)
