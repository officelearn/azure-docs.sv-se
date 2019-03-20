---
title: Använda Apache Beeline med Apache Hive - Azure HDInsight
description: Lär dig hur du använder Beeline klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 via JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: beeline hive, hive beeline
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: hrasheed
ms.openlocfilehash: 4b7e2a8d5eb981fc30dd14fed8f7efcfc094af0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110893"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Använda Apache Beeline klienten med Apache Hive

Lär dig hur du använder [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) att köra Apache Hive-frågor på HDInsight.

Beeline är en Hive-klient som ska tas med på huvudnoderna i ditt HDInsight-kluster. Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns i ditt HDInsight-kluster. Du kan också använda Beeline för att få fjärråtkomst till Hive på HDInsight via internet. I följande exempel får de vanligaste anslutningssträngar som används för att ansluta till HDInsight från Beeline:

* __Använda Beeline från en SSH-anslutning till en huvudnod eller edge-nod__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Använda Beeline på en klient som ansluter till HDInsight via Azure Virtual Network__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Använda Beeline på en klient som ansluter till ett kluster i HDInsight Enterprise Security Package (ESP) över Azure Virtual Network__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>`
* __Använda Beeline på en klient som ansluter till HDInsight via det offentliga internet__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]  
> Ersätt `admin` med kontot för klusterinloggning för klustret.
>
> Ersätt `password` med lösenordet för kontot för klusterinloggning.
>
> Ersätt `clustername` med namnet på HDInsight-klustret.
>
> När du ansluter till klustret via ett virtuellt nätverk, Ersätt `<headnode-FQDN>` med fullständigt kvalificerade domännamnet för en klusterhuvudnod.
>
> När du ansluter till ett kluster med Enterprise Security Package (ESP), Ersätt `<AAD-Domain>` med namnet på den Azure Active Directory (AAD) som klustret tillhör. Ersätt `<username>` med namnet på ett konto på domänen med behörigheter för åtkomst till klustret.

## <a id="prereq"></a>Förhandskrav

* En Linux-baserat Hadoop i HDInsight-kluster av version 3.4 och senare.

  > [!IMPORTANT]  
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En SSH-klient eller en lokal Beeline-klient. De flesta av stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret. Information om hur du kör Beeline från utanför klustret finns i den [använda Beeline via fjärranslutning](#remote) avsnittet.

    Mer information om hur du använder SSH finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Kör en Hive-fråga

1. När du startar Beeline, måste du ange en anslutningssträng för HiveServer2 i ditt HDInsight-kluster:

    * När du ansluter via det offentliga internet, måste du ange kontonamn för inloggning för kluster (standard `admin`) och lösenord. Till exempel använda Beeline från en klientsystem för att ansluta till den `<clustername>.azurehdinsight.net` adress. Den här anslutningen görs via port `443`, och krypteras med SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * När du ansluter från en SSH-session till en klusterhuvudnod, du kan ansluta till den `headnodehost` adress på port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * När du ansluter via Azure Virtual Network, måste du ange det fullständigt kvalificerade domännamnet (FQDN) av en huvudnod för klustret. Eftersom den här anslutningen görs direkt till noderna i klustret, anslutningen använder port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```
    * När du ansluter till ett kluster med Enterprise Security Package (ESP) ansluten till Azure Active Directory (AAD), måste du även ange domännamnet `<AAD-Domain>` och namnet på ett domänanvändarkonto med behörigheter för åtkomst till klustret `<username>`:
        
        ```bash
        kinit <username>
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
        ```

2. Beeline kommandon som börjar med en `!` tecken, till exempel `!help` visar hjälpen. Dock `!` kan utelämnas för vissa kommandon. Till exempel `help` fungerar även.

    Det finns en `!sql`, som används för att köra HiveQL-instruktioner. Dock HiveQL så används ofta för att du kan utelämna det föregående `!sql`. Följande två uttryck är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    På ett nytt kluster visas endast en tabell: **hivesampletable**.

3. Använd följande kommando för att visa schemat för hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Det här kommandot returnerar följande information:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Den här informationen beskriver kolumnerna i tabellen.

4. Ange följande instruktioner för att skapa en tabell med namnet **log4jLogs** med hjälp av exempeldata som medföljer HDInsight-klustret:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Dessa instruktioner utför följande åtgärder:

    * `DROP TABLE` -Om tabellen finns, tas den bort.

    * `CREATE EXTERNAL TABLE` -Skapar en **externa** tabellen i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

    * `ROW FORMAT` – Hur informationen har formaterats. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

    * `STORED AS TEXTFILE LOCATION` -Där data lagras och i vilka filformat.

    * `SELECT` -Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Den här frågan returnerar ett värde av **3** som det finns tre rader som innehåller det här värdet.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive försöker använda schemat för alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräpinsamling data i resultatet, meddelar den här instruktionen Hive att den endast ska returnera data från filer som slutar på. log.

   > [!NOTE]  
   > Externa tabeller som ska användas när du förväntar dig att underliggande data uppdateras av en extern källa. Till exempel en automatiserade uppladdningen eller en MapReduce-åtgärd.
   >
   > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

    Kommandots utdata liknar följande text:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Om du vill avsluta Beeline använder `!exit`.

### <a id="file"></a>Använd Beeline för att köra en HiveQL-fil

Använd följande steg för att skapa en fil och sedan köra den genom Beeline.

1. Använd följande kommando för att skapa en fil med namnet **query.hql**:

    ```bash
    nano query.hql
    ```

2. Använd följande text som innehållet i filen. Den här frågan skapar en ny ”intern” tabell med namnet **felvillkoren**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa instruktioner utför följande åtgärder:

   * **SKAPA tabellen IF NOT finns** -om tabellen inte redan finns skapas den. Eftersom den **externa** nyckelord används inte, den här instruktionen skapar en intern tabell. Interna tabeller lagras i datalagret Hive och hanteras helt av Hive.
   * **LAGRADE AS ORC** -lagrar data i optimerade rad kolumner (ORC)-format. ORC-format är ett mycket optimerade och effektiv format för att lagra Hive-data.
   * **INFOGA SKRIVA ÖVER... Välj** -väljer rader från den **log4jLogs** tabellen som innehåller **[fel]**, infogar data till den **felvillkoren** tabell.

     > [!NOTE]  
     > Till skillnad från externa tabeller, släppa en intern tabell tar bort de underliggande data.

3. Om du vill spara filen, Använd **Ctrl**+**_X**, ange sedan **Y**, och slutligen **RETUR**.

4. Använd följande för att köra filen med Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Den `-i` parametern startar Beeline och kör instruktionerna i den `query.hql` filen. När frågan har slutförts kan du komma fram till den `jdbc:hive2://headnodehost:10001/>` prompten. Du kan också köra en fil med hjälp av den `-f` parametern, som avslutas Beeline när frågan har slutförts.

5. Kontrollera att den **felvillkoren** tabellen skapades, använder du följande instruktion för att returnera alla rader från **felvillkoren**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tre raderna med data ska returneras, som innehåller alla **[fel]** i kolumnen t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Använd Beeline via en fjärranslutning

Om du har installerat lokalt Beeline och ansluter via det offentliga internet, använder du följande parametrar:

* __Anslutningssträngen__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Klustrets inloggningsnamn__: `-n admin`

* __Lösenord för klusterinloggning__ `-p 'password'`

Ersätt den `clustername` i anslutningssträngen med namnet på ditt HDInsight-kluster.

Ersätt `admin` med namnet på din klusterinloggning och Ersätt `password` med lösenordet för din klusterinloggning.

Om du har installerat lokalt Beeline och ansluta via Azure Virtual Network, använder du följande parametrar:

* __Anslutningssträngen__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Använd informationen i för att hitta det fullständigt kvalificerade domännamnet för en huvudnod, den [hantera HDInsight med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentet.

## <a id="sparksql"></a>Använda Beeline med Apache Spark

Apache Spark tillhandahåller en egen implementering av HiveServer2, som ibland kallas Spark Thrift-server. Den här tjänsten använder Spark SQL för att lösa frågor i stället för Hive och kan ge bättre prestanda beroende på din fråga.

Den __anslutningssträngen__ används när du ansluter via internet är något annorlunda. I stället för `httpPath=/hive2` är det `httpPath/sparkhive2`. Följande är ett exempel för att ansluta via internet:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

När du ansluter direkt från klustrets huvudnod eller från en resurs i Azure samma virtuella nätverk som HDInsight-kluster, port `10002` bör användas för Spark Thrift-server i stället för `10001`. Följande är ett exempel för att ansluta till direkt till huvudnoden:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Nästa steg

Mer allmän information om Hive i HDInsight finns i följande dokument:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Mer information om andra sätt du kan arbeta med Hadoop i HDInsight finns i följande dokument:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive, se följande dokument: [Använd Apache Ambari Tez-vyn på Linux-baserade HDInsight](../hdinsight-debug-ambari-tez-view.md).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
