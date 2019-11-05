---
title: Använda Apache Beeline med Apache Hive – Azure HDInsight
description: Lär dig hur du använder Beeline-klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 över JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d97470494af0d64cc20d78d69957d84a8acebc16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494897"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Använda Apache Beeline-klienten med Apache Hive

Lär dig hur du använder [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) för att köra apache Hive frågor på HDInsight.

Beeline är en Hive-klient som ingår i head-noderna i HDInsight-klustret. Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns i HDInsight-klustret. Du kan också använda Beeline för att få åtkomst till Hive i HDInsight via Internet. I följande exempel finns de vanligaste anslutnings strängarna som används för att ansluta till HDInsight från Beeline:

## <a name="types-of-connections"></a>Typer av anslutningar

### <a name="from-an-ssh-session"></a>Från en SSH-session

När du ansluter från en SSH-session till ett kluster huvudnoden kan du ansluta till `headnodehost`-adressen på port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Över en Azure-Virtual Network

När du ansluter från en klient till HDInsight över en Azure-Virtual Network måste du ange det fullständigt kvalificerade domän namnet (FQDN) för en kluster huvud nod. Eftersom den här anslutningen görs direkt till klusternoderna använder anslutningen port `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersätt `<headnode-FQDN>` med det fullständigt kvalificerade domän namnet för ett kluster huvudnoden. Om du vill hitta det fullständigt kvalificerade domän namnet för en huvudnoden använder du informationen i [Hantera HDInsight med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) Document.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Till HDInsight Enterprise Security Package-kluster (ESP) med Kerberos

När du ansluter från en klient till ett Enterprise Security Package-kluster (ESP) som är anslutet till Azure Active Directory (AAD)-DS på en dator i samma sfär i klustret, måste du också ange domän namnet `<AAD-Domain>` och namnet på ett domän användar konto med behörighet att åtkomst till klustret `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersätt `<username>` med namnet på ett konto i domänen med behörighet att komma åt klustret. Ersätt `<AAD-DOMAIN>` med namnet på den Azure Active Directory (AAD) som klustret är anslutet till. Använd en versal sträng för värdet `<AAD-DOMAIN>`, annars hittas inte autentiseringsuppgiften. Kontrol lera `/etc/krb5.conf` för sfär namnen om det behövs.

---

### <a name="over-public-or-private-endpoints"></a>Över offentliga eller privata slut punkter

När du ansluter till ett kluster med hjälp av offentliga eller privata slut punkter måste du ange konto namnet för kluster inloggning (standard `admin`) och lösen ord. Du kan till exempel använda Beeline från ett klient system för att ansluta till `<clustername>.azurehdinsight.net`-adressen. Den här anslutningen görs via port `443` och krypteras med SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

eller för privat slut punkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `<username>` med kluster inloggnings kontot för klustret. Obs! för ESP-kluster används fullständigt UPN (t. ex. user@domain.com). Ersätt `password` med lösen ordet för klustrets inloggnings konto.

Privata slut punkter pekar på en grundläggande belastningsutjämnare som bara kan nås från virtuella nätverk-peer i samma region. Mer information finns i [begränsningar i global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Du kan använda kommandot `curl` med `-v` för att felsöka anslutnings problem med offentliga eller privata slut punkter innan du använder Beeline.

---

### <a id="sparksql"></a>Använda Beeline med Apache Spark

Apache Spark tillhandahåller en egen implementering av HiveServer2, som ibland kallas Spark Thrift-servern. Den här tjänsten använder Spark SQL för att matcha frågor i stället för Hive, och kan ge bättre prestanda beroende på din fråga.

#### <a name="through-public-or-private-endpoints"></a>Via offentliga eller privata slut punkter

Den anslutnings sträng som används skiljer sig något åt. I stället för att innehålla `httpPath=/hive2` är det `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

eller för privat slut punkt:

```bash 
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `<username>` med kluster inloggnings kontot för klustret. Obs! för ESP-kluster används fullständigt UPN (t. ex. user@domain.com). Ersätt `password` med lösen ordet för klustrets inloggnings konto.

Privata slut punkter pekar på en grundläggande belastningsutjämnare som bara kan nås från virtuella nätverk-peer i samma region. Mer information finns i [begränsningar i global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Du kan använda kommandot `curl` med `-v` för att felsöka anslutnings problem med offentliga eller privata slut punkter innan du använder Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Från kluster huvud eller inom Azure Virtual Network med Apache Spark

När du ansluter direkt från klustrets huvud nod, eller från en resurs i samma Azure-Virtual Network som HDInsight-klustret, ska port `10002` användas för Spark Thrift-servern i stället för `10001`. I följande exempel visas hur du ansluter direkt till Head-noden:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Förhandskrav

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Lägg märke till [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Till exempel `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har Aktiver ATS för Azure Storage är URI: n `wasbs://`. Mer information finns i [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* Alternativ 1: en SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). De flesta av stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret.

* Alternativ 2: en lokal Beeline-klient.

## <a id="beeline"></a>Köra en Hive-fråga

Det här exemplet baseras på användningen av Beeline-klienten från en SSH-anslutning.

1. Öppna en SSH-anslutning till klustret med koden nedan. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. När du uppmanas till det anger du lösen ordet för SSH-användarkontot.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Anslut till HiveServer2 med Beeline-klienten från den öppna SSH-sessionen genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-kommandon börjar med ett `!`-Character, till exempel `!help` visar hjälp. Men `!` kan utelämnas för vissa kommandon. `help` fungerar till exempel också.

    Det finns `!sql`, som används för att köra HiveQL-instruktioner. HiveQL används dock ofta så att du kan utelämna föregående `!sql`. Följande två uttryck är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    I ett nytt kluster visas endast en tabell: **hivesampletable**.

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

5. Ange följande instruktioner för att skapa en tabell med namnet **log4jLogs** med hjälp av exempel data från HDInsight-klustret: (ändra efter behov baserat på ditt [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme)).

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

    * `DROP TABLE` – om tabellen finns tas den bort.

    * `CREATE EXTERNAL TABLE`-skapar en **extern** tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. Data finns kvar på den ursprungliga platsen.

    * `ROW FORMAT` – hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.

    * `STORED AS TEXTFILE LOCATION` – var data lagras och i vilket fil format.

    * `SELECT` – väljer antalet rader där kolumnen **T4** innehåller värdet **[Error]** . Den här frågan returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.

    * `INPUT__FILE__NAME LIKE '%.log'`-Hive försöker tillämpa schemat på alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräp data i resultaten anger den här instruktionen Hive att den bara ska returnera data från filer som slutar med. log.

   > [!NOTE]  
   > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa. Till exempel en automatiserad data överförings process eller en MapReduce-åtgärd.
   >
   > Att släppa en extern tabell tar **inte** bort data, endast tabell definitionen.

    Utdata från det här kommandot liknar följande text:

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

6. Använd `!exit` om du vill avsluta Beeline.

## <a id="file"></a>Köra en HiveQL-fil

Detta är en fortsättning från föregående exempel. Använd följande steg för att skapa en fil och kör den med hjälp av Beeline.

1. Använd följande kommando för att skapa en fil med namnet **Query. HQL**:

    ```bash
    nano query.hql
    ```

2. Använd följande text som filens innehåll. Den här frågan skapar en ny intern tabell med namnet **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa instruktioner utför följande åtgärder:

   * **CREATE TABLE om inte finns** – om tabellen inte redan finns skapas den. Eftersom det **externa** nyckelordet inte används skapar den här instruktionen en intern tabell. Interna tabeller lagras i Hive-datalagret och hanteras fullständigt av Hive.
   * **Lagras som Orc** – lagrar data i optimerade rad kolumners (Orc)-format. ORC-formatet är ett mycket optimerat och effektivt format för att lagra Hive-data.
   * **Infoga ÖVERskrivning... SELECT** -markerar rader från tabellen **log4jLogs** som innehåller **[Error]** och infogar sedan data i **errorLogs** -tabellen.

    > [!NOTE]  
    > Till skillnad från externa tabeller, tar en intern tabell bort även underliggande data.

3. Om du vill spara filen använder du **Ctrl**+ **_X**och anger sedan **Y**och slutligen **RETUR**.

4. Använd följande för att köra filen med Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametern `-i` startar Beeline och kör instruktionerna i `query.hql`-filen. När frågan har slutförts visas `jdbc:hive2://headnodehost:10001/>`-prompten. Du kan också köra en fil med parametern `-f`, som avslutar Beeline när frågan har slutförts.

5. Verifiera att tabellen **errorLogs** har skapats genom att använda följande instruktion för att returnera alla rader från **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tre rader med data ska returneras, alla innehåller **[Error]** i kolumnen T4:

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

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Mer information om andra sätt att arbeta med Hadoop i HDInsight finns i följande dokument:

* [Använda Apache gris med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
