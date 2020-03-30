---
title: Apache Spark & Hive - Hive Warehouse Connector - Azure HDInsight
description: Lär dig hur du integrerar Apache Spark och Apache Hive med Hive Warehouse Connector på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252410"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrera Apache Spark och Apache Hive med Hive Warehouse Connector

Apache Hive Warehouse Connector (HWC) är ett bibliotek som gör att du lättare kan arbeta med Apache Spark och Apache Hive genom att stödja uppgifter som att flytta data mellan Spark DataFrames och Hive-tabeller och även styra Spark-strömmande data till Hive-tabeller. Hive Warehouse Connector fungerar som en brygga mellan Spark och Hive. Den stöder Scala, Java och Python för utveckling.

Hive Warehouse Connector gör att du kan dra nytta av de unika funktionerna i Hive och Spark för att skapa kraftfulla big-data-program. Apache Hive erbjuder stöd för databastransaktioner som är atomära, konsekventa, isolerade och hållbara (ACID). Mer information om ACID och transaktioner i Hive finns i [Hive-transaktioner](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive erbjuder också detaljerade säkerhetskontroller via Apache Ranger och Analytisk bearbetning med låg latens som inte är tillgänglig i Apache Spark.

Apache Spark, har ett strukturerat streaming-API som ger streamingfunktioner som inte är tillgängliga i Apache Hive. Från och med HDInsight 4.0, Apache Spark 2.3.1 och Apache Hive 3.1.0 har separata metabutiker, vilket kan göra interoperabilitet svårt. Hive Warehouse Connector gör det enklare att använda Spark och Hive tillsammans. HWC-biblioteket läser in data från LLAP-demoner till Spark-körmaskiner parallellt, vilket gör det mer effektivt och skalbart än att använda en vanlig JDBC-anslutning från Spark till Hive.

![hive lagerkoppling arkitektur](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Några av de åtgärder som stöds av Hive Warehouse Connector är:

* Beskriva en tabell
* Skapa en tabell för ORC-formaterade data
* Välja Hive-data och hämta en DataFrame
* Skriva en DataFrame till Hive i batch
* Köra ett Hive-uppdateringsmeddelande
* Läsa tabelldata från Hive, omvandla dem i Spark och skriva den till en ny Hive-tabell
* Skriva en DataFrame- eller Spark-ström till Hive med HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Inställningar för Hive Warehouse Connector

Så här konfigurerar du Hive Warehouse Connector mellan ett Spark och Interactive Query-kluster i Azure HDInsight:

### <a name="create-clusters"></a>Skapa kluster

1. Skapa ett HDInsight Spark **4.0-kluster** med ett lagringskonto och ett anpassat virtuellt Azure-nätverk. Information om hur du skapar ett kluster i ett virtuellt Azure-nätverk finns i [Lägga till HDInsight i ett befintligt virtuellt nätverk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Skapa ett LLAP-kluster (HDInsight Interactive Query) **4.0** med samma lagringskonto och virtuella Azure-nätverk som Spark-klustret.

### <a name="modify-hosts-file"></a>Ändra hosts-fil

Kopiera nodinformationen `/etc/hosts` från filen på headnode0 i interactive query-klustret `/etc/hosts` och sammanfoga informationen till filen på huvudnod0 i Spark-klustret. Det här steget gör det möjligt för Spark-klustret att matcha IP-adresser för noderna i interactive query-klustret. Visa innehållet i den `cat /etc/hosts`uppdaterade filen med . Den slutliga produktionen bör se ut ungefär som vad som visas i skärmdumpen nedan.

![programa ware lageranslutningen värd fil](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Samla in preliminär information

#### <a name="from-your-interactive-query-cluster"></a>Från ditt interaktiva frågekluster

1. Navigera till klustrets Apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `LLAPCLUSTERNAME` Hive-sida med var är namnet på ditt interaktiva frågekluster.

1. Navigera till **Avancerad** > **allmän** > **hive.metastore.uris** och notera värdet. Värdet kan likna: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Navigera till **Avancerad** > **avancerad hive-site** > **hive.zookeeper.quorum** och notera värdet. Värdet kan likna: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>Från apache spark-klustret

1. Navigera till klustrets Apache Ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `SPARKCLUSTERNAME` Hive-sida med var är namnet på apache spark-klustret.

1. Navigera till **Avancerad** > **avancerad hive-interaktiv-webbplats** > **hive.llap.daemon.service.hosts** och notera värdet. Värdet kan likna: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Konfigurera inställningar för Spark-kluster

Från ditt Spark Ambari-webbgränssnitt navigerar du till **Spark2** > **CONFIGS** > **Custom spark2-standardvärden**.

![Apache Ambari Spark2 konfiguration](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Välj **Lägg till egenskap...** efter behov för att lägga till/uppdatera följande:

| Nyckel | Värde |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Värdet som du fick tidigare från **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Ange till JDBC-anslutningssträngen, som ansluter till Hiveserver2 i klustret Interaktiv fråga. ERSÄTT `LLAPCLUSTERNAME` med namnet på ditt interaktiva frågekluster. Ersätt `PWD` med det faktiska lösenordet.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Ställ in på en lämplig HDFS-kompatibel mellanlagringskatalog. Om du har två olika kluster bör mellanlagringskatalogen vara en mapp i mellanlagringskatalogen för LLAP-klustrets lagringskonto så att HiveServer2 har åtkomst till den.  Ersätt `STORAGE_ACCOUNT_NAME` med namnet på det lagringskonto som `STORAGE_CONTAINER_NAME` används av klustret och med namnet på lagringsbehållaren.|
|`spark.datasource.hive.warehouse.metastoreUri`|Värdet som du fick tidigare från **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`för YARN-klientdriftmatningsläge.|
|`spark.hadoop.hive.zookeeper.quorum`|Värdet du fick tidigare från **hive.zookeeper.quorum**.|

Spara ändringar och starta om komponenter efter behov.

## <a name="using-the-hive-warehouse-connector"></a>Använda Hive Warehouse Connector

### <a name="connecting-and-running-queries"></a>Ansluta och köra frågor

Du kan välja mellan några olika metoder för att ansluta till ditt interaktiva frågekluster och köra frågor med Hive Warehouse Connector. Metoder som stöds omfattar följande verktyg:

* [gnistskal](../spark/apache-spark-shell.md)
* Pyspark (på en)
* gnista-skicka
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alla exempel i den här artikeln kommer att utföras via spark-shell.

Så här startar du en spark-shell-session:

1. SSH i headnod för din Apache Spark kluster. Mer information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ange följande kommando för att starta gnistskalet:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Du kommer att se `scala>` ett välkomstmeddelande och en fråga där du kan ange kommandon.

1. När du har startat gnistskalet kan en Hive Warehouse Connector-instans startas med följande kommandon:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Ansluta och köra frågor i ESP-kluster (Enterprise Security Package)

Enterprise Security Package (ESP) tillhandahåller funktioner i företagsklass som Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomstkontroll för Apache Hadoop-kluster i Azure HDInsight. Mer information om ESP finns [i Använda Enterprise Security Package i HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH i headnod för din Apache Spark kluster. Mer information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Skriv `kinit` och logga in med en domänanvändare.

1. Starta gnistskalet med den fullständiga listan över konfigurationsparametrar som visas nedan. Alla värden i versaler mellan vinkelparenteser måste anges baserat på klustret. Om du behöver ta reda på de värden som ska matas in för någon av parametrarna nedan läser du avsnittet om inställningarna för [Hive Warehouse Connector](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Skapa Spark DataFrames från Hive-frågor

Resultatet av alla frågor som använder HWC-biblioteket returneras som en DataFrame. Följande exempel visar hur du skapar en grundläggande fråga.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Resultatet av frågan är Spark DataFrames, som kan användas med Spark-bibliotek som MLIB och SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Skriva ut Spark DataFrames till Hive-tabeller

Spark stöder inte inbyggt skrivande till Hive's managed ACID tabeller. Med HWC kan du dock skriva ut alla DataFrame till en Hive-tabell. Du kan se den här funktionen på jobbet i följande exempel:

1. Skapa en `sampletable_colorado` tabell som anropas och ange dess kolumner med följande kommando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrera tabellen `hivesampletable` där `state` kolumnen `Colorado`är lika med . Den här frågan i Hive-tabellen returneras som en Spark DataFrame. Sedan sparas DataFrame i tabellen `sampletable_colorado` Hive med `write` hjälp av funktionen.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Visa resultaten med följande kommando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![hive lagerkoppling visa hive-tabell](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturerade strömmande skrivningar

Med Hive Warehouse Connector kan du använda Spark-direktuppspelning för att skriva data till Hive-tabeller.

Följ stegen nedan för att skapa ett Hive Warehouse Connector-exempel som förtär data från en Spark-ström på localhost-port 9999 till en Hive-tabell.

1. Följ stegen under [Ansluta och köra frågor](#connecting-and-running-queries).

1. Börja gnistströmmen med följande kommando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generera data för Spark-strömmen som du skapade genom att göra följande steg:
    1. Öppna en andra SSH-session på samma Spark-kluster.
    1. Skriv i `nc -lk 9999`kommandotolken . Det här kommandot använder netcat-verktyget för att skicka data från kommandoraden till den angivna porten.

1. Återgå till den första SSH-sessionen och skapa en ny Hive-tabell för att lagra strömmande data. Vid gnistskalet anger du följande kommando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Skriv sedan strömmande data till den nyligen skapade tabellen med följande kommando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Alternativen `metastoreUri` `database` och måste ställas in manuellt på grund av ett känt problem i Apache Spark. Mer information om problemet finns i [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Återgå till den andra SSH-sessionen och ange följande värden:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Återgå till den första SSH-sessionen och notera den korta aktiviteten. Använd följande kommando för att visa data:

    ```scala
    hive.table("stream_table").show()
    ```

Använd **Ctrl + C** för att stoppa netcat på den andra SSH-sessionen. Används `:q` för att avsluta gnistskalet på den första SSH-sessionen.

### <a name="securing-data-on-spark-esp-clusters"></a>Skydda data på Spark ESP-kluster

1. Skapa en `demo` tabell med några exempeldata genom att ange följande kommandon:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visa tabellens innehåll med följande kommando. Innan du använder `demo` principen visar tabellen hela kolumnen.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demotabell innan du använder ranger-principen](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Använd en kolumnmaskeringsprincip som bara visar de fyra sista tecknen i kolumnen.  
    1. Gå till Ranger Admin `https://CLUSTERNAME.azurehdinsight.net/ranger/`UI på .
    1. Klicka på Hive-tjänsten för ditt kluster under **Hive**.
        ![servicehanterare för ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klicka på fliken **Maskering** och lägg sedan **till ny princip**

        ![hive lagerkopplingsintervaller hive principlista](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Ange ett önskat principnamn. Välj databas: **Standard**, Hive-tabell: **demo**, Hive-kolumn: **namn**, Användare: **rsadmin2**, Åtkomsttyper: **välj**och **Partiell mask: visa sista 4** på menyn **Välj maskeringsalternativ.** Klicka på **Lägg till**.
                ![skapa princip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visa innehållet i tabellen igen. När du har tillämpat ranger-principen kan vi bara se de fyra sista tecknen i kolumnen.

    ![demotabell efter tillämpning av rangerprincip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Nästa steg

* [Använda Interactive Query i HDInsight](./apache-interactive-query-get-started.md)
* [Exempel på interaktion med Hive Warehouse Connector med Zeppelin, Livy, spark-submit och pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
