---
title: Samtidigt som läser in till Apache Phoenix med psql - Azure HDInsight
description: Använd psql-verktyget för att läsa in massinläsning av data till Phoenix tabeller.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: dab9465a5319e3e3df46771349373fc6996d0bd2
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600150"
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Massinläsning av data till Phoenix med psql

[Apache Phoenix](http://phoenix.apache.org/) är en öppen källkod, massivt parallella relationell databas som bygger på [HBase](../hbase/apache-hbase-overview.md). Phoenix ger SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutinerna så att användarna kan skapa, ta bort och ändra SQL-tabeller, index, vyer och sekvenser och upsert rader individuellt och gruppvis. Phoenix använder noSQL interna kompilering i stället för att använda MapReduce för att kompilera frågor, för att skapa med låg latens program ovanpå HBase. Phoenix lägger till coprocessorer för att stödja köra klienten anger kod i adressutrymmet för servern, köra koden samordnad med data. Detta minskar klient/server-dataöverföring.  Först skapar tabeller och sedan läsa in data i dem om du vill arbeta med data med hjälp av Phoenix i HDInsight.

## <a name="bulk-loading-with-phoenix"></a>Samtidigt som läser in med Phoenix

Det finns flera sätt att få data i HBase, inklusive med klientens API: er, ett MapReduce-jobb med TableOutputFormat, eller mata in data manuellt med hjälp av HBase-gränssnittet. Phoenix erbjuder två metoder för att läsa in CSV-data till Phoenix tabeller: en klient som läser in verktyg som heter `psql`, och ett MapReduce-baserade bulk load-verktyg.

Den `psql` verktyget är enkla trådar och lämpar sig bäst för att läsa in megabyte eller gigabyte med data. Alla CSV-filer som ska läsas in måste ha filnamnstillägget ”.csv”.  Du kan också ange SQL skriptfiler i den `psql` från kommandoraden med filtillägget '.sql'.

Samtidigt som läser in med MapReduce används för mycket större datavolymer, vanligtvis i produktionsscenarier bör eftersom MapReduce använder flera trådar.

Innan du börjar läsa in data, kontrollera att Phoenix är aktiverat och att inställningarna för timeout för fråga är som förväntat.  Åtkomst till HDInsight-kluster Ambari instrumentpanelen, väljer HBase och sedan på fliken konfiguration.  Rulla ned för att kontrollera att Apache Phoenix har angetts till `enabled` enligt:

![Apache Phoenix HDInsight klusterinställningar](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Använd `psql` till bulk belastningen tabeller

1. Skapa en ny tabell, och spara din fråga med filename `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopiera CSV-filen (exempel innehållet visas) som `customers.csv` i en `/tmp/` katalogen för att laddas i din nyligen skapade tabellen.  Använd den `hdfs` kommando för att kopiera CSV-filen till din önskade källplats.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Skapa en SQL SELECT-fråga för att verifiera indata lästs in korrekt, och spara din fråga med filename `listCustomers.sql`. Du kan använda en SQL-fråga.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Massinläsa data genom att öppna en *nya* Hadoop kommandofönstret. Först ändra till katalogplatsen körning med den `cd` kommandot och sedan använda den `psql` verktyget (Python `psql.py` kommandot). 

    I följande exempel förväntar sig att du kopierat den `customers.csv` fil från ett lagringskonto till din lokala temp-katalog med hjälp av `hdfs` som i steg 2 ovan.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Att fastställa den `ZookeeperQuorum` namn, leta upp zookeeper kvorum strängen i filen `/etc/hbase/conf/hbase-site.xml` med egenskapsnamn `hbase.zookeeper.quorum`.

5. Efter den `psql` åtgärden har slutförts, du bör se ett meddelande i kommandofönstret:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Använda MapReduce för att bulk belastningen tabeller

För högre dataflöde läser in fördelas på klustret, använder du verktyget MapReduce belastningen. Den här inläsaren först konverterar alla data till HFiles och ger den skapade HFiles till HBase.

1. Starta CSV-MapReduce-inläsaren med hjälp av den `hadoop` med Phoenix klienten jar:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Skapa en ny tabell med en SQL-instruktionen, precis som med `CreateCustomersTable.sql` i föregående steg 1.

3. Kontrollera schemat för tabellen med `!describe inputTable`.

4. Fastställa sökvägen till din inkommande data, till exempel `customers.csv` fil. Indatafilerna kan vara i ditt WASB/ADLS-lagringskonto. I det här exemplet, indatafilerna finns i den `<storage account parent>/inputFolderBulkLoad` directory.

5. Ändra till körningskatalogen för kommandot MapReduce bulk belastning:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Leta upp din `ZookeeperQuorum` värde i `/etc/hbase/conf/hbase-site.xml`, med egenskapsnamn `hbase.zookeeper.quorum`.

7. Ställa in klassökvägen och kör den `CsvBulkLoadTool` verktyget kommando:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Om du vill använda MapReduce med ADLS, hitta rotkatalogen ADLS, vilket är den `hbase.rootdir` värde i `hbase-site.xml`. I följande kommando, ADLS-rotkatalogen är `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. I det här kommandot anger ADLS-indata och utdata mappar som parametrar:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Rekommendationer

* Använd samma lagringsmedium för både inkommande och utgående mappar WASB eller ADLS. Om du vill överföra data från WASB till ADLS, du kan använda den `distcp` kommando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Använd större storlek arbetsnoder. Kartan processer för MapReduce-masskopiering generera stora mängder tillfälliga utdata som fyller det tillgängliga icke-DFS-utrymmet. Använd mer och större storlek arbetsnoder för en stor del av massinläsning. Antalet arbetsnoder som du allokerar till ditt kluster direkt påverkar bearbetningshastigheten.

* Dela upp indatafiler i segment med ~ 10 GB. Massinläsning är en storage-intensiva åtgärd, så dela upp dina indatafiler i flera segment resulterar i bättre prestanda.

* Undvik att region server-anslutningar. Om din radnyckel monotont ökar kan HBase sekventiella skrivåtgärder medföra region server hotspotting. *Saltning* Radnyckeln minskar sekventiella skrivåtgärder. Phoenix är ett sätt att transparent salt Radnyckeln med en dessa byte för en viss tabell, som det hänvisas till nedan.

## <a name="next-steps"></a>Nästa steg

* [Stora mängder Data läses in med Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Saltat tabeller](https://phoenix.apache.org/salted.html)
* [Phoenix grammatik](http://phoenix.apache.org/language/index.html)
