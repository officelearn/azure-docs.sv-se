---
title: Använd Apache Ambari Hive View med Apache Hadoop i Azure HDInsight
description: Läs om hur du använder Hive-vyn från din webbläsare för att skicka Hive-frågor. Hive View är en del av Ambari Web UI som medföljer ditt Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 6c199a0dd75b89d9c9368e799c97a28b73758d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73097104"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Använd Apache Ambari Hive-vyn med Apache Hadoop i HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du kör Hive-frågor med hjälp av Apache Ambari Hive View. Med Hive-vyn kan du skapa, optimera och köra Hive-frågor från din webbläsare.

## <a name="prerequisites"></a>Krav

* Ett Hadoop-kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* En webbläsare

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Välj ditt kluster i [Azure-portalen.](https://portal.azure.com/)  Se [Lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters) för instruktioner. Klustret öppnas i ett nytt portalblad.

1. Välj **Ambari-vyer** **i klusterinstrumentpaneler**. När du uppmanas att autentisera `admin`använder du kontonamnet och lösenordet för klusterinloggning (standard) som du angav när du skapade klustret. Du kan också `https://CLUSTERNAME.azurehdinsight.net/#/main/views` navigera till `CLUSTERNAME` i webbläsaren där är namnet på klustret.

1. Välj __Hive View__i listan med vyer .

    ![Apache Ambari välj Apache Hive-vy](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Visa-sidan för Hive liknar följande bild:

    ![Bild av frågeförslaget för Hive-vyn](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Klistra in följande HiveQL-satser i kalkylbladet på fliken __Fråga:__

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

    Dessa satser utför följande åtgärder:

   * `DROP TABLE`: Tar bort tabellen och datafilen om tabellen redan finns.

   * `CREATE EXTERNAL TABLE`: Skapar en ny "extern" tabell i Hive.
     Externa tabeller lagrar endast tabelldefinitionen i Hive. Data finns kvar på den ursprungliga platsen.

   * `ROW FORMAT`: Visar hur data formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

   * `STORED AS TEXTFILE LOCATION`: Visar var data lagras och att de lagras som text.

   * `SELECT`: Markerar ett antal rader där kolumn t4 innehåller värdet [FEL].

   > [!IMPORTANT]  
   > Lämna __databasvalet__ som __standard__. Exemplen i det här dokumentet använder standarddatabasen som ingår i HDInsight.

1. Om du vill starta frågan väljer du **Kör** under kalkylbladet. Knappen blir orange och texten ändras till **Stopp**.

1. När frågan är klar visar fliken **Resultat** resultatet av åtgärden. Följande text är resultatet av frågan:

        loglevel       count
        [ERROR]        3

    Du kan använda fliken **LOGG** för att visa loggningsinformationen som jobbet har skapat.

   > [!TIP]  
   > Hämta eller spara resultat från listrutan **Åtgärder** under fliken **Resultat.**

### <a name="visual-explain"></a>Visuell förklaring

Om du vill visa en visualisering av frågeplanen väljer du fliken **Visuell förklaring** under kalkylbladet.

Visuell **förklaringsvyn** för frågan kan vara till hjälp för att förstå flödet av komplexa frågor.

### <a name="tez-ui"></a>Tez användargränssnitt

Om du vill visa Tez-användargränssnittet för frågan markerar du fliken **Tez-gränssnitt** under kalkylbladet.

> [!IMPORTANT]  
> Tez används inte för att lösa alla frågor. Du kan lösa många frågor utan att använda Tez.

## <a name="view-job-history"></a>Visa jobbhistorik

På fliken __Jobb__ visas en historik över Hive-frågor.

![Apache Hive visa jobb flikhistorik](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Databastabeller

Du kan använda fliken __Tabeller__ för att arbeta med tabeller i en Hive-databas.

![Bild av fliken Apache Hive-tabeller](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Sparade frågor

På fliken **Fråga** kan du också spara frågor. När du har sparat en fråga kan du återanvända den från fliken __Sparade frågor.__

![Fliken Sparade frågor i Apache Hive-vyn](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Sparade frågor lagras i standardklusterlagringen. Du hittar de sparade frågorna `/user/<username>/hive/scripts`under sökvägen . Dessa lagras som oformaterade textfiler. `.hql`
>
> Om du tar bort klustret, men behåller lagringen, kan du använda ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) eller Data Lake Storage Explorer (från Azure [Portal)](https://portal.azure.com)för att hämta frågorna.

## <a name="user-defined-functions"></a>Användardefinierade funktioner

Du kan utöka Hive genom användardefinierade funktioner (UDF). Använd en UDF för att implementera funktioner eller logik som inte är lätt modelleras i HiveQL.

Deklarera och spara en uppsättning UDF-filer med hjälp av fliken **UDF** högst upp i Hive-vyn. Dessa UDF:er kan användas med **Frågeredigeraren**.

![Apache Hive-vy UDFs flikvisning](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

När du har lagt till en UDF i Hive-vyn visas knappen **Infoga udfs** längst ned i **Frågeredigeraren**. Om du väljer den här posten visas en listruta över de UDF:er som definierats i Hive-vyn. Om du väljer en UDF läggs HiveQL-satser till i frågan för att aktivera UDF.

Om du till exempel har definierat en UDF med följande egenskaper:

* Resursnamn: myudfs

* Resurssökväg: /myudfs.jar

* UDF namn: myawesomeudf

* UDF klass namn: com.myudfs.Awesome

Med knappen **Infoga udfs** visas en post med namnet **myudfs**, med en annan listruta för varje UDF som definierats för den resursen. I det här fallet är det **myawesomeudf.** Om du väljer den här posten läggs följande till i början av frågan:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Du kan sedan använda UDF i frågan. Till exempel `SELECT myawesomeudf(name) FROM people;`.

Mer information om hur du använder UDF med Hive på HDInsight finns i följande artiklar:

* [Använda Python med Apache Hive och Apache Pig i HDInsight](python-udf-hdinsight.md)
* [Så här lägger du till en anpassad Apache Hive UDF i HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Inställningar för Hive

Du kan ändra olika Hive-inställningar, till exempel ändra körningsmotorn för Hive från Tez (standard) till MapReduce.

## <a name="next-steps"></a><a id="nextsteps"></a>Nästa steg

För allmän information om Hive på HDInsight:

* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

För information om andra sätt du kan arbeta med Hadoop på HDInsight:

* [Använd Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
