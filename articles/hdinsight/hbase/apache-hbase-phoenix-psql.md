---
title: Massinläsning i Apache Phoenix med psql - Azure HDInsight
description: Använd psql-verktyget för att läsa in massinläsningsdata i Apache Phoenix-tabeller i Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552618"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Massinläsning av data till Apache Phoenix med psql

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källkod, massivt parallella relationella databas byggd på [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix tillhandahåller SQL-liknande frågor via HBase. Phoenix använder JDBC-drivrutiner för att göra det möjligt för användare att skapa, ta bort och ändra SQL-tabeller, index, vyer och sekvenser och uppåtrader individuellt och i grupp. Phoenix använder noSQL native kompilering i stället för att använda MapReduce för att kompilera frågor, för att skapa låg latensprogram ovanpå HBase. Phoenix lägger till coprocessorer för att stödja körning av klientkod i serverns adressutrymme och kör koden som är samlokaliserad med data. Detta minimerar överföring av klient-serverdata.  Om du vill arbeta med data med Phoenix i HDInsight skapar du först tabeller och läser sedan in data i dem.

## <a name="bulk-loading-with-apache-phoenix"></a>Bulk lastning med Apache Phoenix

Det finns flera sätt att hämta data till HBase, inklusive användning av klient-API:er, ett MapReduce-jobb med TableOutputFormat eller mata in data manuellt med hbase-skalet. Phoenix innehåller två metoder för att läsa in CSV-data i Phoenix-tabeller: ett klientinläsningsverktyg med namnet `psql`mapreduce-baserade massbelastningsverktyg.

Verktyget `psql` är enkelgängat och passar bäst för att läsa in megabyte eller gigabyte data. Alla CSV-filer som ska läsas in måste ha filändelsen ".csv".  Du kan också ange SQL-skriptfiler på `psql` kommandoraden med filtillägget ".sql".

Massinläsning med MapReduce används för mycket större datavolymer, vanligtvis i produktionsscenarier, eftersom MapReduce använder flera trådar.

Innan du börjar läsa in data kontrollerar du att Phoenix är aktiverat och att frågetidsgränsen är som förväntat.  Öppna din HDInsight-kluster [Apache Ambari-instrumentpanel,](https://ambari.apache.org/) välj HBase och sedan fliken Konfiguration.  Bläddra nedåt för att kontrollera `enabled` att Apache Phoenix är inställt på som visas:

![Inställningar för Apache Phoenix HDInsight-kluster](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Används `psql` för massinläsningstabeller

1. Skapa en `createCustomersTable.sql`fil som heter och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Skapa en `listCustomers.sql`fil som heter och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```sql
    SELECT * from Customers;
    ```

1. Skapa en `customers.csv`fil som heter och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Skapa en `customers2.csv`fil som heter och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Öppna en kommandotolk och ändra katalogen till platsen för de nyskapade filerna. Ersätt CLUSTERNAME nedan med det faktiska namnet på HBase-klustret. Kör sedan koden för att ladda upp filerna till huvudnoden för klustret:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från din ssh-session ändrar du katalogen till **psql-verktygets** plats. Kör kommandot nedan:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Massinläsning av data. Koden nedan skapar både tabellen **Kunder** och överför sedan data.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    När `psql` åtgärden har slutförts bör du se ett meddelande som liknar följande:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Du kan fortsätta `psql` att använda för att visa innehållet i tabellen Kunder. Kör koden nedan:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Du kan också använda [HBase-skalet](./query-hbase-with-hbase-shell.md)eller [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) för att fråga efter data.

1. Ladda upp ytterligare data. Nu när tabellen redan finns anger kommandot tabellen. Kör kommandot nedan:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Använda MapReduce till massbelastningstabeller

För högre dataflödesinläsning fördelad över klustret använder du mapreduce-inläsningsverktyget. Den här lastaren konverterar först alla data till HFiles och tillhandahåller sedan den skapade HFiles till HBase.

1. Det här avsnittet fortsätter med ssh-sessionen och objekt som skapats tidigare. Skapa tabellen **Kunder** och **filen customers.csv** efter behov med hjälp av stegen ovan. Om det behövs, återupprätta din ssh-anslutning.

1. Trunkera innehållet i tabellen **Kunder.** Från din öppna ssh-session kör du kommandona nedan:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Kopiera `customers.csv` filen från din headnode till Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Ändra till körningskatalogen för kommandot MapReduce-massbelastning:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Starta CSV MapReduce-lastaren `hadoop` med kommandot med Phoenix-klientburken:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    När uppladdningen är klar bör du se ett meddelande som liknar följande:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Om du vill använda MapReduce med Azure Data Lake Storage `hbase.rootdir` letar `hbase-site.xml`du reda på rotkatalogen Data Lake Storage, som är värdet i . I följande kommando är `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`rotkatalogen Data Lake Storage . I det här kommandot anger du in- och utdatamapparna för datasjölagring som parametrar:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Om du vill fråga och visa data kan du använda **psql** enligt beskrivningen tidigare. Du kan också använda [HBase-skalet](./query-hbase-with-hbase-shell.md)eller [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Rekommendationer

* Använd samma lagringsmedium för både indata- och utdatamappar, antingen Azure Storage (WASB) eller Azure Data Lake Storage (ADL). Om du vill överföra data från Azure Storage `distcp` till Data Lake Storage kan du använda kommandot:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Använd arbetsnoder av större storlek. Kartprocesserna för Masskopian MapReduce producerar stora mängder temporärt utdata som fyller upp det tillgängliga icke-DFS-utrymmet. För en stor mängd massinläsning använder du fler och större arbetsnoder. Antalet arbetsnoder som du allokerar till klustret påverkar direkt bearbetningshastigheten.

* Dela indatafiler i ~10 GB-bitar. Massinläsning är en lagringsintensiv åtgärd, så att dela upp indatafilerna i flera segment ger bättre prestanda.

* Undvik regionserverhotspots. Om radnyckeln ökar monotoniskt kan HBase-sekventiella skrivningar framkalla aktivering av regionserver.If your row key is monotonically increasing, HBase sekvential writes may inducering region server hotspotting. *Om du saltar* radtangenten minskas sekventiella skrivningar. Phoenix ger ett sätt att transparent salta radnyckeln med en saltningsbyte för en viss tabell, som refereras nedan.

## <a name="next-steps"></a>Nästa steg

* [Massdata inläsning med Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Använd Apache Phoenix med Linux-baserade Apache HBase-kluster i HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Saltade bord](https://phoenix.apache.org/salted.html)
* [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html)
