---
title: "Använda Beeline med Apache Hive - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Beeline klienten för att köra Hive-frågor med Hadoop i HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 över JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive, hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/02/2018
ms.author: larryfr
ms.openlocfilehash: 5d4e9d6ffb7fa0c2e4b69c5b534f0078aec5f68c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Använda Beeline klienten med Apache Hive

Lär dig hur du använder [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) att köra Hive-frågor i HDInsight.

Beeline är en Hive-klient som ingår i huvudnoderna för ditt HDInsight-kluster. Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns på ditt HDInsight-kluster. Du kan också använda Beeline för att fjärransluta Hive i HDInsight via internet. Följande exempel visar de vanligaste anslutningssträngar som används för att ansluta till HDInsight från Beeline:

* __Använder Beeline från en SSH-anslutning till en headnode eller edge nod__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Med hjälp av Beeline på en klient ansluter till HDInsight via ett Azure Virtual Network__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Med hjälp av Beeline på en klient ansluter till HDInsight via det offentliga internet__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Ersätt `admin` med klustret inloggningskontot för klustret.
>
> Ersätt `password` med lösenordet för inloggningskontot för klustret.
>
> Ersätt `clustername` med namnet på HDInsight-klustret.
>
> När du ansluter till klustret via ett virtuellt nätverk, ersätter `<headnode-FQDN>` med fullständigt kvalificerade domännamnet för en kluster-headnode.

## <a id="prereq"></a>Förhandskrav

* En Linux-baserade Hadoop på HDInsight-kluster av version 3.4 eller större.

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En SSH-klient eller en lokal Beeline-klient. De flesta av stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret. Information om hur du kör Beeline från utanför klustret finns i [använder Beeline via fjärranslutning](#remote) avsnitt.

    Mer information om hur du använder SSH finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Köra en Hive-fråga

1. När du startar Beeline måste du ange en anslutningssträng för HiveServer2 på ditt HDInsight-kluster:

    * När du ansluter via det offentliga internet, måste du ange klusternamnet inloggningen konto (standard `admin`) och lösenord. Till exempel använda Beeline från en klientdator för att ansluta till den `<clustername>.azurehdinsight.net` adress. Den här anslutningen görs via port `443`, och krypteras med SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * När du ansluter från en SSH-session till en kluster-headnode, du kan ansluta till den `headnodehost` adress på port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * När du ansluter via ett virtuellt Azure-nätverk måste du ange fullständigt kvalificerade domännamnet (FQDN) för en klustrets huvudnod. Eftersom den här anslutningen görs direkt till klusternoderna anslutningen använder port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Beeline kommandon som börjar med en `!` tecken, till exempel `!help` visar hjälpen. Men den `!` kan utelämnas för vissa kommandon. Till exempel `help` fungerar även.

    Det finns en `!sql`, som används för att köra HiveQL-instruktioner. HiveQL så ofta används dock att du kan hoppa över den föregående `!sql`. Följande två satser är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Endast en tabell anges på ett nytt kluster: **hivesampletable**.

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

    Den här informationen beskriver kolumnerna i tabellen. Medan vi kunde utföra några frågor mot dessa data vi istället skapa en helt ny tabell för att demonstrera hur du läser in data i Hive och använda ett schema.

4. Ange följande instruktioner om du vill skapa en tabell med namnet **log4jLogs** med exempeldata som medföljer HDInsight-kluster:

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

    * `DROP TABLE` – Om tabellen finns tas bort.

    * `CREATE EXTERNAL TABLE` -Skapar en **externa** tabellen i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

    * `ROW FORMAT` -Hur data ska formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

    * `STORED AS TEXTFILE LOCATION` -Där data lagras och i vilka filformat.

    * `SELECT` -Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Den här frågan returnerar ett värde för **3** som det finns tre rader som innehåller det här värdet.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive försöker använda schemat för alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra ogiltiga data i resultaten visar den här instruktionen Hive vi bör endast returnera data från filer som slutar på. log.

  > [!NOTE]
  > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa. Till exempel en automatisk överföring av data eller en MapReduce-åtgärd.
  >
  > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

    Kommandots utdata liknar följande:

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

Använd följande steg för att skapa en fil och kör den med hjälp av Beeline.

1. Använd följande kommando för att skapa en fil med namnet **query.hql**:

    ```bash
    nano query.hql
    ```

2. Använd följande text som innehållet i filen. Den här frågan skapar en ny ”interna” tabell med namnet **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa instruktioner utför följande åtgärder:

    * **Skapa tabell om inte finns** -om tabellen inte redan finns, skapas den. Eftersom den **externa** nyckelordet används inte, skapar en intern tabell för den här instruktionen. Interna register lagras i datalagret Hive och hanteras helt av Hive.
    * **LAGRAS AS ORC** -lagrar data i optimerad raden kolumner (ORC)-format. ORC är ett mycket optimerad och effektiv format för att lagra data med Hive.
    * **INFOGA ÖVER... Välj** -väljer rader från den **log4jLogs** tabellen som innehåller **[fel]**, infogar data till den **errorLogs** tabell.

    > [!NOTE]
    > Till skillnad från externa tabeller kan släppa en intern tabell tar bort de underliggande data.

3. Om du vill spara filen, Använd **Ctrl**+**_X**, ange **Y**, och slutligen **RETUR**.

4. Använd följande för att köra filen med Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > Den `-i` parametern startar Beeline och kör instruktionerna i den `query.hql` filen. När frågan har slutförts kan du komma fram till den `jdbc:hive2://headnodehost:10001/>` prompt. Du kan också köra en fil med hjälp av den `-f` som avslutas Beeline när frågan har slutförts.

5. Kontrollera att den **errorLogs** tabellen skapades, Använd följande-instruktion för att returnera alla rader från **errorLogs**:

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

## <a id="remote"></a>Använd Beeline via fjärranslutning

Om du har installerat lokalt Beeline och ansluter via det offentliga internet, använder du följande parametrar:

* __Anslutningssträngen__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Klustrets inloggningsnamn__: `-n admin`

* __Klustret inloggningslösenordet__ `-p 'password'`

Ersätt den `clustername` i anslutningssträngen med namnet på ditt HDInsight-kluster.

Ersätt `admin` med namnet på din klusterinloggning och Ersätt `password` med lösenordet för ditt klusterinloggning.

Om du har installerat lokalt Beeline och ansluta via ett virtuellt Azure-nätverk, kan du använda följande parametrar:

* __Anslutningssträngen__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Du hittar det fullständigt kvalificerade domännamnet för en headnode genom att använda informationen i den [hantera HDInsight med Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentet.

## <a id="sparksql"></a>Använda Beeline med Spark

Spark innehåller sin egen implementering av HiveServer2, som ibland kallas Spark Thrift-servern. Den här tjänsten använder Spark SQL för att lösa frågor i stället för Hive och kan ge bättre prestanda beroende på din fråga.

Anslut till Spark Thrift-servern i ett Spark på HDInsight-kluster genom att använda port `10002` i stället för `10001`. Till exempel `beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'`.

> [!IMPORTANT]
> Spark Thrift-servern är inte tillgänglig direkt via internet. Du kan bara ansluta till den från en SSH-session eller i Azure samma virtuella nätverk som HDInsight-klustret.

## <a id="summary"></a><a id="nextsteps"></a>Nästa steg

Mer allmän information om Hive i HDInsight finns i följande dokument:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Mer information om andra sätt kan du arbeta med Hadoop i HDInsight finns i följande dokument:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument:

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)
* [Använd vyn Ambari Tez på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
