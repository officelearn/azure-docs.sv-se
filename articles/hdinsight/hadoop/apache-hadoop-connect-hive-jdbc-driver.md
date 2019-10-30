---
title: Fråga Apache Hive genom JDBC-drivrutinen – Azure HDInsight
description: Använd JDBC-drivrutinen från ett Java-program för att skicka Apache Hive frågor till Hadoop på HDInsight. Anslut program mässigt och från SQuirrel SQL-klienten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 2250e41bffc26bd9ae59dfc652a06d08016d227a
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053796"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Fråga Apache Hive genom JDBC-drivrutinen i HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Lär dig hur du använder JDBC-drivrutinen från ett Java-program för att skicka Apache Hive frågor till Apache Hadoop i Azure HDInsight. Informationen i det här dokumentet visar hur du ansluter program mässigt och från SQuirreL SQL-klienten.

Mer information om Hive JDBC-gränssnittet finns i [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Krav

* An-HDInsight Hadoop-kluster. Information om hur du skapar ett finns i [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) version 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) eller senare.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL är ett JDBC-klientprogram.

## <a name="jdbc-connection-string"></a>JDBC-anslutningssträng

JDBC-anslutningar till ett HDInsight-kluster på Azure görs via port 443 och trafiken skyddas med hjälp av SSL. Den offentliga gateway som klustren är bakom omdirigerar trafiken till den port som HiveServer2 faktiskt lyssnar på. Följande anslutnings sträng visar det format som ska användas för HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.

## <a name="authentication"></a>Autentisering

När du upprättar anslutningen måste du använda HDInsight-kluster administratörens namn och lösen ord för att autentisera till kluster-gatewayen. När du ansluter från JDBC-klienter som SQuirreL SQL måste du ange administratörens namn och lösen ord i klient inställningarna.

Från ett Java-program måste du använda namnet och lösen ordet när du upprättar en anslutning. Följande Java-kod öppnar till exempel en ny anslutning med anslutnings strängen, administratörs namnet och lösen ordet:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Anslut med SQuirreL SQL-klient

SQuirreL SQL är en JDBC-klient som kan användas för att fjärrköra Hive-frågor med ditt HDInsight-kluster. Följande steg förutsätter att du redan har installerat SQuirreL SQL.

1. Skapa en katalog som innehåller vissa filer som ska kopieras från klustret.

2. I följande skript ersätter du `sshuser` med namnet på SSH-användarkontot för klustret.  Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.  Ändra arbets katalogen till den som skapades i föregående steg från en kommando rad och ange sedan följande kommando för att kopiera filer från ett HDInsight-kluster:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Starta SQuirreL SQL-programmet. Välj **driv rutiner**från vänster i fönstret.

    ![Fliken driv rutiner till vänster i fönstret](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Från ikonerna överst i dialog rutan **driv rutiner** väljer du ikonen **+** för att skapa en driv rutin.

    ![Ikonen SQuirreL för SQL-programdriv rutiner](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. I dialog rutan Lägg till driv rutin lägger du till följande information:

    |Egenskap | Värde |
    |---|---|
    |Namn|Hive|
    |Exempel-URL|JDBC: hive2://localhost: 443/default; transportMode = http; SSL = True; httpPath =/hive2|
    |Extra klass Sök väg|Använd knappen **Lägg** till för att lägga till alla jar-filer som hämtats tidigare.|
    |Klass namn|org. apache. Hive. JDBC. HiveDriver|

   ![dialog rutan Lägg till driv rutin med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Välj **OK** för att spara inställningarna.

6. Till vänster om SQL-fönstret SQuirreL väljer du **alias**. Välj sedan **+** ikonen för att skapa ett anslutnings Ali Aset.

    ![SQuirreL SQL Lägg till nytt alias dialog ruta](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Använd följande värden för dialog rutan **Lägg till alias** :

    |Egenskap |Värde |
    |---|---|
    |Namn|Hive i HDInsight|
    |Drivrutin|Använd List rutan för att välja **Hive** -drivrutinen.|
    |URL|JDBC: hive2://CLUSTERNAME.azurehdinsight.net: 443/default; transportMode = http; SSL = True; httpPath =/hive2. Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.|
    |Användarnamn|Kluster inloggnings konto namnet för ditt HDInsight-kluster. Standardvärdet är **admin**.|
    |Lösenord|Lösen ordet för klustrets inloggnings konto.|

    ![dialog rutan Lägg till alias med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Använd **test** knappen för att kontrol lera att anslutningen fungerar. När dialog rutan **Anslut till: Hive i HDInsight** visas väljer du **Anslut** för att utföra testet. Om testet lyckas visas en dialog ruta med en **anslutning** . Om ett fel inträffar, se [fel sökning](#troubleshooting).

    Om du vill spara anslutnings Ali Aset använder du knappen **OK** längst ned i dialog rutan **Lägg till alias** .

8. I list rutan **Anslut till** överst i SQuirreL SQL väljer du **Hive i HDInsight**. När du uppmanas väljer du **Anslut**.

    ![dialog rutan anslutning med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. När du är ansluten, ange följande fråga i dialog rutan SQL-fråga och välj sedan ikonen **Kör** (en person som körs). Resultat ytan bör visa resultatet av frågan.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![dialog rutan SQL-fråga, inklusive resultat](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Ansluta från ett Java-exempel till ett Java-program

Ett exempel på hur du använder en Java-klient för att fråga Hive i HDInsight finns på [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Följ instruktionerna i lagrings platsen för att skapa och köra exemplet.

## <a name="troubleshooting"></a>Felsöka

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ett oväntat fel uppstod vid försök att öppna en SQL-anslutning

**Symptom**: när du ansluter till ett HDInsight-kluster som är version 3,3 eller senare kan du få ett fel meddelande om att ett oväntat fel har uppstått. Stack spårningen för det här felet börjar med följande rader:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Orsak**: det här felet orsakas av en äldre version Commons-codec. jar-fil som ingår i SQuirreL.

**Lösning**: åtgärda det här felet med hjälp av följande steg:

1. Avsluta SQuirreL och gå sedan till den katalog där SQuirreL är installerat på datorn, kanske `C:\Program Files\squirrel-sql-4.0.0\lib`. I katalogen SquirreL, under `lib`-katalogen, ersätter du den befintliga Commons-codec. jar med den som hämtats från HDInsight-klustret.

1. Starta om SQuirreL. Felet bör inte längre uppstå när du ansluter till Hive i HDInsight.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder JDBC för att arbeta med Hive kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Visualisera Apache Hive data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva Hive-frågedata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med hjälp av Power Query](apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight-verktyget för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache gris med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
