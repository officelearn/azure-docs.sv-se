---
title: Att läsa och skriva data i HBase - Azure HDInsight Spark | Microsoft Docs
description: Använda Spark HBase-anslutningen för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: acad1058cbff2099a67603cc547a19d6cac022be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Använda Spark för att läsa och skriva HBase-data

Apache HBase efterfrågas vanligtvis med dess lågnivå API (sökningar, hämtar och placeringar) eller med en SQL-syntaxen med Phoenix. Apache ger också Spark HBase Connector, som är ett praktiskt och performant alternativ för att fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Förutsättningar

* Två separata kluster, en HBase och en Spark i HDInsight med Spark 2.1 (HDInsight 3,6) installerat.
* Spark-klustret måste kommunicera direkt med HBase-kluster med minimal svarstid, så är den rekommenderade konfigurationen distribuera båda klustren i samma virtuella nätverk. Mer information finns i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* SSH-åtkomst till varje kluster.
* Åtkomst till standardlagring för varje kluster.

## <a name="overall-process"></a>Övergripande processen

Anvisningar om hur du aktiverar Spark-kluster att fråga ditt HDInsight-kluster är följande:

1. Förbered exempeldata i HBase.
2. Hämta filen hbase-site.XML från mappen HBase-kluster configuration (/ etc/hbase/conf).
3. Placera en kopia av hbase-site.xml i mappen Spark 2-konfiguration (/ etc/spark2/conf).
4. Kör `spark-shell` refererar till Spark HBase koppling av dess Maven samordnar i den `packages` alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med RDD eller DataFrame APIs.

## <a name="prepare-sample-data-in-hbase"></a>Förbereda exempeldata i HBase

I det här steget Skapa och fylla i en enkel tabell i HBase som sedan kan du ställa frågor med Spark.

1. Ansluta till din HBase-kluster med SSH huvudnod. Mer information finns i [Anslut till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Kör HBase-gränssnittet:

        hbase shell

3. Skapa en `Contacts` tabell med kolumnen familjer `Personal` och `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Läsa in några exempel rader med data:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Hämta hbase-site.xml från HBase-kluster

1. Ansluta till din HBase-kluster med SSH huvudnod.
2. Kopiera hbase-site.xml från lokal lagring till roten i HBase-kluster standardlagring:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Navigera till din HBase-kluster med den [Azure-portalen](https://portal.azure.com).
4. Välj Storage-konton. 

    ![Lagringskonton](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Välj lagringskontot i listan med en bock under kolumnen standard.

    ![Standardkontot för lagring](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. Välj BLOB-panelen i rutan Storage-konto.

    ![BLOB-panelen](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Markera den behållare som används av HBase-kluster i listan över behållare.
8. I listan, Välj `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. På panelen Blob-egenskaper väljer du hämta och spara `hbase-site.xml` till en plats på den lokala datorn.

    ![Ladda ned](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Spärra hbase-site.xml Spark-kluster

1. Navigera till din Spark-kluster med den [Azure-portalen](https://portal.azure.com).
2. Välj Storage-konton.

    ![Lagringskonton](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Välj lagringskontot i listan med en bock under kolumnen standard.

    ![Standardkontot för lagring](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. Välj BLOB-panelen i rutan Storage-konto.

    ![BLOB-panelen](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Markera den behållare som används av ditt Spark-kluster i listan över behållare.
6. Välj överför.

    ![Ladda upp](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Välj den `hbase-site.xml` filen som du tidigare har hämtat till din lokala dator.

    ![Överför hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Välj överför.
9. Ansluta till ditt Spark-kluster med SSH huvudnod.
10. Kopiera `hbase-site.xml` från Spark-kluster standardlagring till Spark 2 Konfigurationsmappen på klustrets lokal lagring:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Kör Spark-gränssnittet som refererar till Spark HBase-koppling

1. Ansluta till ditt Spark-kluster med SSH huvudnod.
2. Starta spark shell, ange paketets Spark HBase-anslutningen:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. Håll den här instansen Spark Shell öppet och fortsätta till nästa steg.

## <a name="define-a-catalog-and-query"></a>Definiera en katalog och fråga

I det här steget definierar du ett objekt i katalogen som mappar schemat från Spark till HBase. 

1. Din öppna Spark Shell, kör du följande `import` instruktioner:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Definiera en katalog för tabellen för kontakter som du skapade i HBase:
    1. Definiera ett katalogschema för HBase-tabellen med namnet `Contacts`.
    2. Identifiera rowkey som `key`, och mappa kolumnnamn som används i Spark kolumnfamilj, kolumnnamn och kolumntypen som används i HBase.
    3. Rowkey måste också definieras i detalj som en namngiven kolumn (`rowkey`), som har en viss kolumnfamilj `cf` av `rowkey`.

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

3. Definiera en metod som ger en DataFrame runt din `Contacts` tabellen i HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Skapa en instans av DataFrame:

        val df = withCatalog(catalog)

5. Fråga i DataFrame:

        df.show()

6. Du bör se två rader data:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registrera en tillfällig tabell så att du kan ställa frågor med Spark SQL HBase-tabellen:

        df.registerTempTable("contacts")

8. Utfärda en SQL-fråga mot den `contacts` tabellen:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Du bör se resultat som dessa:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Infoga ny data

1. Om du vill infoga en ny kontaktpost, definiera en `ContactRecord` klass:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Skapa en instans av `ContactRecord` och placera den i en matris:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Spara matris med nya data i HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Granska resultaten:
    
        df.show()

5. Du bör se utdata som ser ut så här:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Nästa steg

* [Spark HBase-koppling](https://github.com/hortonworks-spark/shc)
