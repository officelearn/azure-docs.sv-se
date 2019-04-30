---
title: Använd Apache Ambari-vyer för att arbeta med Hive på HDInsight (Apache Hadoop) – Azure
description: Lär dig använda Hive-vyn från din webbläsare för att skicka Hive-frågor. Hive-vyn är en del av Ambari-Webbgränssnittet som medföljer ditt Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095327"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Använd Apache Ambari Hive-vyn med Apache Hadoop i HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig mer om att köra Hive-frågor med hjälp av Apache Ambari Hive-vy. Hive-vyn kan du skapa, optimera och köra Hive-frågor från din webbläsare.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* En webbläsare

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Från den [Azure-portalen](https://portal.azure.com/), Välj ditt kluster.  Se [lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters) anvisningar. Klustret har öppnats i ett nytt portalblad.

2. Från **Klusterinstrumentpaneler**väljer **Ambari-vyer**. När du uppmanas att autentisera använder klusterinloggning (standard `admin`) konto och lösenord som du angav när du skapade klustret.

3. I listan med vyer, Välj __Hive-vy__.

    ![Hive-vyn som valts](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Sidan Visa Hive liknar följande bild:

    ![Bild av fråga kalkylbladet för Hive-vyn](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Från den __fråga__ och klistra in följande HiveQL-instruktioner i kalkylbladet:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Dessa instruktioner utför följande åtgärder:

   * `DROP TABLE`: Tar bort tabellen och datafilen, om tabellen redan finns.

   * `CREATE EXTERNAL TABLE`: Skapar en ny ”external” tabell i Hive.
     Externa tabeller lagra endast tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

   * `ROW FORMAT`: Visar hur data ska formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

   * `STORED AS TEXTFILE LOCATION`: Visar var data lagras och att den lagras som text.

   * `SELECT`: Väljer en uppräkning av alla rader där kolumnen t4 innehåller värdet [fel].

   > [!IMPORTANT]  
   > Lämna den __databasen__ valet __standard__. I exemplen i det här dokumentet används standarddatabasen som ingår i HDInsight.

5. För att starta frågan markerar **kör** under kalkylbladet. Knappen blir orange och texten ändras till **stoppa**.

6. När frågan har slutförts, den **resultat** fliken visas resultatet av åtgärden. Följande text är resultatet av frågan:

        loglevel       count
        [ERROR]        3

    Du kan använda den **LOG** fliken för att visa loggningsinformation som skapats för jobbet.

   > [!TIP]  
   > Hämta eller spara resultatet från den **åtgärder** listrutan i dialogrutan som visas under den **resultat** fliken.

### <a name="visual-explain"></a>Visual förklara

Om du vill visa en visualisering av frågeplanen, Välj den **Visual förklara** fliken under kalkylbladet.

Den **Visual förklara** vy av frågan kan vara lättare att förstå flödet av komplexa frågor.

### <a name="tez-ui"></a>Tez UI

Om du vill visa Tez UI för frågan, Välj den **Tez UI** fliken under kalkylbladet.

> [!IMPORTANT]  
> Tez används inte för att lösa alla frågor. Du kan lösa många frågor utan att använda Tez. 

## <a name="view-job-history"></a>Visa jobbhistorik

Den __jobb__ fliken visar en historik över Hive-frågor.

![Bild av jobbets historik](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Databastabeller

Du kan använda den __tabeller__ fliken för att arbeta med tabeller i en Hive-databas.

![Bild av fliken tabeller](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Sparade frågor

Från den **fråga** och du kan också spara frågor. När du har sparat en fråga kan du återanvända det från den __sparade frågor__ fliken.

![Bild av fliken sparade frågor](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Sparade frågor lagras i standardklusterlagringen. Du kan hitta de sparade frågorna under sökvägen `/user/<username>/hive/scripts`. Dessa lagras som klartext `.hql` filer.
>
> Om du tar bort klustret men behålla lagringen, kan du använda ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) eller Data Lake Storage Explorer (från den [Azure-portalen](https://portal.azure.com)) att hämta frågorna.

## <a name="user-defined-functions"></a>Användardefinierade funktioner

Du kan utöka Hive via användardefinierade funktioner (UDF). Använd en UDF för att implementera funktioner eller logik som inte är enkelt modellerats i HiveQL.

Deklarera och spara en uppsättning UDF: er med hjälp av den **UDF** fliken högst upp på Hive-vyn. Dessa UDF: er kan användas med den **frågeredigeraren**.

![Bild av fliken UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

När du har lagt till en UDF till Hive-vyn en **Infoga UDF: er** visas knappen längst ned på den **frågeredigeraren**. Att välja den här posten visar en nedrullningsbar lista över UDF: er som definieras i Hive-vyn. Att välja en UDF lägger till HiveQL-instruktioner i din fråga för att aktivera en användardefinierad funktion.

Exempel: Om du har definierat en UDF med följande egenskaper:

* Resursnamn: myudfs

* Resurssökväg: /myudfs.jar

* UDF-namn: myawesomeudf

* Klassnamn för UDF: com.myudfs.Awesome

Med hjälp av den **Infoga UDF: er** anslutningsknappen visar en post med namnet **myudfs**, med en annan nedrullningsbara listrutan för varje UDF som definierats för den här resursen. I det här fallet är det **myawesomeudf**. Att välja den här posten lägger till följande i början av frågan:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Du kan sedan använda en användardefinierad funktion i din fråga. Till exempel `SELECT myawesomeudf(name) FROM people;`.

Mer information om hur du använder UDF med Hive i HDInsight finns i följande artiklar:

* [Med hjälp av Python med Apache Hive och Apache Pig i HDInsight](python-udf-hdinsight.md)
* [Lägga till en anpassad Apache Hive UDF i HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive-inställningar

Du kan ändra olika Hive-inställningar, till exempel ändra Körningsmotor för Hive från Tez (standard) för MapReduce.

## <a id="nextsteps"></a>Nästa steg

Allmän information om Hive på HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)
