---
title: Massinläsning läsa in i Apache Phoenix med psql - Azure HDInsight | Microsoft Docs
description: Verktyget psql används för att läsa in massinläsning av data till Phoenix tabeller.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 2c192707c6cf8f84d2ca1c0307770cadd5cdb8bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Massinläsning av data till Phoenix med psql

[Apache Phoenix](http://phoenix.apache.org/) är en öppen källkod, massivt parallell relationsdatabas som bygger på [HBase](../hbase/apache-hbase-overview.md). Phoenix innehåller SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutiner för att användarna ska kunna skapa, ta bort och ändra SQL-tabeller, index, vyer och sekvenser och upsert rader och separat gruppvis. Phoenix använder noSQL interna kompilering i stället för att använda MapReduce för att kompilera frågor, för att skapa låg latens program ovanpå HBase. Phoenix lägger till medarbetare processorer för att kunna köra klienten har angett koden i adressutrymmet för servern, köra koden samordnad med data. Detta minimerar klient/server dataöverföring.  Om du vill arbeta med data med Phoenix i HDInsight, först skapa tabeller och läsa in data i dem.

## <a name="bulk-loading-with-phoenix"></a>Massredigera inläsning med Phoenix

Det finns flera sätt att hämta data till HBase inklusive använder klienten API: er, ett MapReduce-jobb med TableOutputFormat, eller mata in data manuellt med hjälp av HBase-gränssnittet. Phoenix erbjuder två metoder för att läsa in CSV-data i Phoenix tabeller: en klient som läser in verktyget med namnet `psql`, och ett MapReduce-baserade bulk belastningen verktyg.

Den `psql` verktyget är enkeltrådad och lämpar sig bäst för att läsa in megabyte eller gigabyte av data. Alla CSV-filer som ska läsas in måste ha filnamnstillägget '.csv-'.  Du kan också ange SQL-skriptfiler i de `psql` kommandoraden med filnamnstillägget 'SQL'.

Massinläsning inläsning med MapReduce används för mycket större datavolymer, vanligtvis i produktion scenarier som MapReduce använder flera trådar.

Innan du börjar läser in data, kontrollera att Phoenix är aktiverat och att frågan timeout-inställningar är som förväntat.  Åtkomst till HDInsight-kluster Ambari instrumentpanelen, Välj HBase och sedan fliken konfiguration.  Rulla nedåt för att kontrollera att Apache Phoenix är `enabled` som visas:

![Inställningar för Apache Phoenix HDInsight-kluster](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Använd `psql` bulk belastningen tabeller

1. Skapa en ny tabell och sedan spara frågan med filnamnet `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopiera CSV-fil (exempel innehållet visas) som `customers.csv` i en `/tmp/` katalogen för inläsning till nyligen skapade tabellen.  Använd den `hdfs` kommando för att kopiera CSV-filen till din önskade källplatsen.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Skapa en SQL SELECT-fråga för att verifiera indata lästs in korrekt och sedan spara frågan med filnamnet `listCustomers.sql`. Du kan använda SQL-frågan.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Massinläsning läsa in data genom att öppna en *nya* Hadoop-kommandofönstret. Först ändra till katalogplatsen för körning med den `cd` kommando och sedan använda den `psql` verktyget (Python `psql.py` kommandot). 

    I följande exempel förväntar sig att du kopierat den `customers.csv` fil från ett lagringskonto till din lokala temp-katalog med hjälp av `hdfs` som i steg 2 ovan.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Att fastställa den `ZookeeperQuorum` namn, leta upp zookeeper kvorum strängen i filen `/etc/hbase/conf/hbase-site.xml` med egenskapsnamn `hbase.zookeeper.quorum`.

5. Efter den `psql` åtgärden har slutförts, bör du se ett meddelande i kommandofönstret:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Använda MapReduce bulk belastningen tabeller

För högre genomströmning inläsning fördelas på klustret, verktyget MapReduce belastningen. Den här inläsaren först konverterar alla data till HFiles och ger sedan den skapade HFiles till HBase.

1. Starta CSV-MapReduce-inläsaren med hjälp av den `hadoop` med Phoenix klienten jar:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Skapa en ny tabell med SQL-uttryck med `CreateCustomersTable.sql` i föregående steg 1.

3. För att verifiera schemat för din tabell kör `!describe inputTable`.

4. Fastställa sökvägen till dina inkommande data, till exempel exemplet `customers.csv` fil. Inkommande filer kan finnas i ditt WASB/ADLS-lagringskonto. I det här exemplet indatafilerna finns i den `<storage account parent>/inputFolderBulkLoad` directory.

5. Ändra till katalogen körningen för kommandot MapReduce bulk belastningen:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Leta upp din `ZookeeperQuorum` värde i `/etc/hbase/conf/hbase-site.xml`, med egenskapsnamn `hbase.zookeeper.quorum`.

7. Ställ in klassökvägen och kör den `CsvBulkLoadTool` verktyget kommando:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Om du vill använda MapReduce med ADLS hitta ADLS-rotkatalog, vilket är den `hbase.rootdir` värde i `hbase-site.xml`. I följande kommando, ADLS-rotkatalogen är `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. I det här kommandot anger ADLS-indata och utdata mappar som parametrar:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Rekommendationer

* Använd samma lagringsmedium för både inkommande och utgående mappar WASB eller ADLS. Du kan använda för att överföra data från WASB till ADLS, den `distcp` kommando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Använd arbetarnoder större storlek. Karta processer för masskopiering MapReduce generera stora mängder tillfälliga utdata som fyller tillgängliga icke-DFS-utrymmet. Använd mer och större storlek arbetarnoder för en stor del av massinläsning. Antalet arbetarnoder du allokera till klustret direkt påverkar bearbetningshastigheten.

* Dela indatafiler i ~ 10 GB segment. Massinläsning är processorintensiva lagring, så delar upp dina indatafiler i flera segment resulterar i bättre prestanda.

* Undvik region server-anslutningar. Om din radnyckel monotont ökar kan HBase sekventiella skrivåtgärder medföra region server hotspotting. *Saltning* Radnyckeln minskar sekventiella skrivåtgärder. Phoenix är ett sätt att transparent salt radnyckel med dessa byte för en viss tabell som det hänvisas till nedan.

## <a name="next-steps"></a>Nästa steg

* [Stora mängder Data som lästes in med Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Saltat tabeller](https://phoenix.apache.org/salted.html)
* [Phoenix grammatik](http://phoenix.apache.org/language/index.html)
