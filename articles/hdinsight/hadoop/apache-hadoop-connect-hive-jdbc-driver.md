---
title: Fråga Hive genom JDBC driver - Azure HDInsight
description: Använda JDBC-drivrutinen från ett Java-program för att skicka Hive-frågor till Hadoop på HDInsight. Anslut och programmässigt från SQuirrel SQL-klienten.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: jasonh
ms.openlocfilehash: 9c7881d0cc4f0c2c13f34fa8909d15dec1bf121a
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591677"
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Fråga Hive via JDBC-drivrutinen i HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Lär dig hur du använder JDBC-drivrutinen från ett Java-program för att skicka Hive-frågor till Hadoop i Azure HDInsight. Informationen i det här dokumentet visar hur du ansluter och programmässigt från SQuirrel SQL-klienten.

Läs mer på gränssnittet Hive JDBC [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Förutsättningar

* Ett Hadoop på HDInsight-kluster.

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight 3.3 tillbakadragande](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL är en JDBC-klientprogram.

* Den [Java Developer Kit (JDK) version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) eller högre.

* [Apache Maven](https://maven.apache.org). Maven är ett projekt bygg system för Java-projekt som används av projektet som är associerade med den här artikeln.

## <a name="jdbc-connection-string"></a>JDBC-anslutningssträng

JDBC-anslutningar till ett HDInsight-kluster på Azure görs över 443 och trafiken skyddas med SSL. Den offentliga gatewayen som kluster som finns bakom dirigerar trafiken till den port som faktiskt HiveServer2 lyssnar på. Följande anslutningssträng visar formatet som används för HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.

## <a name="authentication"></a>Autentisering

När anslutningen upprättas, måste du använda HDInsight-kluster administratörsnamn och lösenord för att autentisera till kluster-gateway. När du ansluter från JDBC-klienter, till exempel SQuirreL SQL, måste du ange administratörsnamn och lösenord i klientinställningarna.

Från ett Java-program, måste du använda namn och lösenord när en anslutning upprättas. Till exempel öppnas följande Java-kod en ny anslutning med anslutningssträngen, administratörsnamn och lösenord:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Ansluta med SQuirreL SQL-klienten

SQuirreL SQL är en JDBC-klient som kan användas för att köra Hive-frågor via en fjärranslutning med ditt HDInsight-kluster. Följande steg förutsätter att du redan har installerat SQuirreL SQL.

1. Skapa en katalog som innehåller filerna. Till exempel `mkdir hivedriver`.

2. Använd följande kommandon för att kopiera filerna från HDInsight-kluster från en kommandorad:

    ```bash
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    ```

    Ersätt `USERNAME` med SSH-användarkontonamnet för klustret. Ersätt `CLUSTERNAME` med HDInsight-klusternamnet.

3. Starta SQuirreL SQL-programmet. Till vänster i fönstret Välj **drivrutiner**.

    ![Fliken drivrutiner till vänster i fönstret](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Bland ikonerna överst i den **drivrutiner** dialogrutan den **+** ikon för att skapa en drivrutin.

    ![Drivrutiner ikoner](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Lägg till följande information i dialogrutan Lägg till drivrutin:

    * **Namn på**: Hive
    * **Exempel-URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra klassökvägen**: Använd knappen Lägg till att lägga till i alla jar-filer som hämtats tidigare
    * **Klassnamn**: org.apache.hive.jdbc.HiveDriver

   ![Lägg till drivrutin dialogrutan](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Klicka på **OK** dessa inställningar ska sparas.

6. Till vänster i SQuirreL SQL-fönstret Välj **alias**. Klicka sedan på den **+** ikon för att skapa ett alias för anslutningen.

    ![Lägg till nytt alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Använd följande värden för den **lägga till Alias** dialogrutan.

    * **Namn på**: Hive på HDInsight

    * **Drivrutinen**: Använd listrutan för att välja den **Hive** drivrutin

    * **URL: EN**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

    * **Användarnamnet**: kluster konto inloggningsnamnet för ditt HDInsight-kluster. Standardvärdet är `admin`.

    * **Lösenord**: lösenordet för kontot för klusterinloggning.

 ![Lägg till alias dialogrutan](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Använd den **Test** knappen för att kontrollera att anslutningen fungerar. När **ansluta till: Hive på HDInsight** dialogrutan visas, väljer **Connect** att utföra testet. Om testet lyckas visas en **anslutningen lyckades** dialogrutan. Om ett fel inträffar, se [felsökning](#troubleshooting).

    Spara anslutningen aliaset genom att använda den **Ok** knappen längst ned på den **lägga till Alias** dialogrutan.

8. Från den **ansluta till** listrutan överst i SQuirreL SQL, Välj **Hive på HDInsight**. När du uppmanas, väljer **Connect**.

    ![dialogruta för anslutning](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. När du är ansluten, anger du följande fråga i dialogrutan SQL-fråga och välj sedan den **kör** ikon. Resultatområdet ska visa resultatet av frågan.

        select * from hivesampletable limit 10;

    ![dialogrutan för SQL-fråga, inklusive resultat](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Ansluta från ett exempel Java-program

Ett exempel på hur du använder en Java-klient för att fråga Hive på HDInsight finns på [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Följ instruktionerna i databasen för att skapa och köra exemplet.

## <a name="troubleshooting"></a>Felsökning

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ett oväntat fel uppstod vid försök att öppna en SQL-anslutning

**Symptom**: när du ansluter till ett HDInsight-kluster som är version 3.3 eller senare, kan du få ett felmeddelande som ett oväntat fel uppstod. Stackspårning för det här felet börjar med följande rader:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Orsak**: det här felet beror på en äldre version commons codec.jar fil som ingår i SQuirreL.

**Lösning**: åtgärda felet genom att använda följande steg:

1. Ladda ned commons-codec jar-filen från ditt HDInsight-kluster.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Avsluta SQuirreL och går sedan till katalogen där SQuirreL är installerad på datorn. I katalogen SquirreL under den `lib` directory, Ersätt den befintliga commons codec.jar med det hämtade från HDInsight-kluster.

3. Starta om SQuirreL. Felet ska inte längre uppstå när du ansluter till Hive på HDInsight.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder JDBC för att arbeta med Hive, Använd följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md)
* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
