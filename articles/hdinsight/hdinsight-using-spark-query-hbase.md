---
title: Använda Spark för att läsa och skriva HBase-data – Azure HDInsight
description: Använd Spark HBase-anslutningen för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: e6b3fc4f9badeedbed55f89702933b41a952977b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180797"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Använda Apache Spark för att läsa och skriva Apache HBase-data

Apache HBase frågas vanligt vis antingen med dess lågnivå-API (genomsökningar, hämtar och placerar) eller med en SQL-syntax som använder Apache Phoenix. Apache ger också Apache Spark HBase-anslutningen, som är ett bekvämt och lämpligt alternativ att fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Förutsättningar

* Två separata HDInsight-kluster distribuerade i samma virtuella nätverk. En HBase och en spark med minst Spark 2,1 (HDInsight 3,6) installerad. Mer information finns i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [URI-schemat](hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta är wasb://för Azure Blob Storage, abfs://för Azure Data Lake Storage Gen2 eller adl://för Azure Data Lake Storage Gen1. Om säker överföring har Aktiver ATS för Blob Storage är URI: `wasbs://`n.  Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Övergripande process

Den övergripande processen för att aktivera Spark-klustret för att fråga ditt HDInsight-kluster är följande:

1. Förbered några exempel data i HBase.
2. Hämta filen HBase-site. XML från HBase-klustrets konfigurations mapp (/etc/HBase/conf).
3. Placera en kopia av HBase-site. xml i din spark 2-installationsmapp (/etc/spark2/conf).
4. Kör `spark-shell` referens för Spark HBase-anslutningen med dess maven-koordinater `packages` i alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med antingen RDD-eller DataFrame-API: erna.

## <a name="prepare-sample-data-in-apache-hbase"></a>Förbereda exempel data i Apache HBase

I det här steget skapar du och fyller i en tabell i Apache HBase som du sedan kan fråga med Spark.

1. `ssh` Använd kommandot för att ansluta till ditt HBase-kluster. Redigera kommandot nedan genom att ersätta `HBASECLUSTER` med namnet på ditt HBase-kluster och ange sedan kommandot:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell` Använd kommandot för att starta det interaktiva HBase-gränssnittet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

3. `create` Använd kommandot för att skapa en HBase-tabell med två kolumn serier. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `put` Använd kommandot för att infoga värden i en angiven kolumn på en angiven rad i en viss tabell. Ange följande kommando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. `exit` Använd kommandot för att stoppa det interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiera HBase-site. xml till Spark-kluster

Kopiera HBase-site. XML från lokal lagring till roten av ditt Spark-klusters standard lagring.  Redigera kommandot nedan för att avspegla konfigurationen.  Sedan anger du kommandot från den öppna SSH-sessionen till HBase-klustret:

| Syntax-värde | Nytt värde|
|---|---|
|[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Ändra för att återspegla din lagring.  Syntaxen nedan är för Blob Storage med säker överföring aktiverat.|
|`SPARK_STORAGE_CONTAINER`|Ersätt med namnet på den standard lagrings behållare som används för Spark-klustret.|
|`SPARK_STORAGE_ACCOUNT`|Ersätt med det standard lagrings konto namn som används för Spark-klustret.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Avsluta sedan ssh-anslutningen till ditt HBase-kluster.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Lägg HBase-site. xml på ditt Spark-kluster

1. Anslut till Head-noden i Spark-klustret med hjälp av SSH.

2. Ange kommandot nedan för att kopiera `hbase-site.xml` från ditt Spark-klusters standard lagring till mappen Spark 2 i klustrets lokala lagrings plats:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Kör Spark-gränssnitt som refererar till Spark HBase-anslutningen

1. Från din öppna SSH-session till Spark-klustret anger du kommandot nedan för att starta ett Spark-gränssnitt:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Behåll den här Spark Shell-instansen öppen och fortsätt till nästa steg.

## <a name="define-a-catalog-and-query"></a>Definiera en katalog och fråga

I det här steget definierar du ett katalog objekt som mappar schemat från Apache Spark till Apache HBase.  

1. I ditt öppna Spark-gränssnitt anger du följande `import` instruktioner:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Ange kommandot nedan för att definiera en katalog för den kontakt tabell som du skapade i HBase:

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

     a. Definiera ett katalog schema för HBase-tabellen med `Contacts`namnet.  
     b. Identifiera rowkey som `key`och mappa kolumn namnen som används i Spark till kolumn serien, kolumn namnet och kolumn typen som används i HBase.  
     c. Rowkey måste också definieras i detalj som en namngiven kolumn (`rowkey`) som har en viss kolumn familj `cf` av `rowkey`.  

3. Ange kommandot nedan för att definiera en metod som tillhandahåller en DataFrame runt din `Contacts` tabell i HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Skapa en instans av DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Fråga DataFrame:

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

7. Registrera en temporär tabell så att du kan fråga HBase-tabellen med Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Utfärda en SQL-fråga mot `contacts` tabellen:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Du bör se resultatet så här:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Infoga nya data

1. Om du vill infoga en ny kontakt post definierar `ContactRecord` du en klass:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Skapa en instans av `ContactRecord` och Lägg den i en matris:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Spara matrisen med nya data till HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Granska resultaten:

    ```scala  
    df.show()
    ```

5. Du bör se utdata som ser ut så här:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

6. Stäng Spark-gränssnittet genom att ange följande kommando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Nästa steg

* [Apache Spark HBase-anslutning](https://github.com/hortonworks-spark/shc)
