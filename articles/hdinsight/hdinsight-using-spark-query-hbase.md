---
title: Använd Spark för att läsa och skriva HBase-data – Azure HDInsight
description: Använda HBase Spark-Anslutningsappen för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
services: hdinsight
author: maxluk
ms.author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5123a95852fae58adf0b4a4684b012d3b9c71e3b
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144779"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Använda Spark för att läsa och skriva HBase-data

Apache HBase är vanligtvis efterfrågas med dess lågnivå-API (genomsökningar, hämtar och placerar) eller med en SQL-syntax med Phoenix. Apache innehåller också Spark HBase-anslutningstjänsten, som är ett praktiskt och högpresterande alternativ för att fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Förutsättningar

* Två separata HDInsight-kluster, en HBase och en Spark med Spark 2.1 (HDInsight 3.6) installerat.
* Spark-klustret behöver för att kommunicera direkt med HBase-kluster med minimal svarstid, så den rekommenderade konfigurationen är att distribuera båda klustren i samma virtuella nätverk. Mer information finns i [skapa Linux-baserade kluster i HDInsight med Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).
* SSH-åtkomst till varje kluster.
* Åtkomst till standardlagring för varje kluster.

## <a name="overall-process"></a>Övergripande processen

Den övergripande processen för att aktivera ditt Spark-kluster att fråga ditt HDInsight-kluster är följande:

1. Förbered lite exempeldata i HBase.
2. Hämta filen hbase-site.xml från mappen HBase-kluster-konfiguration (/ conf/etc/hbase).
3. Placera en kopia av hbase-site.xml i mappen Spark-2-konfiguration (/ etc/spark2/conf).
4. Kör `spark-shell` refererar till Spark-Anslutningsappen för HBase av dess Maven koordinerar i den `packages` alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med hjälp av antingen RDD eller DataFrame APIs.

## <a name="prepare-sample-data-in-hbase"></a>Förbereda exempeldata i HBase

I det här steget ska du skapa och fylla i en enkel tabell i HBase, som sedan kan du ställa frågor med Spark.

1. Ansluta till klustrets huvudnod HBase-kluster med SSH. Mer information finns i [Anslut till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Kör HBase-gränssnittet:

        hbase shell

3. Skapa en `Contacts` tabellen med kolumnserier `Personal` och `Office`:

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

1. Ansluta till klustrets huvudnod HBase-kluster med SSH.
2. Kopiera hbase-site.xml från lokal lagring till roten i din HBase-kluster standardlagring:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Gå till din HBase-kluster med den [Azure-portalen](https://portal.azure.com).
4. Välj lagringskonton. 

    ![Lagringskonton](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Välj lagringskontot i listan med en bock under kolumnen standard.

    ![Standardkontot för lagring](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. Välj panelen Blobar på rutan för lagring.

    ![Blobbar-panel](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. I listan över behållare, väljer du den behållare som används av HBase-kluster.
8. I listan, Välj `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. På panelen Blob-egenskaper väljer du ladda ned och spara `hbase-site.xml` till en plats på den lokala datorn.

    ![Ladda ned](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placera hbase-site.xml på Spark-kluster

1. Gå till ditt Spark-kluster med den [Azure-portalen](https://portal.azure.com).
2. Välj lagringskonton.

    ![Lagringskonton](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Välj lagringskontot i listan med en bock under kolumnen standard.

    ![Standardkontot för lagring](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. Välj panelen Blobar på rutan för lagring.

    ![Blobbar-panel](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. I listan över behållare, väljer du den behållare som används av ditt Spark-kluster.
6. Välj överför.

    ![Ladda upp](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Välj den `hbase-site.xml` filen som du tidigare har hämtat till den lokala datorn.

    ![Ladda upp hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Välj överför.
9. Ansluta till huvudnoden för ditt Spark-kluster med SSH.
10. Kopiera `hbase-site.xml` från standardlagring för ditt Spark-kluster till Spark 2 Konfigurationsmappen på klustrets lokal lagring:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Köra Spark-Shell som refererar till Spark HBase-Anslutningsappen

1. Ansluta till huvudnoden för ditt Spark-kluster med SSH.
2. Starta spark-shell, som att ange Spark-Anslutningsappen för HBase-paketet:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/content/groups/public/

3. Låt den här instansen av Spark-Shell öppet och fortsätta till nästa steg.

## <a name="define-a-catalog-and-query"></a>Definiera en katalog och fråga

I det här steget definierar du ett katalogobjekt som mappar schemat från Spark till HBase. 

1. Dina öppna Spark-Shell och kör följande `import` instruktioner:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Definiera en katalog för tabellen för kontakter som du skapade i HBase:
    1. Definiera ett katalogschema för HBase-tabellen med namnet `Contacts`.
    2. Identifiera rowkey som `key`, och mappa de kolumnnamn som använts i Spark till kolumnserie, kolumnnamn och kolumntyp i HBase.
    3. Rowkey har också definieras i detalj som en namngiven kolumn (`rowkey`), som har en viss kolumnfamilj `cf` av `rowkey`.

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

3. Definiera en metod som ger en dataram runt dina `Contacts` tabellen i HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Skapa en instans av dataramen:

        val df = withCatalog(catalog)

5. Nu när dataramen-fråga:

        df.show()

6. Du bör se två rader med data:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registrera en tillfällig tabell så att du kan ställa frågor med Spark SQL HBase-tabellen:

        df.registerTempTable("contacts")

8. Utfärda en SQL-fråga mot den `contacts` tabell:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Du bör se resultat som dessa:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Infoga nya data

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

* [Spark-Anslutningsappen för HBase](https://github.com/hortonworks-spark/shc)
