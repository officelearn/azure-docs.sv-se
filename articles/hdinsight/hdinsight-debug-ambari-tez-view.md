---
title: "Använda Ambari Tez vyn med HDInsight - Azure | Microsoft Docs"
description: "Lär dig använda Ambari Tez-vyn för att felsöka Tez-jobb i HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: b565ef0f7672d1288e922e28551ad3f6ec5b6cb7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Använda Ambari-vyer för att felsöka Tez-jobb i HDInsight

Ambari-Webbgränssnittet för HDInsight innehåller en vy i Tez som kan användas för att förstå och felsöka jobb som använder Tez. Tez-vyn kan du visualisera jobbet som ett diagram över anslutna objekt, detaljer om varje objekt, och hämta statistik och loggningsinformation.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight component-versioning](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Krav

* Ett Linux-baserat HDInsight-kluster. Anvisningar om hur du skapar ett kluster finns [komma igång med Linux-baserat HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* En modern webbläsare som stöder HTML5.

## <a name="understanding-tez"></a>Förstå Tez

Tez är ett utökningsbart ramverk för databearbetning i Hadoop och som ger högre hastigheter än traditionella MapReduce-bearbetning. För Linux-baserade HDInsight-kluster är det standard motor för Hive.

Tez skapar en dirigeras acykliska diagram (DAG) som beskriver ordningen för åtgärder som krävs av jobb. Enskilda åtgärder kallas formhörnen och köra en del av en övergripande jobbet. Faktiska körningen av det arbete som beskrivs av en nod kallas för en aktivitet och kan distribueras över flera noder i klustret.

### <a name="understanding-the-tez-view"></a>Förstå Tez-vyn

Tez-vyn innehåller både historisk information och information om processer som körs. Den här informationen visar hur ett jobb är fördelad över kluster. Den visar även räknare som används av uppgifter och formhörnen och information om fel relaterade till projektet. Den kan erbjuda användbar information i följande scenarier:

* Övervakning tidskrävande processer, visa förloppet för kartan och minska uppgifter.
* Analysera historiska data för lyckade eller misslyckade processer att lära dig hur bearbetning kan förbättras eller orsaken till felet.

## <a name="generate-a-dag"></a>Generera en DAG

Tez-vyn innehåller bara data om ett jobb som använder Tez-motorn körs eller har körts tidigare. Enkel Hive-frågor kan lösas utan att använda Tez. Mer komplexa frågor som vill filtrering, gruppering, sortering, kopplingar och så vidare. Använd Tez-motorn.

Använd följande steg för att köra en Hive-fråga som använder Tez:

1. I en webbläsare, navigerar du till https://CLUSTERNAME.azurehdinsight.net, där **KLUSTERNAMN** är namnet på ditt HDInsight-kluster.

2. På menyn överst på sidan, Välj den **vyer** ikon. Den här ikonen som ser ut som en serie rutor. Välj i listrutan som visas, **Hive visa**.

    ![Du har markerat Hive-vy](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. När Hive-vyn läses in, klistra in följande fråga i frågeredigeraren och klicka sedan på **köra**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    När jobbet har slutförts, du bör se utdata som visas i den **Frågeprocessresultat** avsnitt. Resultatet bör likna följande:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Välj den **loggen** fliken. Du kan se information som liknar följande:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Spara den **App-id** värdet som det här värdet används i nästa avsnitt.

## <a name="use-the-tez-view"></a>Använd vyn Tez

1. På menyn överst på sidan, Välj den **vyer** ikon. Välj i listrutan som visas, **Tez visa**.

    ![Du har markerat Tez vy](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. När Tez vyn läses in, visas en lista över hive-frågor som körs eller har körts på klustret.

    ![Alla dag](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Om du har en enda post, gäller den fråga som du körde i föregående avsnitt. Om du har flera poster kan söka du med hjälp av fält längst upp på sidan.

4. Välj den **fråge-ID** för en Hive-fråga. Information om frågan visas.

    ![DAG-information](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Flikar på den här sidan kan du visa följande information:

    * **Fråga information**: information om Hive-fråga.
    * **Tidslinjen**: Information om hur lång tid tog av varje steg i processen.
    * **Konfigurationer**: den konfiguration som använts för den här frågan.

    Från __Frågedetaljer__ du kan använda länkarna för att hitta information om den __programmet__ eller __DAG__ för den här frågan.
    
    * Den __programmet__ länken visar information om YARN-programmet för den här frågan. Du kan komma åt programloggarna YARN härifrån.
    * Den __DAG__ länken visar information om riktat acykliskt diagram för den här frågan. Härifrån kan du visa en grafisk representation av gruppen för Databastillgänglighet. Du kan också hitta information om formhörnen i gruppen för Databastillgänglighet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder vyn Tez, lär du dig mer om [med hjälp av Hive i HDInsight](hadoop/hdinsight-use-hive.md).

Mer teknisk information om Tez finns i [Tez sidan vid Hortonworks](http://hortonworks.com/hadoop/tez/).

Mer information om hur du använder Ambari med HDInsight finns [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
