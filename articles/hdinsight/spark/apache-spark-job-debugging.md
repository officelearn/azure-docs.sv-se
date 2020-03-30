---
title: Felsöka Apache Spark-jobb som körs i Azure HDInsight
description: Använd YARN UI-, Spark UI- och Spark History-server för att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932142"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark-jobb som körs i Azure HDInsight

I den här artikeln får du lära dig hur du spårar och felsöker [Apache Spark-jobb](https://spark.apache.org/) som körs på HDInsight-kluster med hjälp av [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI, Spark UI och Spark History Server. Du startar ett Spark-jobb med hjälp av en bärbar dator som är tillgänglig med Spark-klustret, **Machine learning: Predictive analysis on food inspection data using MLLib**. Du kan använda följande steg för att spåra ett program som du har skickat in med någon annan metod, till exempel **spark-submit**.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Du borde ha börjat köra anteckningsboken, **[Maskininlärning: Prediktiv analys av livsmedelsinspektionsdata med MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Instruktioner om hur du kör den här anteckningsboken följer du länken.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i YARN-användargränssnittet

1. Starta YARN UI. Välj **Garn** under **klusterinstrumentpaneler**.

    ![Azure portal starta YARN UI](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternativt kan du också starta YARN UI från Ambari UI. Om du vill starta användargränssnittet i Ambari väljer du **Ambari-hem** under **Klusterinstrumentpaneler**. Från Ambari UI navigerar du till **YARN** > **Snabblänkar** > det aktiva resurshanteraren > **Resurshanterarens användargränssnitt**.

2. Eftersom du startade Spark-jobbet med Jupyter-anteckningsböcker har programmet namnet **remotesparkmagics** (det här är namnet på alla program som startas från anteckningsböckerna). Välj program-ID mot programnamnet för att få mer information om jobbet. Detta startar programvyn.

    ![Spark-historikserver Hitta Spark-program-ID](./media/apache-spark-job-debugging/find-application-id1.png)

    För sådana program som startas från Jupyter-anteckningsböckerna **körs** statusen alltid tills du avslutar anteckningsboken.

3. Från programvyn kan du öka detaljnivån ytterligare för att ta reda på behållarna som är associerade med programmet och loggarna (stdout/stderr). Du kan också starta spark-användargränssnittet genom att klicka på länken som motsvarar **spårnings-URL:en**, som visas nedan.

    ![Spark historik server hämta behållarloggar](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-användargränssnittet

I Spark UI kan du öka detaljnivån i Spark-jobben som skapas av programmet du startade tidigare.

1. Om du vill starta spark-användargränssnittet väljer du länken mot **spårnings-URL:en**från programvyn, som visas i skärmbilden ovan. Du kan se alla Spark-jobb som startas av programmet som körs i jupyter-anteckningsboken.

    ![Fliken Spark-historikserverjobb](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Välj fliken **Utförare** om du vill visa bearbetnings- och lagringsinformation för varje utförare. Du kan också hämta anropsstacken genom att välja länken **Tråddump.**

    ![Fliken Spark-historikserverutstratorer](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Välj fliken **Faser** om du vill visa de faser som är associerade med programmet.

    ![Fliken Sparkhistorikserver](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Visa miniatyrsteg")

    Varje steg kan ha flera uppgifter som du kan visa körningsstatistik för, som visas nedan.

    ![Information om fliken Sparkhistorikserver](./media/apache-spark-job-debugging/view-spark-stages-details.png "Visa information om miniatyrscener")

4. Från sidan sceninformation kan du starta DAG Visualisering. Expandera länken **DAG-visualisering** högst upp på sidan, som visas nedan.

    ![Visa miniatyrfaser DAG-visualisering](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG eller Direct Aclyic Graph representerar de olika stegen i applikationen. Varje blå ruta i diagrammet representerar en Spark-åtgärd som anropas från programmet.

5. Från sidan sceninformation kan du också starta programtidslinjevyn. Expandera länken **Händelsetidslinje** högst upp på sidan, som visas nedan.

    ![Visa händelsetidslinje för Spark-faser](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Detta visar Spark-händelserna i form av en tidslinje. Tidslinjevyn är tillgänglig på tre nivåer, mellan jobb, jobb och inom ett stadium. Bilden ovan tar tidslinjevyn för ett visst steg.

   > [!TIP]  
   > Om du markerar kryssrutan **Aktivera zoomning** kan du rulla åt vänster och höger över tidslinjevyn.

6. Andra flikar i Spark UI ger användbar information om Spark-instansen också.

   * Fliken Lagring - Om programmet skapar en rdd-data kan du hitta information om dem på fliken Lagring.
   * Fliken Miljö – Den här fliken innehåller användbar information om Din Spark-instans, till exempel:
     * Scala-version
     * Händelseloggkatalog som är associerad med klustret
     * Antal executorkärnor för programmet
     * O.s.v.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med Spark History Server

När ett jobb har slutförts sparas informationen om jobbet i Spark History Server.

1. Om du vill starta Spark History Server väljer du **Spark-historikservern** på sidan **Översikt** under **Klusterinstrumentpaneler**.

    ![Azure portal starta Spark historik server](./media/apache-spark-job-debugging/launch-spark-history-server.png "Starta Spark-historikserver1")

   > [!TIP]  
   > Alternativt kan du också starta Spark History Server UI från Ambari UI. Om du vill starta Ambari-användargränssnittet väljer du **Ambari-hemmet** under **Klusterinstrumentpaneler**från bladet Översikt . Från Ambari UI navigerar du till **Spark2** > **Quick Links** > **Spark2 History Server UI**.

2. Du ser alla slutförda program som anges. Välj ett program-ID om du vill öka detaljnivån i ett program för mer information.

    ![Spark historik server avslutade program](./media/apache-spark-job-debugging/view-completed-applications.png "Starta Spark-historikserver2")

## <a name="see-also"></a>Se även

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Felsöka Apache Spark-jobb med utökad Spark-historikserver](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program Insight telemetri dataanalys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
