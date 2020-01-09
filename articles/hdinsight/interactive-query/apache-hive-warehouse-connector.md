---
title: Apache Spark & Hive – Hive lager koppling – Azure HDInsight
description: Lär dig hur du integrerar Apache Spark och Apache Hive med Hive Warehouse Connector på Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 47bcc9a4f906fa1e0cc0560cdbd2e0cebec481ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435383"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrera Apache Spark och Apache Hive med Hive-lagrets koppling

Apache Hive Warehouse Connector (INSTANSEN) är ett bibliotek som gör det lättare att arbeta enklare med Apache Spark och Apache Hive genom att stödja uppgifter som att flytta data mellan Spark-DataFrames och Hive-tabeller, och även dirigera data strömnings data till Hive-tabeller. Hive-lagrets koppling fungerar som en brygga mellan Spark och Hive. Det stöder Scala, Java och python för utveckling.

Med Hive-lagrets koppling kan du dra nytta av de unika funktionerna i Hive och Spark för att skapa kraftfulla Big data-program. Apache Hive erbjuder stöd för databas transaktioner som är atomiska, konsekventa, isolerade och varaktiga (sur). Mer information om syra och transaktioner i Hive finns i [Hive-transaktioner](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive erbjuder också detaljerade säkerhets kontroller via Apache Ranger och analytisk bearbetning med låg latens som inte är tillgängliga i Apache Spark.

Apache Spark, har ett strukturerat strömnings-API som ger direkt uppspelnings funktioner som inte är tillgängliga i Apache Hive. Från och med HDInsight 4,0 har Apache Spark 2.3.1 och Apache Hive 3.1.0 separata metastores, vilket kan göra det svårt att samverka. Hive-lager kopplingen gör det lättare att använda Spark och Hive tillsammans. INSTANSEN-biblioteket läser in data från LLAP-daemonar till Spark-körningar parallellt, vilket gör det mer effektivt och skalbart än att använda en standard JDBC-anslutning från Spark till Hive.

![arkitektur för Hive-lager koppling](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Några av de åtgärder som stöds av Hive-lager kopplingen är:

* Beskriva en tabell
* Skapa en tabell för ORC-formaterade data
* Välja Hive-data och hämta en DataFrame
* Skriver en DataFrame till Hive i batch
* Kör en uppdaterings instruktion för Hive
* Läsa tabell data från Hive, transformera dem i Spark och skriva till en ny Hive-tabell
* Skriva en DataFrame-eller Spark-dataström till Hive med HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Installation av Hive lager koppling

Följ dessa steg om du vill konfigurera Hive-lagrets koppling mellan ett Spark-och Interactive Query-kluster i Azure HDInsight:

### <a name="create-clusters"></a>Skapa kluster

1. Skapa ett HDInsight Spark **4,0** -kluster med ett lagrings konto och ett anpassat virtuellt Azure-nätverk. Information om hur du skapar ett kluster i ett virtuellt Azure-nätverk finns i [lägga till HDInsight i ett befintligt virtuellt nätverk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Skapa ett LLAP-kluster (Interactive HDInsight Query) **4,0** med samma lagrings konto och virtuella Azure-nätverk som Spark-klustret.

### <a name="modify-hosts-file"></a>Ändra hosts-filen

Kopiera Node-informationen från `/etc/hosts`-filen på headnode0 av ditt interaktiva fråge kluster och sammanfoga informationen till `/etc/hosts`-filen på headnode0 i Spark-klustret. Det här steget gör att ditt Spark-kluster kan matcha IP-adresser för noderna i det interaktiva fråga klustret. Visa innehållet i den uppdaterade filen med `cat /etc/hosts`. Slutgiltiga utdata bör se ut ungefär som på skärm bilden nedan.

![Hive lager koppling Hosts-fil](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Samla in preliminär information

#### <a name="from-your-interactive-query-cluster"></a>Från ditt interaktiva Query-kluster

1. Gå till klustrets start sida för Apache-Ambari med `https://LLAPCLUSTERNAME.azurehdinsight.net` där `LLAPCLUSTERNAME` är namnet på ditt interaktiva fråga-kluster.

1. Navigera till **Hive** - > **konfiguration** > **Avancerad** > **Avancerad Hive-site** > **Hive. Zookeeper. kvorum** och anteckna värdet. Värdet kan vara ungefär så här: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Navigera till **Hive** - > **config** > **Advanced** > **General** > **Hive. metaarkiv. URI: er** och anteckna värdet. Värdet kan vara ungefär så här: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>Från Apache Spark-klustret

1. Gå till klustrets start sida för Apache-Ambari med `https://SPARKCLUSTERNAME.azurehdinsight.net` där `SPARKCLUSTERNAME` är namnet på ditt Apache Spark-kluster.

1. Navigera till **Hive** - > **konfigurations** > **Avancerad** > **Avancerad Hive – interaktiv plats** > **Hive. LLAP. daemon. service. hosts** och anteckna värdet. Värdet kan vara ungefär så här: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Konfigurera inställningar för Spark-kluster

Från ditt Spark Ambari-webbgränssnitt navigerar du till **Spark2** > **configs** > **anpassade Spark2-standardvärden**.

![Apache Ambari Spark2-konfiguration](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Välj **Lägg till egenskap...** vid behov för att lägga till/uppdatera följande:

| Nyckel | Värde |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Värdet du hämtade tidigare från **Hive. LLAP. daemon. service. hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Anges till anslutnings strängen JDBC, som ansluter till Hiveserver2 i det interaktiva fråga klustret. Ersätt `LLAPCLUSTERNAME` med namnet på ditt interaktiva fråga-kluster. Ersätt `PWD` med det faktiska lösen ordet.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Ange en lämplig HDFS-kompatibel uppsamlings katalog. Om du har två olika kluster bör mellanlagringsplatsen vara en mapp i uppsamlings katalogen för LLAP-klustrets lagrings konto så att HiveServer2 har åtkomst till den.  Ersätt `STORAGE_ACCOUNT_NAME` med namnet på det lagrings konto som används av klustret och `STORAGE_CONTAINER_NAME` med namnet på lagrings behållaren.|
|`spark.datasource.hive.warehouse.metastoreUri`|Det värde som du hämtade tidigare från **Hive. metaarkiv. URI: er**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` för garn för klient distributions läge.|
|`spark.hadoop.hive.zookeeper.quorum`|Värdet som du hämtade tidigare från **Hive. Zookeeper. kvorum**.|

Spara ändringar och starta om komponenter efter behov.

## <a name="using-the-hive-warehouse-connector"></a>Använda Hive-lagrets koppling

### <a name="connecting-and-running-queries"></a>Ansluta och köra frågor

Du kan välja mellan några olika metoder för att ansluta till ditt interaktiva Query-kluster och köra frågor med hjälp av Hive Warehouse-anslutningsprogrammet. De metoder som stöds är följande verktyg:

* [Spark-Shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alla exempel som anges i den här artikeln körs via Spark-Shell.

Gör så här för att starta en spark-Shell-session:

1. SSH till huvudnoden för ditt Apache Spark-kluster. Mer information om hur du ansluter till klustret med SSH finns i [ansluta till HDInsight (Apache Hadoop) med SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ange följande kommando för att starta Spark-gränssnittet:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Ett välkomst meddelande visas och en `scala>` fråga där du kan ange kommandon.

1. När du har startat Spark-Shell kan du starta en Hive-instans med hjälp av följande kommandon:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Ansluta och köra frågor på Enterprise Security Package-kluster (ESP)

Enterprise Security Package (ESP) tillhandahåller funktioner i företags klass som Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll för Apache Hadoop kluster i Azure HDInsight. Mer information om ESP finns i [använda Enterprise Security Package i HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH till huvudnoden för ditt Apache Spark-kluster. Mer information om hur du ansluter till klustret med SSH finns i [ansluta till HDInsight (Apache Hadoop) med SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Skriv `kinit` och logga in med en domän användare.

1. Starta Spark-Shell med den fullständiga listan över konfigurations parametrar som visas nedan. Alla värden i versaler mellan vinkelparenteser måste anges baserat på klustret. Om du behöver ta reda på värdena för indata för någon av parametrarna nedan kan du läsa avsnittet om [konfiguration av Hive-anslutningsprogrammet](#hive-warehouse-connector-setup).:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Skapa Spark-DataFrames från Hive-frågor

Resultaten av alla frågor som använder INSTANSEN-biblioteket returneras som en DataFrame. Följande exempel visar hur du skapar en grundläggande fråga.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Resultatet av frågan är Spark-DataFrames, som kan användas med Spark-bibliotek som MLIB och SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Skriva ut Spark-DataFrames till Hive-tabeller

Spark stöder inte internt skrivning till Hive-tabeller med hanterade syror. Med INSTANSEN kan du dock skriva ut valfri DataFrame till en Hive-tabell. Du kan se den här funktionen på jobbet i följande exempel:

1. Skapa en tabell med namnet `sampletable_colorado` och ange dess kolumner med följande kommando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrera tabellen `hivesampletable` där kolumnen `state` är lika med `Colorado`. Den här frågan av Hive-tabellen returneras som en spark-DataFrame. Sedan sparas DataFrame i Hive-tabellen `sampletable_colorado` med hjälp av funktionen `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Visa resultaten med följande kommando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![Hive-dist. lager koppling visar Hive-tabell](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturerade strömmande skrivningar

Med hjälp av Hive Warehouse Connector kan du använda Spark-direktuppspelning för att skriva data till Hive-tabeller.

Följ stegen nedan för att skapa ett Hive-exempel för Hive-anslutning som matar in data från en spark-dataström på localhost-port 9999 i en Hive-tabell.

1. Följ stegen för att [ansluta och köra frågor](#connecting-and-running-queries).

1. Starta Spark-dataströmmen med följande kommando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generera data för Spark-dataströmmen som du skapade genom att utföra följande steg:
    1. Öppna en andra SSH-session i samma Spark-kluster.
    1. Skriv `nc -lk 9999`i kommando tolken. Det här kommandot använder verktyget netcat för att skicka data från kommando raden till den angivna porten.

1. Gå tillbaka till den första SSH-sessionen och skapa en ny Hive-tabell för att lagra strömmande data. I Spark-Shell anger du följande kommando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Skriv sedan strömmande data till den nyligen skapade tabellen med hjälp av följande kommando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Alternativen `metastoreUri` och `database` måste anges manuellt på grund av ett känt problem i Apache Spark. Mer information om det här problemet finns i [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Gå tillbaka till den andra SSH-sessionen och ange följande värden:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Gå tillbaka till den första SSH-sessionen och anteckna den korta aktiviteten. Använd följande kommando för att visa data:

    ```scala
    hive.table("stream_table").show()
    ```

Använd **CTRL + C** för att stoppa netcat på den andra SSH-sessionen. Använd `:q` för att avsluta Spark-Shell på den första SSH-sessionen.

### <a name="securing-data-on-spark-esp-clusters"></a>Skydda data i Spark ESP-kluster

1. Skapa en tabell `demo` med några exempel data genom att ange följande kommandon:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visa tabellens innehåll med följande kommando. Innan du tillämpar principen visas den fullständiga kolumnen i `demo`s tabellen.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demo tabell innan du använder en Ranger-princip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Använd en kolumn Maskerings princip som bara visar de sista fyra tecknen i kolumnen.  
    1. Gå till gränssnittet Ranger admin på `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klicka på Hive-tjänsten för klustret under **Hive**.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klicka på fliken **maskering** och **Lägg sedan till ny princip**

        ![Hive-princip lista för Hive-lager kopplings Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Ange ett önskat princip namn. Välj databas: **standard**, Hive-tabell: **demo**, Hive-kolumn: **namn**, användare: **Rsadmin2**, åtkomst typer: **Select**och **partiell mask: Visa sista 4** från menyn **Välj masknings alternativ** . Klicka på **Lägg till**.
                ![skapa princip](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visa tabellens innehåll igen. När du har tillämpat Ranger-principen ser vi bara de sista fyra tecknen i kolumnen.

    ![demo tabell efter användning av Ranger-policy](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Nästa steg

* [Använda Interactive Query i HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Exempel på interaktion med Hive-anslutningsprogrammet med Zeppelin, livy, Spark-Submit och pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
