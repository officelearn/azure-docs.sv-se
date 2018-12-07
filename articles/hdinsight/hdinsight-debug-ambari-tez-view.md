---
title: Använd Apache Ambari Tez vyn HDInsight - Azure
description: Lär dig använda Apache Ambari Tez-vyn för att felsöka Tez-jobb på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 312b476f8809d1d6375cc20035901d8d11c32173
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012359"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Använd Apache Ambari-vyer för att felsöka Apache Tez-jobb på HDInsight

Den [Apache Ambari](https://ambari.apache.org/) Webbgränssnittet för HDInsight innehåller en [Apache TEZ](https://tez.apache.org/) vy som kan användas för att förstå och felsöka jobb som använder Tez. Tez-vy kan du visualisera för jobbet som ett diagram över anslutna objekt, öka detaljnivån i varje objekt och hämta statistik och loggningsinformation.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versionshantering för HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Förutsättningar

* Ett Linux-baserade HDInsight-kluster. Anvisningar om hur du skapar ett kluster finns i [komma igång med Linux-baserade HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* En modern webbläsare som stöder HTML5.

## <a name="understanding-apache-tez"></a>Förstå Apache Tez

Tez är ett utökningsbart ramverk för att bearbeta i Apache Hadoop och ger högre hastigheter än med traditionella MapReduce-bearbetning. För Linux-baserade HDInsight-kluster är den standard-motor för Hive.

Tez skapar en dirigeras Acyklisk graf (DAG) som beskriver ordningen på åtgärder som krävs av jobb. Enskilda åtgärder kallas hörn och köra en del av det övergripande jobbet. Den faktiska körningen av arbetet som beskrivs av en brytpunkt kallas en uppgift och kan distribueras över flera noder i klustret.

### <a name="understanding-the-tez-view"></a>Förstå Tez-vy

Tez-vyn innehåller både historisk information och information om processer som körs. Den här informationen visar hur ett jobb ska distribueras mellan datorkluster. Den visar även räknare som används av uppgifter och hörn och information om fel relaterade till jobbet. Det kan erbjuda användbar information i följande scenarier:

* Övervaka tidskrävande bearbetar, visa förloppet för kartan och minska uppgifter.
* Analys av historiska data för lyckade eller misslyckade processer för att lära dig hur vi kan förbättra bearbetning eller varför den misslyckades.

## <a name="generate-a-dag"></a>Skapa en grupp för Databastillgänglighet

Tez-vyn innehåller bara data om ett jobb som använder Tez-motorn körs eller har körts tidigare. Enkel Hive-frågor kan lösas utan att använda Tez. Mer komplexa frågor som gör som filtrering, gruppering, beställningar, kopplingar, osv. använda Tez-motorn.

Använd följande steg för att köra en Hive-fråga som använder Tez:

1. I en webbläsare, navigerar du till https://CLUSTERNAME.azurehdinsight.net, där **CLUSTERNAME** är namnet på ditt HDInsight-kluster.

2. På menyn överst på sidan, väljer den **vyer** ikon. Den här ikonen som ser ut som en serie rutor. I listrutan som visas, Välj **Hive visa**.

    ![Att välja Hive-vyn](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. När Hive-vyn läses in, klistra in följande fråga i frågeredigeraren och klicka sedan på **köra**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    När jobbet har slutförts bör du se utdata som visas i den **Frågeprocessresultat** avsnittet. Resultatet bör likna följande:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Välj den **Log** fliken. Du ser information liknande följande text:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Spara den **App-id** eftersom det här värdet används i nästa avsnitt.

## <a name="use-the-tez-view"></a>Använda Tez-vy

1. På menyn överst på sidan, väljer den **vyer** ikon. I listrutan som visas, Välj **Tez visa**.

    ![Markerat Tez-vy](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. När Tez-vyn har lästs in visas en lista över hive-frågor som körs eller har körts på klustret.

    ![Alla dag](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Om du har bara en post, är det för den fråga som du körde i föregående avsnitt. Om du har flera poster kan söka du med hjälp av fälten överst på sidan.

4. Välj den **fråge-ID** för en Hive-fråga. Information om frågan visas.

    ![Information om DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Flikarna på den här sidan kan du se följande information:

    * **Frågedetaljer**: information om Hive-frågan.
    * **Tidslinje**: Information om hur lång tid varje steg i processen tog.
    * **Konfigurationer**: den konfiguration som använts för den här frågan.

    Från __Frågedetaljer__ du kan använda länkar för att hitta information om den __programmet__ eller __DAG__ för den här frågan.
    
    * Den __program__ länken visar information om YARN-programmet för den här frågan. Härifrån kan du komma åt YARN-programloggar.
    * Den __DAG__ länken visar information om riktad Acyklisk graf för den här frågan. Härifrån kan du visa en grafisk representation av gruppen för Databastillgänglighet. Du kan också hitta information om hörn i gruppen för Databastillgänglighet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Tez-vy, Läs mer om [med Apache Hive på HDInsight](hadoop/hdinsight-use-hive.md).

Mer detaljerad teknisk information om Apache Tez finns i den [Apache Tez-sidan på Hortonworks](https://hortonworks.com/hadoop/tez/).

Mer information om hur du använder Apache Ambari med HDInsight finns i [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
