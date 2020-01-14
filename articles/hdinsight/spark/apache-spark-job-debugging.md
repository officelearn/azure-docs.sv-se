---
title: Felsöka Apache Spark jobb som körs på Azure HDInsight
description: Använd garn gränssnitt, Spark UI och Spark historik Server för att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932142"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark jobb som körs på Azure HDInsight

I den här artikeln får du lära dig att spåra och felsöka [Apache Spark](https://spark.apache.org/) jobb som körs på HDInsight-kluster med hjälp av [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) gränssnittet, Spark-användargränssnittet och Spark-historiken. Du startar ett Spark-jobb med en bärbar dator som är tillgänglig med Spark-klustret, **Machine Learning: förutsägande analys av livsmedels inspektions data med MLLib**. Du kan använda följande steg för att spåra ett program som du har skickat med någon annan metod, till exempel **Spark-Submit**.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Du bör ha börjat köra antecknings boken, **[Machine Learning: förutsägande analys av livsmedels inspektions data med MLLib](apache-spark-machine-learning-mllib-ipython.md)** . Följ länken om du vill ha mer information om hur du kör den här antecknings boken.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i garn gränssnittet

1. Starta garn gränssnittet. Välj **garn** under **kluster instrument paneler**.

    ![Azure Portal starta garn gränssnittet](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Du kan också starta garn gränssnittet från Ambari-ANVÄNDARGRÄNSSNITTET. Starta Ambari-ANVÄNDARGRÄNSSNITTET genom att välja **Ambari Home** under **kluster instrument paneler**. Från Ambari-ANVÄNDARGRÄNSSNITTET navigerar du till **garn** > **snabb länkar** > active Resource Manager-> **Resource Manager-användargränssnittet**.

2. Eftersom du startade Spark-jobbet med Jupyter-anteckningsböcker har programmet namnet **remotesparkmagics** (detta är namnet på alla program som startas från antecknings böckerna). Välj program-ID: t mot program namnet för att få mer information om jobbet. Detta startar programvisningen.

    ![Spark-historik Server hitta Spark-programid](./media/apache-spark-job-debugging/find-application-id1.png)

    För sådana program som startas från Jupyter Notebooks **körs** alltid statusen tills du avslutar antecknings boken.

3. Från programvyn kan du öka detalj nivån ytterligare för att ta reda på de behållare som är kopplade till programmet och loggarna (STDOUT/STDERR). Du kan också starta Spark-ANVÄNDARGRÄNSSNITTET genom att klicka på länken som motsvarar **spårnings-URL: en**, som visas nedan.

    ![Spark historik Server Hämta behållar loggar](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-ANVÄNDARGRÄNSSNITTET

I Spark-ANVÄNDARGRÄNSSNITTET kan du öka detalj nivån i Spark-jobben som har skapats av det program som du startade tidigare.

1. Starta Spark-ANVÄNDARGRÄNSSNITTET från vyn program genom att välja länken mot **spårnings-URL: en**, som visas i skärmdumpen ovan. Du kan se alla Spark-jobb som startas av programmet som körs i Jupyter Notebook.

    ![Fliken jobb i Spark historik Server](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Välj fliken **körningar** om du vill se bearbetnings-och lagrings information för varje utförar. Du kan också hämta anrops stacken genom att välja länken **tråd dum par** .

    ![Fliken körningar av Spark historik Server](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Välj fliken **steg** för att se de steg som är kopplade till programmet.

    ![Fliken Server faser i Spark-historik](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Visa Spark-faser")

    Varje steg kan ha flera aktiviteter som du kan använda för att Visa körnings statistik, som du ser nedan.

    ![Fliken Detaljer för Server etapper för Spark-historik](./media/apache-spark-job-debugging/view-spark-stages-details.png "Visa information om Spark-faser")

4. På sidan information om scenen kan du starta DAG visualisering. Expandera länken **dag visualisering** överst på sidan, som du ser nedan.

    ![Visa DAG visualisering för Spark-faser](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG eller Direct Aclyic Graph representerar de olika stegen i programmet. Varje blå ruta i diagrammet representerar en spark-åtgärd som anropas från programmet.

5. På sidan information om scenen kan du också starta vyn tids linje för program. Expandera länken för **händelsens tids linje** överst på sidan, som du ser nedan.

    ![Visa händelse tids linje för Spark-faser](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Då visas Spark-händelserna i form av en tids linje. Vyn tids linje är tillgänglig på tre nivåer, mellan jobb, inom ett jobb och inom ett steg. Bilden ovan fångar vyn tids linje för ett angivet Stadium.

   > [!TIP]  
   > Om du markerar kryss rutan **Aktivera zoomning** kan du rulla åt vänster och höger i vyn tids linje.

6. Andra flikar i Spark-ANVÄNDARGRÄNSSNITTET ger också värdefull information om Spark-instansen.

   * Fliken lagring – om ditt program skapar en RDD kan du hitta information om dem på fliken lagring.
   * Fliken miljö – den här fliken innehåller användbar information om din spark-instans, till exempel:
     * Scala-version
     * Händelse logg katalog som är kopplad till klustret
     * Antal utförar-kärnor för programmet
     * O.s.v.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med Spark historik Server

När ett jobb har slutförts sparas informationen om jobbet på Spark-historik servern.

1. Om du vill starta Spark-historik servern väljer du **Spark historik Server** under **kluster instrument paneler**på sidan **Översikt** .

    ![Azure Portal starta Spark-historik Server](./media/apache-spark-job-debugging/launch-spark-history-server.png "Starta Spark-historik server1")

   > [!TIP]  
   > Du kan också starta Spark-historik serverns användar gränssnitt från Ambari-ANVÄNDARGRÄNSSNITTET. Om du vill starta Ambari-ANVÄNDARGRÄNSSNITTET väljer du **Ambari start** under **kluster instrument paneler**på bladet översikt. I Ambari-ANVÄNDARGRÄNSSNITTET navigerar du till **Spark2** > **snabb länkar** > **Spark2 historik serverns användar gränssnitt**.

2. Alla slutförda program visas. Välj ett program-ID för att öka detalj nivån i ett program för mer information.

    ![Spark-historik Server slutförde program](./media/apache-spark-job-debugging/view-completed-applications.png "Starta Spark-historik server2")

## <a name="see-also"></a>Se också

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Felsöka Apache Spark jobb med utökad Spark-historik Server](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>För data analyser

* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program insiktering telemetri data analys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
