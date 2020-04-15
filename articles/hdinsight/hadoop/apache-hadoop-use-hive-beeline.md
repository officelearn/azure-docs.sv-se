---
title: Använda Apache Beeline med Apache Hive - Azure HDInsight
description: Lär dig hur du använder Beeline-klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 över JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 426294f20dd51538920182a0e7a2915f6a47ba54
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383563"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Använda Apache Beeline-klienten med Apache Hive

Lär dig hur du använder [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) för att köra Apache Hive-frågor på HDInsight.

Beeline är en Hive-klient som ingår i huvudnoderna i ditt HDInsight-kluster. Om du vill installera Beeline lokalt läser du [Installera beeline-klienten](#install-beeline-client)nedan. Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns på ditt HDInsight-kluster. Du kan också använda Beeline för att komma åt Hive på HDInsight på distans via Internet. Följande exempel innehåller de vanligaste anslutningssträngarna som används för att ansluta till HDInsight från Beeline.

## <a name="types-of-connections"></a>Typer av anslutningar

### <a name="from-an-ssh-session"></a>Från en SSH-session

När du ansluter från en SSH-session till en klusterhuvudnod kan du sedan ansluta till `headnodehost` adressen på porten: `10001`

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Över ett virtuellt Azure-nätverk

När du ansluter från en klient till HDInsight via ett Virtuellt Azure-nätverk måste du ange det fullständigt kvalificerade domännamnet (FQDN) för en klusterhuvudnod. Eftersom den här anslutningen görs direkt till klusternoderna används porten `10001`i anslutningen:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

<a name="replace-headnode-fqdn-with-the-fully-qualified-domain-name-of-a-cluster-headnode-to-find-the-fully-qualified-domain-name-of-a-headnode-use-the-information-in-the-manage-hdinsight-using-the-apache-ambari-rest-api-document"></a>Ersätt `<headnode-FQDN>` med det fullständigt kvalificerade domännamnet för en klusterhuvudnod. Om du vill hitta det fullständigt kvalificerade domännamnet för en headnode använder du informationen i [hantera HDInsight med hjälp av Apache Ambari REST API-dokumentet.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)
---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Till ESP-kluster (HDInsight Enterprise Security Package) med Kerberos

När du ansluter från en klient till ett ESP-kluster (Enterprise Security Package) som är anslutet till Azure Active Directory (AAD)-DS på en dator i samma sfär i klustret, måste du också ange domännamnet `<AAD-Domain>` och namnet på ett domänanvändarkonto med behörighet att komma åt klustret: `<username>`

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersätt `<username>` med namnet på ett konto i domänen med behörighet att komma åt klustret. Ersätt `<AAD-DOMAIN>` med namnet på Azure Active Directory (AAD) som klustret är anslutet till. Använd en versaler `<AAD-DOMAIN>` för värdet, annars hittas inte autentiseringsuppgifterna. Sök `/etc/krb5.conf` efter sfärnamnen om det behövs.

Så här hittar du JDBC-webbadressen från Ambari:

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, var `CLUSTERNAME` är namnet på klustret. Se till att HiveServer2 körs.

1. Använd Urklipp för att kopiera HiveServer2 JDBC-URL:en.

---

### <a name="over-public-or-private-endpoints"></a>Över offentliga eller privata slutpunkter

När du ansluter till ett kluster med hjälp av offentliga eller privata slutpunkter måste du ange namnet på klustrets inloggningskonto (standard) `admin`och lösenord. Till exempel använda Beeline från ett klientsystem för att ansluta till `clustername.azurehdinsight.net` adressen. Den här anslutningen `443`görs över porten och krypteras med TLS/SSL.

Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `admin` med klusterinloggningskontot för klustret. För ESP-kluster använder du hela UPN user@domain.com(till exempel). Ersätt `password` med lösenordet för klusterinloggningskontot.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

eller för privat slutpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Privata slutpunkter pekar på en grundläggande belastningsutjämnare, som endast kan nås från VNETs som peered i samma region. Se [begränsningar för global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. Du kan `curl` använda `-v` kommandot med alternativet för att felsöka eventuella anslutningsproblem med offentliga eller privata slutpunkter innan du använder beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Använd beeline med Apache Spark

Apache Spark tillhandahåller sin egen implementering av HiveServer2, som ibland kallas Spark Thrift-servern. Den här tjänsten använder Spark SQL för att lösa frågor i stället för Hive och kan ge bättre prestanda beroende på din fråga.

#### <a name="through-public-or-private-endpoints"></a>Genom offentliga eller privata slutpunkter

Anslutningssträngen som används är något annorlunda. Istället för `httpPath=/hive2` att `httpPath/sparkhive2`innehålla det är . Ersätt `clustername` med namnet på HDInsight-klustret. Ersätt `admin` med klusterinloggningskontot för klustret. För ESP-kluster använder du hela UPN user@domain.com(till exempel). Ersätt `password` med lösenordet för klusterinloggningskontot.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

eller för privat slutpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Privata slutpunkter pekar på en grundläggande belastningsutjämnare, som endast kan nås från VNETs som peered i samma region. Se [begränsningar för global VNet-peering och belastningsutjämnare](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. Du kan `curl` använda `-v` kommandot med alternativet för att felsöka eventuella anslutningsproblem med offentliga eller privata slutpunkter innan du använder beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Från klusterhuvud eller i Azure Virtual Network med Apache Spark

När du ansluter direkt från klusterhuvudnoden eller från en resurs i samma Virtuella `10002` Azure-nätverk som HDInsight-klustret, bör porten användas för Spark Thrift-server i stället för `10001`. I följande exempel visas hur du ansluter direkt till huvudnoden:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Förutsättningar för exempel

* Ett Hadoop-kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Lägg märke till [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Till exempel `wasb://` för Azure `abfs://` Storage, för Azure Data `adl://` Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure `wasbs://`Storage är URI. Mer information finns i [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* Alternativ 1: En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). De flesta stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret.

* Alternativ 2: En lokal Beeline-klient.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Det här exemplet baseras på hur du använder Beeline-klienten från en SSH-anslutning.

1. Öppna en SSH-anslutning till klustret med koden nedan. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. Ange lösenordet för SSH-användarkontot när du uppmanas att göra det.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Anslut till HiveServer2 med Din Beeline-klient från din öppna SSH-session genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-kommandon börjar `!` med ett `!help` tecken, till exempel visar hjälp. Men `!` kan utelämnas för vissa kommandon. Till exempel `help` fungerar också.

    Det finns `!sql`, som används för att utföra HiveQL-satser. HiveQL används dock så ofta att du kan `!sql`utelämna föregående . Följande två uttalanden är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    I ett nytt kluster visas bara en tabell: **hivesampletable**.

4. Använd följande kommando för att visa schemat för registreringsfilen:

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

5. Ange följande satser för att skapa en tabell med namnet **log4jLogs** med hjälp av exempeldata som medföljer HDInsight-klustret: (Ändra efter behov baserat på [ditt URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Dessa uttalanden gör följande åtgärder:

    |Instruktion |Beskrivning |
    |---|---|
    |SLÄPP TABELL|Om tabellen finns tas den bort.|
    |SKAPA EXTERN TABELL|Skapar en **extern** tabell i Hive. Externa tabeller lagrar bara tabelldefinitionen i Hive. Data finns kvar på den ursprungliga platsen.|
    |RADFORMAT|Hur data formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.|
    |LAGRAD SOM TEXTFILE-PLATS|Var data lagras och i vilket filformat.|
    |VÄLJ|Markerar ett antal rader där kolumn **t4** innehåller värdet **[FEL]**. Den här frågan returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.|
    |INPUT__FILE__NAME SOM %.log|Hive försöker tillämpa schemat på alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. Om du vill förhindra skräpdata i resultatet talar den här satsen om för Hive att det bara ska returnera data från filer som slutar i .log.|

   > [!NOTE]  
   > Externa tabeller bör användas när du förväntar dig att de underliggande data som ska uppdateras av en extern källa. Till exempel en automatisk dataöverföringsprocess eller en MapReduce-åtgärd.
   >
   > Om du släpper en extern tabell tas **inte** data bort, bara tabelldefinitionen.

    Utdata för det här kommandot liknar följande text:

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

6. Avsluta beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Köra en HiveQL-fil

Detta är en fortsättning från föregående exempel. Använd följande steg för att skapa en fil och kör den sedan med Beeline.

1. Använd följande kommando för att skapa en fil med namnet **query.hql**:

    ```bash
    nano query.hql
    ```

1. Använd följande text som innehållet i filen. Den här frågan skapar en ny "intern" tabell med namnet **errorLogs:**

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa uttalanden gör följande åtgärder:

    |Instruktion |Beskrivning |
    |---|---|
    |SKAPA TABELL OM DET INTE FINNS|Om tabellen inte redan finns skapas den. Eftersom nyckelordet **EXTERNAL** inte används skapas en intern tabell i den här satsen. Interna tabeller lagras i Hive-informationslagret och hanteras helt av Hive.|
    |LAGRAS SOM ORC|Lagrar data i ORC-format (Optimized Row Columnar). ORC-formatet är ett mycket optimerat och effektivt format för lagring av Hive-data.|
    |SÄTT ÖVER SKRIVA ÖVER ... Välj|Markerar rader i tabellen **log4jLogs** som innehåller **[FEL]** och infogar sedan data i tabellen **errorLogs.**|

    > [!NOTE]  
    > Till skillnad från externa tabeller tas även underliggande data om du släpper en intern tabell bort.

1. Om du vill spara filen använder du **Ctrl**+**X**och anger sedan **Y**och slutligen **enter**.

1. Använd följande för att köra filen med Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametern `-i` startar Beeline och kör `query.hql` satserna i filen. När frågan är klar kommer `jdbc:hive2://headnodehost:10001/>` du till prompten. Du kan också köra `-f` en fil med parametern som avslutar Beeline när frågan är klar.

1. Om du vill kontrollera att tabellen **errorLogs** skapades använder du följande sats för att returnera alla rader från **errorLogs:**

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tre rader med data ska returneras, alla som innehåller **[FEL]** i kolumn t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Installera beeline-klient

Även om Beeline ingår i huvudnoderna i HDInsight-klustret kanske du vill installera det på en lokal dator.  Stegen nedan för att installera Beeline på en lokal dator är baserade på ett [Windows-delsystem för Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Uppdatera paketlistor. Ange följande kommando i ditt bashskal:

    ```bash
    sudo apt-get update
    ```

1. Installera Java om det inte är installerat. Du kan kontrollera `which java` med kommandot.

    1. Om inget java-paket är installerat anger du följande kommando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Öppna bashrc-filen (finns vanligtvis i ~/.bashrc): `nano ~/.bashrc`.

    1. Ändra bashrc-filen. Lägg till följande rad i slutet av filen:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Tryck sedan på **Ctrl+X**och sedan **Y**och skriv sedan in.

1. Ladda ner Hadoop och Beeline arkiv, ange följande kommandon:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Packa upp arkiven, ange följande kommandon:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Ytterligare ändra bashrc filen. Du måste identifiera sökvägen till var arkiven packades upp. Om du använder [Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/install-win10)och du följde `/mnt/c/Users/user/`stegen `user` exakt, skulle din sökväg vara , var är ditt användarnamn.

    1. Öppna filen:`nano ~/.bashrc`

    1. Ändra kommandona nedan med lämplig sökväg och ange dem sedan i slutet av bashrc-filen:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Tryck sedan på **Ctrl+X**och sedan **Y**och skriv sedan in.

1. Stäng och öppna sedan bashsessionen igen.

1. Testa anslutningen. Använd anslutningsformatet från [Över offentliga eller privata slutpunkter](#over-public-or-private-endpoints)ovan.

## <a name="next-steps"></a>Nästa steg

* Mer allmän information om Hive i HDInsight finns i [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

* Mer information om andra sätt du kan arbeta med Hadoop på HDInsight finns i [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
