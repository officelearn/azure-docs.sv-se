---
title: Använd Spark för att läsa och skriva HBase-data - Azure HDInsight
description: Använd Spark HBase Connector för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623091"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Använda Apache Spark för att läsa och skriva Apache HBase-data

Apache HBase är vanligtvis efterfrågas antingen med sin låg nivå API (skannar, får och sätter) eller med en SQL-syntax med Apache Phoenix. Apache tillhandahåller också Apache Spark HBase Connector, vilket är ett bekvämt och högpresterande alternativ till fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Krav

* Två separata HDInsight-kluster som distribueras i samma [virtuella nätverk](./hdinsight-plan-virtual-network-deployment.md). En HBase och en Spark med minst Spark 2.1 (HDInsight 3.6) installerad. Mer information finns [i Skapa Linux-baserade kluster i HDInsight med Hjälp av Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [URI-schemat](hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Det här schemat skulle vara wasb:// för Azure Blob Storage, abfs:// för Azure Data Lake Storage Gen2 eller adl:// för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Blob `wasbs://`Storage, skulle URI vara .  Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Övergripande process

Högnivåprocessen för att aktivera Spark-klustret för att fråga ditt HDInsight-kluster är följande:

1. Förbered några exempeldata i HBase.
2. Hämta filen hbase-site.xml från konfigurationsmappen för HBase-kluster (/etc/hbase/conf).
3. Placera en kopia av hbase-site.xml i konfigurationsmappen Spark 2 (/etc/spark2/conf).
4. Kör `spark-shell` refererar till Spark HBase Connector med dess `packages` Maven-koordinater i alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med hjälp av api:er för fjärrskrivbordsd eller DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Förbereda exempeldata i Apache HBase

I det här steget skapar och fyller du i en tabell i Apache HBase som du sedan kan fråga med Spark.

1. Använd `ssh` kommandot för att ansluta till HBase-klustret. Redigera kommandot nedan `HBASECLUSTER` genom att ersätta med namnet på HBase-klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Använd `hbase shell` kommandot för att starta det interaktiva HBase-skalet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

3. Använd `create` kommandot för att skapa en HBase-tabell med familjer med två kolumner. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Använd `put` kommandot för att infoga värden vid en angiven kolumn på en angiven rad i en viss tabell. Ange följande kommando:

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

5. Använd `exit` kommandot för att stoppa det interaktiva HBase-skalet. Ange följande kommando:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Kopiera hbase-site.xml till Spark-kluster

Kopiera hbase-site.xml från lokal lagring till roten för Spark-klustrets standardlagring.  Redigera kommandot nedan för att återspegla din konfiguration.  Från den öppna SSH-sessionen till HBase-klustret anger du sedan kommandot:

| Syntaxvärde | Nytt värde|
|---|---|
|[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Ändra för att återspegla din lagring.  Syntaxen nedan är för bloblagring med säker överföring aktiverad.|
|`SPARK_STORAGE_CONTAINER`|Ersätt med standardlagringsbehållarens namn som används för Spark-klustret.|
|`SPARK_STORAGE_ACCOUNT`|Ersätt med standardlagringskontonamnet som används för Spark-klustret.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Avsluta sedan ssh-anslutningen till HBase-klustret.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placera hbase-site.xml i Spark-klustret

1. Anslut till huvudnoden i Spark-klustret med SSH. Redigera kommandot nedan `SPARKCLUSTER` genom att ersätta med namnet på Spark-klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Ange kommandot nedan `hbase-site.xml` om du vill kopiera från Spark-klustrets standardlagring till konfigurationsmappen Spark 2 i klustrets lokala lagring:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Kör Spark Shell som refererar till Spark HBase Connector

1. Från din öppna SSH-session till Spark-klustret anger du kommandot nedan för att starta ett sparkskal:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Håll den här Spark Shell-förekomsten öppen och fortsätt till nästa steg.

## <a name="define-a-catalog-and-query"></a>Definiera en katalog och en fråga

I det här steget definierar du ett katalogobjekt som mappar schemat från Apache Spark till Apache HBase.  

1. I ditt öppna Spark Shell `import` anger du följande satser:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Ange kommandot nedan för att definiera en katalog för tabellen Kontakter som du skapade i HBase:

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

    Koden gör följande:  

     a. Definiera ett katalogschema för HBase-tabellen med namnet `Contacts`.  
     b. Identifiera radnyckeln `key`som och mappa kolumnnamnen som används i Spark till kolumnfamiljen, kolumnnamnet och kolumntypen som används i HBase.  
     c. Radnyckeln måste också definieras i detalj som`rowkey`en namngiven kolumn `cf` `rowkey`( ), som har en specifik kolumnfamilj med .  

1. Ange kommandot nedan för att definiera en metod `Contacts` som tillhandahåller en DataFrame runt tabellen i HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Skapa en instans av DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Fråga DataFrame:

    ```scala
    df.show()
    ```

    Du bör se två rader med data:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registrera en temporär tabell så att du kan fråga HBase-tabellen med Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Utfärda en SQL-fråga mot `contacts` tabellen:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Du bör se resultat som dessa:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Infoga nya data

1. Om du vill infoga en `ContactRecord` ny kontaktpost definierar du en klass:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Skapa en `ContactRecord` förekomst av och placera den i en matris:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Spara matrisen med nya data i HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Undersök resultaten:

    ```scala  
    df.show()
    ```

    Du bör se utdata som ser ut så här:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Stäng gnistskalet genom att ange följande kommando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Nästa steg

* [Apache Spark HBase-kontakt](https://github.com/hortonworks-spark/shc)
