---
title: Fråga Hive genom JDBC driver - Azure HDInsight | Microsoft Docs
description: Använda JDBC-drivrutinen från ett Java-program för att skicka Hive-frågor till Hadoop på HDInsight. Ansluta programmässigt och SQuirrel SQL-klient.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: larryfr
ms.openlocfilehash: 9700408dad36591fc26aa159524f33965b9d1324
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Frågan Hive genom JDBC driver i HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Lär dig hur du använder JDBC driver från ett Java-program för att skicka Hive-frågor till Hadoop i Azure HDInsight. Informationen i det här dokumentet beskrivs hur du ansluta programmässigt och SQuirrel SQL-klient.

Läs mer på gränssnittet Hive JDBC [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Förutsättningar

* En Hadoop på HDInsight-kluster.

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight 3.3 pensionering](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL är en JDBC-klientprogram.

* Den [Java Developer Kit (JDK) version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) eller högre.

* [Apache Maven](https://maven.apache.org). Maven är ett projekt bygg system för Java-projekt som används av projektet som associeras med den här artikeln.

## <a name="jdbc-connection-string"></a>JDBC-anslutningssträng

JDBC-anslutningar till ett HDInsight-kluster i Azure görs över 443 och trafiken skyddas med SSL. Den offentliga gateway som kluster hamnar bakom omdirigerar trafik till den port som faktiskt HiveServer2 lyssnar på. Följande anslutningssträng visar formatet som används för HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.

## <a name="authentication"></a>Autentisering

När anslutningen upprättas, måste du använda HDInsight-kluster admin namn och lösenord för att autentisera till kluster-gateway. När du ansluter från JDBC-klienter, till exempel SQuirreL SQL måste du ange admin namn och lösenord i klientinställningarna.

Från ett Java-program, måste du använda namn och lösenord när en anslutning upprättas. Till exempel öppnas följande Java-kod en ny anslutning med anslutningssträngen, admin namn och lösenord:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Ansluta med SQuirreL SQL-klienten

SQuirreL SQL är en JDBC-klient som kan användas för att köra Hive-frågor via fjärranslutning med ditt HDInsight-kluster. Följande steg förutsätter att du redan har installerat SQuirreL SQL.

1. Skapa en katalog som innehåller filerna. Till exempel `mkdir hivedriver`.

2. Använd följande kommandon för att kopiera filer från HDInsight-kluster från en kommandorad:

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

    Ersätt `USERNAME` med SSH användarkontonamnet för klustret. Ersätt `CLUSTERNAME` med HDInsight-klustrets namn.

3. Starta SQuirreL SQL-programmet. Till vänster i fönstret Välj **drivrutiner**.

    ![Fliken drivrutiner till vänster i fönstret](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Från ikonerna längst upp i den **drivrutiner** markerar den **+** ikon för att skapa en drivrutin.

    ![Drivrutiner ikoner](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Lägg till följande information i dialogrutan Lägg till drivrutin:

    * **Namnet**: Hive
    * **Exempel-URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra klassökvägen**: använda knappen för att lägga till alla jar-filer som hämtats tidigare
    * **Klassnamn**: org.apache.hive.jdbc.HiveDriver

   ![Lägg till dialogrutan för drivrutin](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Klicka på **OK** dessa inställningar ska sparas.

6. Till vänster i fönstret SQuirreL SQL markerar **alias**. Klicka på den **+** ikon för att skapa ett alias för anslutningen.

    ![Lägg till nytt alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Använd följande värden för den **lägga till Alias** dialogrutan.

    * **Namnet**: Hive i HDInsight

    * **Drivrutinen**: Använd listrutan för att välja den **Hive** drivrutin

    * **URL: EN**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

    * **Användarnamnet**: klustret konto inloggningsnamnet för ditt HDInsight-kluster. Standardvärdet är `admin`.

    * **Lösenordet**: lösenordet för inloggningskontot för klustret.

 ![alias dialogrutan Lägg till](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Använd den **Test** för att kontrollera att anslutningen fungerar. När **ansluta till: Hive i HDInsight** dialogrutan visas, väljer du **Anslut** att utföra testet. Om testet lyckas, ser du en **lyckad anslutning** dialogrutan. Om ett fel inträffar, se [felsökning](#troubleshooting).

    För att spara anslutningen alias, Använd den **Ok** knappen längst ned i den **lägga till Alias** dialogrutan.

8. Från den **ansluta till** listrutan överst i SQuirreL SQL, Välj **Hive i HDInsight**. När du uppmanas, Välj **Anslut**.

    ![anslutningsdialogrutan](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. När du är ansluten, ange följande fråga i dialogrutan för SQL-fråga och välj sedan den **kör** ikon. Resultatområdet ska visa resultatet av frågan.

        select * from hivesampletable limit 10;

    ![dialogrutan för SQL-fråga, inklusive resultat](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Ansluta från ett exempel Java-program

Ett exempel på hur en Java-klient för att fråga Hive i HDInsight finns på [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Följ instruktionerna i databasen för att skapa och köra exemplet.

## <a name="troubleshooting"></a>Felsökning

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ett oväntat fel uppstod när att öppna en SQL-anslutning

**Symptom**: vid anslutning till ett HDInsight-kluster som är version 3.3 eller senare, kan du få ett felmeddelande som ett oväntat fel uppstod. Stack-spårning för det här felet börjar med följande rader:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Orsak**: det här felet beror på en äldre version commons codec.jar fil som ingår i SQuirreL.

**Lösning**: åtgärda felet genom att använda följande steg:

1. Hämta commons codec jar-filen från ditt HDInsight-kluster.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Avsluta SQuirreL och gå sedan till katalogen där SQuirreL är installerad på datorn. I katalogen SquirreL under den `lib` directory, Ersätt den befintliga commons codec.jar med det hämtade från HDInsight-klustret.

3. Starta om SQuirreL. Fel bör inte längre uppstå när du ansluter till Hive i HDInsight.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder JDBC för att arbeta med Hive, utforska andra sätt att arbeta med Azure HDInsight med hjälp av följande länkar.

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva frågan Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft Hive ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och köra Hive-frågor med Data Lake-verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Överföra data till HDInsight](../hdinsight-upload-data.md)
* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
