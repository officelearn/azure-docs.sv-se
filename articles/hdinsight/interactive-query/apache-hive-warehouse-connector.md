---
title: Integrera Apache Spark och Apache Hive med Hive Warehouse-Anslutningsappen
description: Lär dig hur du integrerar Apache Spark och Apache Hive med Hive Warehouse-anslutningen på Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b2b3d1ac0a7c0e917f87be1dd131120f63a70f8e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142817"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrera Apache Spark och Apache Hive med Hive Warehouse-Anslutningsappen

I Apache Hive Warehouse Connector (programtillägget för hysningsbar Webbinstans) är ett bibliotek som gör det möjligt att enkelt arbeta med Apache Spark och Apache Hive genom att stödja åtgärder som flyttar data mellan dataramar Spark och Hive-tabeller och också dirigera Spark strömmande data till Hive-tabeller. Hive Warehouse-Anslutningsappen fungerar som en brygga mellan Spark och Hive. Den stöder Scala, Java och Python för utveckling.

Hive Warehouse-Anslutningsappen kan du dra nytta av de unika funktionerna för Hive och Spark för att skapa kraftfulla program för stordata. Apache Hive erbjuder stöd för transaktioner för databaser som är atomiska åtgärden, konsekvens, isolering och varaktiga (ACID). Mer information om ACID och transaktioner i Hive finns i [Hive transaktioner](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive erbjuder även detaljerad säkerhetskontroller via Apache Ranger och låg latens Analytical Processing finns inte i Apache Spark.

Apache Spark, har en Structured Streaming API som ger strömmande funktioner som inte finns i Apache Hive. Från och med Hortonworks Data Platform (HDP) 3.0, har Apache Spark och Apache Hive separata metastores, vilket kan göra samverkan svårt. Hive Warehouse-Anslutningsappen gör det enklare att använda Spark och Hive. Biblioteket genom hysningsbar Webbinstans läser in data från LLAP daemons till Spark executors parallellt, vilket gör det mer effektiv och skalbar än en standard JDBC-anslutning från Spark till Hive.

![Arkitektur](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Några av de åtgärder som stöds av Hive Warehouse-anslutningen är:

* Som beskriver en tabell
* Skapa en tabell för ORC-formaterade data
* Att välja Hive-data och hämta en dataram
* Skriver en dataram till Hive i batch
* Köra en Hive-update-instruktion
* Läsa tabelldata från Hive, omvandla det i Spark och skriver dem till en ny Hive-tabell
* Skriver en dataram eller Spark-dataström till Hive med HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector-konfigurationen

Följ dessa steg om du vill konfigurera Hive Warehouse-anslutningen mellan ett Spark och Interactive Query-kluster i Azure HDInsight:

1. Skapa ett HDInsight Spark 4.0-kluster med Azure portal med ett storage-konto och en anpassad Azure-nätverket. Information om hur du skapar ett kluster i Azure-nätverk finns i [lägga till HDInsight till ett befintligt virtuellt nätverk](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Skapa ett HDInsight Interactive Query (LLAP) 4.0-kluster med Azure portal med samma lagringskonto och Azure-nätverk som Spark-klustret.
1. Kopiera innehållet i den `/etc/hosts` fil på headnode0 av klustret för interaktiv fråga att den `/etc/hosts` fil på headnode0 för ditt Spark-kluster. Det här steget gör att Spark-kluster att matcha IP-adresserna för noderna i klustret för interaktiv fråga. Visa innehållet i den uppdaterade filen med `cat /etc/hosts`. Utdata bör se ut ungefär som visas i skärmbilden nedan.

    ![Visa i hosts-filen](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Konfigurera inställningar för Spark-kluster genom att göra följande: 
    1. Gå till Azure portal, Välj HDInsight-kluster och sedan klicka på klusternamnet.
    1. Till höger under **Klusterinstrumentpaneler**väljer **Ambari home**.
    1. I Ambari-webbgränssnittet, klickar du på **SPARK2** > **PEERINGKONFIGURATIONER** > **anpassade spark2-standardvärden**.

        ![Spark2 Ambari-konfiguration](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Ange `spark.hadoop.hive.llap.daemon.service.hosts` till samma värde som egenskapen **LLAP appnamn** under **avancerade hive interaktiva env**. Till exempel, `@llap0`

    1. Ange `spark.sql.hive.hiveserver2.jdbc.url` till JDBC-anslutningssträngen som ansluter till Hiveserver2 på klustret för interaktiv fråga. Anslutningssträngen för ditt kluster ska se ut som nedan URI. `CLUSTERNAME` är namnet på ditt Spark-kluster och `user` och `password` parametrar är inställda på rätt värden för ditt kluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC-Webbadressen ska innehålla autentiseringsuppgifter för att ansluta till Hiveserver2, inklusive ett användarnamn och lösenord.

    1. Ange `spark.datasource.hive.warehouse.load.staging.dir` till en lämplig uppsamlingskatalogen för HDFS-kompatibla. Om du har två olika kluster ska uppsamlingskatalogen vara en mapp i uppsamlingskatalogen på LLAP klustrets lagringskonto så att HiveServer2 har åtkomst till den. Till exempel `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` där `STORAGE_ACCOUNT_NAME` är namnet på lagringskontot som används av klustret, och `STORAGE_CONTAINER_NAME` är namnet på lagringsbehållaren.

    1. Ange `spark.datasource.hive.warehouse.metastoreUri` med värdet för metaarkiv URI: N för klustret för interaktiv fråga. Du hittar metastoreUri för LLAP klustret genom att leta efter den **hive.metastore.uris** -egenskapen i Ambari UI för din LLAP kluster **Hive** > **AVANCERAT**  >  **Allmänt**. Värdet ser ut ungefär som följande URI: N:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Ange `spark.security.credentials.hiveserver2.enabled` till `false` distribuera läge för YARN-klienten.
    1. Ange `spark.hadoop.hive.zookeeper.quorum` till zookeeperkvorum på LLAP klustret. Du hittar zookeeperkvorum för LLAP klustret genom att leta efter den **hive.zookeeper.quorum** -egenskapen i Ambari UI för din LLAP kluster **Hive** > **AVANCERAT**  >  **Avancerade hive-plats**. Värdet ser ut ungefär som följande sträng:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Om du vill testa konfigurationen av din Hive Warehouse-Anslutningsappen, följer du stegen i avsnittet [anslutande och körs frågor](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Med hjälp av Hive Warehouse-Anslutningsappen

### <a name="connecting-and-running-queries"></a>Ansluta och köra frågor

Du kan välja mellan några olika metoder för att ansluta till dina interaktiva frågekluster och köra frågor med hjälp av Hive Warehouse-Anslutningsappen. Metoder som stöds omfattar följande verktyg:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alla exempel som är tillgängliga i den här artikeln ska utföras via spark-shell.

Om du vill starta en spark-shell-session, gör du följande:

1. SSH till huvudnoden för ditt kluster. Mer information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Ändra i rätt katalog genom att skriva `cd /usr/hdp/current/hive_warehouse_connector` eller ange den fullständiga sökvägen till alla jar-filer som används som parametrar i spark-shell-kommando.
1. Ange följande kommando för att starta spark-shell:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Du ser ett välkomstmeddelande och en `scala>` kommandotolk där du kan ange kommandon.

1. När du har startat spark-shell, kan du starta en Hive-Warehouse Connector-instans med hjälp av följande kommandon:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Ansluta och köra frågor på Enterprise Security Package (ESP)-kluster

Enterprise Security Package (ESP) tillhandahåller funktioner i företagsklass som Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för Apache Hadoop-kluster i Azure HDInsight. Läs mer på ESP [en introduktion till Apache Hadoop-säkerhet med Enterprise Security Package](../domain-joined/apache-domain-joined-introduction.md).

1. Följ de första stegen 1 och 2 under [anslutande och körs frågor](#connecting-and-running-queries).
1. Typ `kinit` och logga in med en domänanvändare.
1. Starta spark-shell med en fullständig lista över konfigurationsparametrar som visas nedan. Alla värden i versaler mellan vinkelparenteser måste anges baserat på ditt kluster. Om du vill ta reda på vilka värden som ska ange för någon av nedanstående parametrar finns i avsnittet på [Hive Warehouse Connector-konfigurationen](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Skapa Spark-dataramar från Hive-frågor

Resultatet av alla frågor med hjälp av biblioteket genom hysningsbar Webbinstans returneras som en dataram. I följande exempel visar hur du skapar en fråga.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Resultatet av frågan är Spark dataramar som kan användas med Spark-bibliotek som MLIB och SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Skriva ut Spark dataramar till Hive-tabeller

Spark stöd inte har inbyggt för skrivning till Hives hanterade ACID-tabeller. Med hjälp av programtillägget för hysningsbar Webbinstans, men kan du skriva ut alla dataram i en Hive-tabell. Du kan se den här funktionen fungerar i följande exempel:

1. Skapa en tabell med namnet `sampletable_colorado` och ange dess kolumner med hjälp av följande kommando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtrera tabellen `hivesampletable` där kolumnen `state` är lika med `Colorado`. Den här frågan för Hive-tabell returneras som en Spark DataFrame. Sedan dataramen som sparas i Hive-tabell `sampletable_colorado` med hjälp av den `write` funktion.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Du kan se den resulterande tabellen i skärmbilden nedan.

![Visa resulterande tabell](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturerad direktuppspelning skrivningar

Du kan använda Spark-strömning för att skriva data till Hive-tabeller med Hive Warehouse-Anslutningsappen.

Följ stegen nedan för att skapa ett exempel på Hive Warehouse-Anslutningsappen som matar in data från en dataström med Spark på localhost port 9999 till en Hive-tabell.

1. Öppna en terminal på Spark-kluster.
1. Börja spark stream med följande kommando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Generera data för Spark-dataström som du skapade genom att göra följande:
    1. Öppna en annan terminal i samma Spark-kluster.
    1. I Kommandotolken, Skriv `nc -lk 9999`. Det här kommandot använder verktyget netcat för att skicka data från kommandoraden till den angivna porten.
    1. Ange de ord som du vill använda Spark stream att mata in, följt av radmatning.
        ![indata till en dataström med spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Skapa en ny Hive-tabell för att lagra strömmande data. Spark-gränssnittet och skriver du följande kommandon:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Skriva strömmande data till den nyligen skapade tabellen med följande kommando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Den `metastoreUri` och `database` alternativ måste anges för närvarande manuellt på grund av ett känt problem i Apache Spark. Mer information om det här problemet finns i [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Du kan visa data som infogats i tabellen med följande kommando:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Skydda data på ESP Spark-kluster

1. Skapa en tabell `demo` med lite exempeldata genom att ange följande kommandon:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visa tabellens innehåll med följande kommando. Innan du tillämpar principen på `demo` tabell visar den fullständiga kolumnen.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demo tabell innan du tillämpar ranger-princip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Använda en kolumn som maskera princip som endast visar de sista fyra tecknen i kolumnen.  
    1. Gå till Ranger Admin-Gränssnittet på `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klicka på Hive-tjänsten för ditt kluster under **Hive**.
        ![demo tabell innan du tillämpar ranger-princip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klicka på den **maskera** fliken och sedan **Lägg till ny princip** ![principlista](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Ange ett namn för önskad princip. Välj databas: **Standard**, Hive-tabell: **demo**, Hive-kolumn: **namn**, användare: **rsadmin2**, åtkomsttyper: **Välj**, och **Partiella mask: Visa senaste 4** från den **Välj maskera alternativ** menyn. Klicka på **Lägg till**.
                ![principlista](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visa tabellens innehåll igen. Efter att ha tillämpat ranger-principen, ser vi endast de sista fyra tecknen i kolumnen.

    ![demo tabell efter ranger-principer tillämpas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Nästa steg

* [Använda interaktiv fråga med HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Exempel på interagera med Hive Warehouse-anslutningen med hjälp av Zeppelin, Livy, spark-submit, och pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
