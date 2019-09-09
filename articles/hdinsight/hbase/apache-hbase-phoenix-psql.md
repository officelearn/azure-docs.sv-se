---
title: Mass inläsning till Apache Phoenix med psql – Azure HDInsight
description: Använd psql-verktyget för att läsa in Mass inläsnings data i Apache Phoenix tabeller i Azure HDInsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 15bb65e004de916862297f91278328cddb16487d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810411"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Massinläsning av data till Apache Phoenix med psql

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källa, massivt parallell Relations databas som bygger på [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix innehåller SQL-liknande frågor via HBase. Phoenix använder JDBC-drivrutiner för att göra det möjligt för användare att skapa, ta bort och ändra SQL-tabeller, index, vyer och sekvenser och upsert-rader individuellt och i grupp. Phoenix använder noSQL intern kompilering i stället för att använda MapReduce för att kompilera frågor, för att skapa program med låg latens ovanpå HBase. Phoenix lägger till co-processorer för att stödja körning av kundlevererad kod i serverns adress utrymme, och kör koden som samplacerade med data. Detta minimerar överföringen av klient/server-data.  Om du vill arbeta med data med hjälp av Phoenix i HDInsight måste du först skapa tabeller och sedan läsa in data i dem.

## <a name="bulk-loading-with-apache-phoenix"></a>Mass inläsning med Apache Phoenix

Det finns flera sätt att hämta data till HBase, inklusive att använda klient-API: er, ett MapReduce-jobb med TableOutputFormat eller mata in data manuellt med HBase-gränssnittet. Phoenix innehåller två metoder för att läsa in CSV-data till Phoenix-tabeller: ett `psql`klient inläsnings verktyg med namnet och ett MapReduce-baserat Mass inläsnings verktyg.

`psql` Verktyget är en enkel tråd och lämpar sig bäst för att läsa in megabyte eller flera gigabyte med data. Alla CSV-filer som ska läsas in måste ha fil namns tillägget. csv.  Du kan också ange SQL-skriptfiler i `psql` kommando raden med fil namns tillägget. SQL.

Mass inläsning med MapReduce används för mycket större data volymer, vanligt vis i produktions scenarier, eftersom MapReduce använder flera trådar.

Innan du börjar läsa in data kontrollerar du att Phoenix är aktiverat och att inställningarna för tids gräns för frågor är som förväntat.  Gå till ditt HDInsight-kluster [Apache Ambari](https://ambari.apache.org/) -instrumentpanelen, Välj HBase och sedan fliken konfiguration.  Rulla nedåt för att kontrol lera att Apache Phoenix är `enabled` inställt på som visas:

![Apache Phoenix inställningar för HDInsight-kluster](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Använd `psql` till Mass inläsnings tabeller

1. Skapa en ny tabell och spara sedan frågan med fil namnet `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopiera CSV-filen (exempel innehåll som visas) `customers.csv` till en `/tmp/` katalog för inläsning till den tabell som du skapade nyligen.  `hdfs` Använd kommandot för att kopiera CSV-filen till önskad käll plats.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Skapa en SQL SELECT-fråga för att verifiera indata som lästs in korrekt och spara sedan `listCustomers.sql`frågan med fil namn. Du kan använda valfri SQL-fråga.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Mass inläsning av data genom att öppna ett *nytt* Hadoop-kommando fönster. Ändra först till körnings katalogens plats med `cd` kommandot och `psql` Använd sedan verktyget (python `psql.py` -kommandot). 

    I följande exempel förväntas att du kopierade `customers.csv` filen från ett lagrings konto till din lokala Temp-katalog med hjälp av `hdfs` som i steg 2 ovan.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Ta reda på `ZookeeperQuorum` namnet genom att leta upp [Apache ZooKeeper](https://zookeeper.apache.org/) -kvorumdisken i filen `/etc/hbase/conf/hbase-site.xml` med ett egenskaps namn `hbase.zookeeper.quorum`.

5. `psql` När åtgärden har slutförts bör du se ett meddelande i kommando fönstret:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Använda MapReduce till Mass inläsnings tabeller

Använd inläsnings verktyget MapReduce för större data flöden som är distribuerade över klustret. Den här inläsaren konverterar först alla data till HFiles och ger sedan den skapade HFiles till HBase.

1. Starta MapReduce-inläsaren genom att `hadoop` använda kommandot med Phoenix-klientens jar:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Skapa en ny tabell med ett SQL-uttryck, precis `CreateCustomersTable.sql` som i föregående steg 1.

3. Kör `!describe inputTable`för att kontrol lera schemat för din tabell.

4. Bestäm sökvägen till indata, t. ex. exempel `customers.csv` filen. Indatafilerna kan finnas i ditt WASB/ADLS-lagrings konto. I det här exempel scenariot finns indatafilerna i `<storage account parent>/inputFolderBulkLoad` katalogen.

5. Ändra till körnings katalogen för MapReduce Mass inläsnings kommando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Hitta ditt `ZookeeperQuorum` värde i `/etc/hbase/conf/hbase-site.xml`, med egenskaps `hbase.zookeeper.quorum`namn.

7. Konfigurera classpath och kör `CsvBulkLoadTool` verktygs kommandot:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Om du vill använda MapReduce med Azure Data Lake Storage letar du upp data Lake Storage rot katalogen, vilket `hbase.rootdir` är värdet `hbase-site.xml`i. I följande kommando är `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`data Lake Storage rot katalogen. I det här kommandot anger du Data Lake Storage in-och utdata-mappar som parametrar:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Rekommendationer

* Använd samma lagrings medium för både in-och utdata-mappar, antingen Azure Storage (WASB) eller Azure Data Lake Storage (ADL). Om du vill överföra data från Azure Storage till data Lake Storage kan du använda `distcp` kommandot:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Använd större arbetsnoder i arbets storlek. Kart processerna för MapReduce Mass kopiering genererar stora mängder temporära utdata som fyller upp det tillgängliga icke-DFS-utrymmet. För en stor mängd Mass inläsning använder du fler och större arbetsnoder för arbetare. Antalet arbetsnoder som du allokerar till klustret påverkar direkt bearbetnings hastigheten.

* Dela in indatafiler i ~ 10 GB-segment. Mass inläsning är en lagrings intensiv åtgärd så att du kan dela in dina indatafiler i flera segment, vilket ger bättre prestanda.

* Undvik hotspots på regions servern. Om rad nyckeln är monotont ökning kan HBase sekventiella skrivningar orsaka region Server Hotspotting. När rad nyckeln *saltas* minskas sekventiella skrivningar. Phoenix är ett sätt att transparent salt rad nyckel med en saltad byte för en viss tabell enligt nedan.

## <a name="next-steps"></a>Nästa steg

* [Mass inläsning av data med Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Använda Apache Phoenix med Linux-baserade Apache HBase-kluster i HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Saltade tabeller](https://phoenix.apache.org/salted.html)
* [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html)
