---
title: Använda Apache Beeline med Apache Hive - Azure HDInsight
description: Lär dig hur du använder Beeline klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 via JDBC.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: dcfcd4b55f848e1725e286e6ef2a87a2c36e5a71
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123152"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Använda Apache Beeline klienten med Apache Hive

Lär dig hur du använder [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) att köra Apache Hive-frågor på HDInsight.

Beeline är en Hive-klient som ska tas med på huvudnoderna i ditt HDInsight-kluster. Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns i ditt HDInsight-kluster. Du kan också använda Beeline för att få fjärråtkomst till Hive på HDInsight via internet. I följande exempel får de vanligaste anslutningssträngar som används för att ansluta till HDInsight från Beeline:

## <a name="types-of-connections"></a>Typer av anslutningar

### <a name="from-an-ssh-session"></a>Från en SSH-session

När du ansluter från en SSH-session till en klusterhuvudnod, du kan sedan ansluta till den `headnodehost` adress på port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Via Azure-nätverk

När du ansluter från en klient till HDInsight via Azure Virtual Network, måste du ange det fullständigt kvalificerade domännamnet (FQDN) av en huvudnod för klustret. Eftersom den här anslutningen görs direkt till noderna i klustret, anslutningen använder port `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersätt `<headnode-FQDN>` med fullständigt kvalificerade domännamnet för en klusterhuvudnod. Använd informationen i för att hitta det fullständigt kvalificerade domännamnet för en huvudnod, den [hantera HDInsight med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentet.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Klustret HDInsight Enterprise Security Package (ESP)

När du ansluter från en klient till ett kluster med Enterprise Security Package (ESP) ansluten till Azure Active Directory (AAD), måste du också ange domännamnet `<AAD-Domain>` och namnet på ett domänanvändarkonto med behörigheter för åtkomst till klustret `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersätt `<username>` med namnet på ett konto på domänen med behörigheter för åtkomst till klustret. Ersätt `<AAD-DOMAIN>` med namnet på den Azure Active Directory (AAD) som klustret tillhör. Använd en versal sträng för den `<AAD-DOMAIN>` annars autentiseringsuppgifterna kommer inte att hitta värdet. Kontrollera `/etc/krb5.conf` för sfärnamn om det behövs.

---

### <a name="over-public-internet"></a>Via offentliga internet

När du ansluter via det offentliga internet, måste du ange kontonamn för inloggning för kluster (standard `admin`) och lösenord. Till exempel använda Beeline från en klientsystem för att ansluta till den `<clustername>.azurehdinsight.net` adress. Den här anslutningen görs via port `443`, och krypteras med SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `admin` med kontot för klusterinloggning för klustret. Ersätt `password` med lösenordet för kontot för klusterinloggning.

---

### <a id="sparksql"></a>Använda Beeline med Apache Spark

Apache Spark tillhandahåller en egen implementering av HiveServer2, som ibland kallas Spark Thrift-server. Den här tjänsten använder Spark SQL för att lösa frågor i stället för Hive och kan ge bättre prestanda beroende på din fråga.

#### <a name="over-public-internet-with-apache-spark"></a>Via offentliga internet med Apache Spark

Anslutningssträngen som används när du ansluter via internet är något annorlunda. I stället för `httpPath=/hive2` är det `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Från head eller inuti Azure Virtual Network med Apache Spark-kluster

När du ansluter direkt från klustrets huvudnod eller från en resurs i Azure samma virtuella nätverk som HDInsight-kluster, port `10002` bör användas för Spark Thrift-server i stället för `10001`. I följande exempel visas hur du ansluter direkt till huvudnoden:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Förhandskrav

* Ett Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observera den [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt kluster primär lagring. Till exempel `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Azure Storage eller Data Lake Storage Gen2, URI: N är `wasbs://` eller `abfss://`respektive. Mer information finns i [säker överföring](../../storage/common/storage-require-secure-transfer.md).


* Alternativ 1: En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). De flesta av stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret.

* Alternativ 2:  En lokal Beeline-klient.


## <a id="beeline"></a>Kör en Hive-fråga

Det här exemplet baseras på användning av Beeline klienten från en SSH-anslutning.

1. Öppna en SSH-anslutning till klustret med koden nedan. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. När du uppmanas, anger du lösenordet för SSH-användarkontot.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Anslut till HiveServer2 med Beeline-klienten från din öppna SSH-session genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline kommandon som börjar med en `!` tecken, till exempel `!help` visar hjälpen. Dock `!` kan utelämnas för vissa kommandon. Till exempel `help` fungerar även.

    Det finns en `!sql`, som används för att köra HiveQL-instruktioner. Dock HiveQL så används ofta för att du kan utelämna det föregående `!sql`. Följande två uttryck är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    På ett nytt kluster visas endast en tabell: **hivesampletable**.

4. Använd följande kommando för att visa schemat för hivesampletable:

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

5. Ange följande instruktioner för att skapa en tabell med namnet **log4jLogs** med hjälp av exempeldata som medföljer HDInsight-klustret: (Ändra vid behov baserat på din [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
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

6. Om du vill avsluta Beeline använder `!exit`.

## <a id="file"></a>Köra en HiveQL-fil

Det här är en fortsättning från föregående exempel. Använd följande steg för att skapa en fil och sedan köra den genom Beeline.

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




## <a id="summary"></a><a id="nextsteps"></a>Nästa steg

Mer allmän information om Hive i HDInsight finns i följande dokument:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Mer information om andra sätt du kan arbeta med Hadoop i HDInsight finns i följande dokument:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
