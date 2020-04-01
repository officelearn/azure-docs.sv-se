---
title: Fråga Apache Hive via JDBC-drivrutinen - Azure HDInsight
description: Använd JDBC-drivrutinen från ett Java-program för att skicka Apache Hive-frågor till Hadoop på HDInsight. Anslut programmässigt och från SQuirrel SQL-klienten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 8129239f152f6b359b930e56466052da12ef4d42
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437036"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Fråga Apache Hive via JDBC-drivrutinen i HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Lär dig hur du använder JDBC-drivrutinen från ett Java-program för att skicka Apache Hive-frågor till Apache Hadoop i Azure HDInsight. Informationen i det här dokumentet visar hur du ansluter programmässigt och från SQL-klienten SQuirreL.

Mer information om Hive JDBC-gränssnittet finns i [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Krav

* Ett HDInsight Hadoop-kluster. Information om hur du skapar en information finns [i Komma igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Kontrollera att tjänsten HiveServer2 körs.
* [Java Developer Kit (JDK) version 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) eller senare.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL är ett JDBC-klientprogram.

## <a name="jdbc-connection-string"></a>JDBC-anslutningssträng

JDBC-anslutningar till ett HDInsight-kluster på Azure görs över port 443 och trafiken är skyddad med TLS/SSL. Den offentliga gatewayen som klustren sitter bakom omdirigerar trafiken till den port som HiveServer2 faktiskt lyssnar på. Följande anslutningssträng visar det format som ska användas för HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.

Eller så kan du få anslutningen via **Ambari UI > Hive > Configs > Advanced**.

![Hämta JDBC-anslutningssträngen via Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Värdnamn i anslutningssträng

Värdnamnet "CLUSTERNAME.azurehdinsight.net" i anslutningssträngen är samma som kluster-URL:en. Du kan få det via Azure-portalen. 

### <a name="port-in-connection-string"></a>Port i anslutningssträng

Du kan bara använda **port 443** för att ansluta till klustret från vissa platser utanför det virtuella Azure-nätverket. HDInsight är en hanterad tjänst, vilket innebär att alla anslutningar till klustret hanteras via en säker gateway. Du kan inte ansluta till HiveServer 2 direkt på portarna 10001 eller 10000 eftersom dessa portar inte är exponerade för utsidan. 

## <a name="authentication"></a>Autentisering

När du upprättar anslutningen måste du använda hdinsight-klusteradministratörens namn och lösenord för att autentisera till klustergatewayen. När du ansluter från JDBC-klienter som SQuirreL SQL måste du ange administratörsnamnet och lösenordet i klientinställningarna.

Från ett Java-program måste du använda namnet och lösenordet när du upprättar en anslutning. Följande Java-kod öppnar till exempel en ny anslutning med anslutningssträngen, administratörsnamnet och lösenordet:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Anslut med SQuirreL SQL-klient

SQuirreL SQL är en JDBC-klient som kan användas för att fjärrköra Hive-frågor med ditt HDInsight-kluster. Följande steg förutsätter att du redan har installerat SQuirreL SQL.

1. Skapa en katalog som innehåller vissa filer som ska kopieras från klustret.

2. I följande skript `sshuser` ersätter du med SSH-användarkontonamnet för klustret.  Ersätt `CLUSTERNAME` med HDInsight-klusternamnet.  Från en kommandorad ändrar du arbetskatalogen till den som skapades i föregående steg och anger sedan följande kommando för att kopiera filer från ett HDInsight-kluster:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Starta SQL-programmet SQuirreL. Välj **Drivrutiner**till vänster om fönstret .

    ![Fliken Drivrutiner till vänster om fönstret](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Välj ikonen **+** för att skapa en drivrutin högst upp i dialogrutan **Drivrutiner.**

    ![Ikon för Ikon för SQuirreL SQL-programdrivrutiner](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Lägg till följande information i dialogrutan Lägg till drivrutin:

    |Egenskap | Värde |
    |---|---|
    |Namn|Hive|
    |Exempel PÅ URL|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Extra klassbana|Använd knappen **Lägg** till om du vill lägga till alla jar-filer som hämtats tidigare.|
    |Klassnamn|org.apache.hive.jdbc.HiveDriver|

   ![lägga till drivrutinsdialogruta med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Välj **OK** för att spara dessa inställningar.

6. Till vänster om SQL-fönstret SQuirreL väljer du **Alias**. Välj sedan **+** ikonen för att skapa ett anslutningsalias.

    ![SQuirreL SQL lägga till nya alias dialog](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Använd följande värden för dialogrutan **Lägg till alias:**

    |Egenskap |Värde |
    |---|---|
    |Namn|Hive på HDInsight|
    |Drivrutin|Använd listrutan för att välja Hive-drivrutinen. **Hive**|
    |URL|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.|
    |Användarnamn|Klusterinloggningskontonamnet för ditt HDInsight-kluster. Standard är **admin**.|
    |lösenord|Lösenordet för klusterinloggningskontot.|

    ![lägga till aliasdialogruta med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Använd knappen **Testa** för att kontrollera att anslutningen fungerar. När **Dialogrutan Anslut till: Hive på HDInsight** visas väljer du **Anslut** för att utföra testet. Om testet lyckas visas en **dialogruta för anslutning.** Om ett fel uppstår läser du [Felsöka](#troubleshooting).

    Om du vill spara anslutningsaliaset använder du knappen **Ok** längst ned i dialogrutan **Lägg till alias.**

8. Välj **Hive på HDInsight**i listrutan **Anslut till** högst upp i SQuirreL SQL . När du uppmanas till det väljer du **Anslut**.

    ![anslutningsdialogruta med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. När du har anslutit anger du följande fråga i SQL-frågedialogrutan och väljer sedan ikonen **Kör** (en person som körs). Resultatområdet ska visa resultatet av frågan.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![sql-frågedialogruta, inklusive resultat](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Ansluta från ett java-exempel

Ett exempel på hur du använder en Java-klient [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)för att fråga Hive på HDInsight finns på . Följ instruktionerna i databasen för att skapa och köra exemplet.

## <a name="troubleshooting"></a>Felsökning

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Oväntat fel uppstod när en SQL-anslutning skulle öppnas

**Symptom:** När du ansluter till ett HDInsight-kluster som är version 3.3 eller senare kan du få ett felmeddelande om att ett oväntat fel uppstod. Stackspårningen för det här felet börjar med följande rader:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Orsak**: Det här felet orsakas av en äldre version commons-codec.jar fil som ingår i SQuirreL.

**Lösning**: Så här åtgärdar du det här felet:

1. Avsluta SQuirreL, och sedan gå till katalogen där SQuirreL är installerat på ditt system, kanske `C:\Program Files\squirrel-sql-4.0.0\lib`. I SquirreL-katalogen, `lib` under katalogen, ersätter du den befintliga commons-codec.jar med den som hämtats från HDInsight-klustret.

1. Starta om SQuirreL. Felet ska inte längre uppstå när du ansluter till Hive på HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Anslutning frånkopplad av HDInsight

**Symptom:** När du försöker ladda ner enorma mängder data (säger flera GB) genom JDBC / ODBC, anslutningen kopplas bort av HDInsight oväntat medan du laddar ner. 

**Orsak**: Det här felet orsakas av begränsningen för gatewaynoder. När du hämtar data från JDBC/ODBC måste alla data passera genom gateway-noden. En gateway är dock inte utformad för att hämta en stor mängd data, så anslutningen kan stängas av gatewayen om den inte kan hantera trafiken.

**Upplösning**: Undvik att använda JDBC / ODBC drivrutin för att ladda ner enorma mängder data. Kopiera data direkt från blob-lagring i stället.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder JDBC för att arbeta med Hive använder du följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva frågeinsdata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive-frågor med DataSjöverktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight Tool för Visual Studio-kod](../hdinsight-for-vscode.md).
* [Överföra data till HDInsight](../hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache Hive med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
