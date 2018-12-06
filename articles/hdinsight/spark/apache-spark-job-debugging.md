---
title: Felsöka Apache Spark-jobb som körs på Azure HDInsight
description: Använd YARN-Användargränssnittet, Spark-Användargränssnittet och Spark-historikserver att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: 5e00c52c17eac92edc3273e2d765d6c5fd76f59b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970695"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark-jobb som körs på Azure HDInsight

I den här artikeln får du lära att spåra och felsöka [Apache Spark](https://spark.apache.org/) HDInsight-jobb som körs på kluster med den [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Användargränssnittet, Spark-Användargränssnittet och Spark-Historikserver. Du startar ett Spark-jobb med hjälp av en bärbar dator som är tillgängliga med Spark-kluster **Machine learning: förutsägande analys mat inspektion data med hjälp av MLLib**. Du kan använda följande steg för att spåra ett program som du skickade med hjälp av någon annan metod, till exempel **spark-submit**.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Du bör har börjat köra anteckningsboken  **[Machine learning: förutsägande analys mat inspektion data med hjälp av MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Följ länken för mer information om hur du kör den här anteckningsboken finns.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i YARN-Användargränssnittet
1. Starta Användargränssnittet för YARN. Klicka på **Yarn** under **Klusterinstrumentpaneler**.
   
    ![Starta Användargränssnittet för YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Alternativt kan du också starta YARN-Användargränssnittet från Ambari UI. Om du vill starta Ambari UI, klickar du på **Ambari home** under **Klusterinstrumentpaneler**. Ambari UI, klickar du på **YARN**, klickar du på **snabblänkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager UI**.    
   > 
   > 
2. Eftersom du påbörjat Spark-jobb med Jupyter-anteckningsböcker programmet har namnet **remotesparkmagics** (detta är namnet för alla program som startas från de bärbara datorerna). Klicka på det program-ID mot namnet på programmet vill ha mer information om jobbet. Detta startar programmet vyn.
   
    ![Hitta Spark-program-ID](./media/apache-spark-job-debugging/find-application-id.png)
   
    Typen av program som startas från Jupyter notebooks, statusen är alltid **kör** tills du avslutar anteckningsboken.
3. Från program-vyn kan granska du nedåt ytterligare till ta reda på de behållare som är associerade med programmet och loggar (stdout/stderr). Du kan också starta Spark-Användargränssnittet genom att klicka på det länkande som motsvarar den **spårnings-URL: en**, enligt nedan. 
   
    ![Hämta behållarloggar](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-Användargränssnittet
I Användargränssnittet för Spark kan du granska nedåt i Spark-jobb som skapade av programmet som du tidigare har startat.

1. Om du vill starta Användargränssnittet för Spark, från vyn program klickar du på länken mot den **spårnings-URL: en**, enligt den här skärmbilden ovan. Du kan se alla Spark-jobb som startas av programmet som körs i Jupyter-anteckningsboken.
   
    ![Visa Spark-jobb](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Klicka på den **Executors** fliken för att se information om bearbetning och lagring för varje executor. Du kan också hämta anropsstacken genom att klicka på den **tråd dumpa** länk.
   
    ![Visa Spark executors](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klicka på den **faser** fliken för att se de steg som är associerade med programmet.
   
    ![Visa Spark faser](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Varje steg kan ha flera aktiviteter som du kan visa körning statistik, som visas nedan.
   
    ![Visa Spark faser](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Du kan starta DAG visualisering från informationssidan steg. Expandera den **DAG visualisering** länkar överst på sidan som visas nedan.
   
    ![Visa Spark faser DAG visualisering](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG eller direkt Aclyic graf representerar olika faser i programmet. Varje blå ruta i diagrammet representerar en Spark-åtgärd som anropas från programmet.
5. Du kan också starta tidslinjevyn program från informationssidan steg. Expandera den **händelse tidslinje** länkar överst på sidan som visas nedan.
   
    ![Visa Spark faser händelse tidslinje](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Då visas Spark-händelser i form av en tidslinje. Tidslinjevyn är tillgängligt på tre nivåer mellan jobb gäller i ett jobb och inom ett steg. Bilden ovan visar tidslinjevyn för ett visst stadium.
   
   > [!TIP]
   > Om du väljer den **aktivera Zooma** kryssruta, du kan bläddra till vänster och höger i tidslinjevyn.
   > 
   > 
6. Andra flikar i Spark-Användargränssnittet innehåller användbar information om Spark-instansen.
   
   * Fliken lagring – om programmet skapar en Rdd du hittar information om de på fliken lagring.
   * Fliken miljö – den här fliken innehåller mycket användbar information om Spark-instans som den 
     * Scala-version
     * Händelseloggen directory kopplat till klustret
     * Antal kärnor executor för programmet
     * O.s.v.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med hjälp av Spark-Historikserver
När ett jobb har slutförts sparas information om jobbet i Spark-Historikserver.

1. Om du vill starta Spark-Historikserver, från bladet översikt klickar du på **Spark-historikserver** under **Klusterinstrumentpaneler**.
   
    ![Starta Spark-Historikserver](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Alternativt kan du också starta Användargränssnittet för Spark historik från Ambari UI. Om du vill starta Ambari UI, från bladet översikt klickar du på **Ambari home** under **Klusterinstrumentpaneler**. Ambari UI, klickar du på **Spark**, klickar du på **snabblänkar**, och klicka sedan på **Spark historik Server UI**.
   > 
   > 
2. Du ser alla slutförda program visas. Klicka på ett program-ID för att granska nedåt i ett program för mer information.
   
    ![Starta Spark-Historikserver](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Se också
*  [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
*  [Felsöka Apache Spark-jobb med hjälp av utökade Spark-Historikserver](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Apache Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetri dataanalys med hjälp av Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe på Azure HDInsight Spark för distribuerade djupinlärning](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-Plugin för IntelliJ IDEA till att felsöka Apache Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
