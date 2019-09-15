---
title: Felsöka Apache Spark jobb som körs på Azure HDInsight
description: Använd garn gränssnitt, Spark UI och Spark historik Server för att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: c71bf9be94799c2b109d57393e70e99a0710e252
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995525"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark jobb som körs på Azure HDInsight

I den här artikeln får du lära dig att spåra och felsöka [Apache Spark](https://spark.apache.org/) jobb som körs på HDInsight-kluster med hjälp av [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) gränssnittet, Spark-användargränssnittet och Spark-historiken. Du startar ett Spark-jobb med en bärbar dator som är tillgänglig med **Spark-klustret, Machine Learning: Förutsägelse analys av livsmedels inspektions data med MLLib**. Du kan använda följande steg för att spåra ett program som du har skickat med någon annan metod, till exempel **Spark-Submit**.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Du bör ha börjat köra antecknings **boken, [Machine Learning: Förutsägelse analys av livsmedels inspektions data med MLLib](apache-spark-machine-learning-mllib-ipython.md).** Följ länken om du vill ha mer information om hur du kör den här antecknings boken.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i garn gränssnittet
1. Starta garn gränssnittet. Klicka på **garn** under **kluster instrument paneler**.
   
    ![Starta garn gränssnitt](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)
   
   > [!TIP]  
   > Du kan också starta garn gränssnittet från Ambari-ANVÄNDARGRÄNSSNITTET. Starta Ambari-ANVÄNDARGRÄNSSNITTET genom att klicka på **Ambari start** under **kluster instrument paneler**. Klicka på **garn**i AMBARI-användargränssnittet, klicka på **snabb länkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager-användargränssnitt**. 

2. Eftersom du startade Spark-jobbet med Jupyter-anteckningsböcker har programmet namnet **remotesparkmagics** (detta är namnet på alla program som startas från antecknings böckerna). Klicka på program-ID: t mot program namnet om du vill ha mer information om jobbet. Detta startar programvisningen.
   
    ![Hitta Spark-programmets ID](./media/apache-spark-job-debugging/find-application-id1.png)
   
    För sådana program som startas från Jupyter Notebooks **körs** alltid statusen tills du avslutar antecknings boken.
3. Från programvyn kan du öka detalj nivån ytterligare för att ta reda på de behållare som är kopplade till programmet och loggarna (STDOUT/STDERR). Du kan också starta Spark-ANVÄNDARGRÄNSSNITTET genom att klicka på länken som motsvarar **spårnings-URL: en**, som visas nedan. 
   
    ![Hämta containgerloggar](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-ANVÄNDARGRÄNSSNITTET
I Spark-ANVÄNDARGRÄNSSNITTET kan du öka detalj nivån i Spark-jobben som har skapats av det program som du startade tidigare.

1. Starta Spark-ANVÄNDARGRÄNSSNITTET från vyn program genom att klicka på länken mot **spårnings-URL: en**, som visas i skärmdumpen ovan. Du kan se alla Spark-jobb som startas av programmet som körs i Jupyter Notebook.
   
    ![Visa Spark-jobb](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)
2. Klicka på fliken **körningar** om du vill se bearbetnings-och lagrings information för varje utförar. Du kan också hämta anrops stacken genom att klicka på länken **tråd dum par** .
   
    ![Visa Spark-körningar](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klicka på fliken **steg** för att se de steg som är kopplade till programmet.
   
    ![Visa Spark-faser](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Visa Spark-faser")
   
    Varje steg kan ha flera aktiviteter som du kan använda för att Visa körnings statistik, som du ser nedan.
   
    ![Visa information om Spark-faser](./media/apache-spark-job-debugging/view-spark-stages-details.png "Visa information om Spark-faser") 
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
   * Fliken miljö – den här fliken ger mycket värdefull information om din spark-instans, till exempel 
     * Scala-version
     * Händelse logg katalog som är kopplad till klustret
     * Antal utförar-kärnor för programmet
     * O.s.v.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med Spark historik Server
När ett jobb har slutförts sparas informationen om jobbet på Spark-historik servern.

1. Starta Spark-historik servern genom att klicka på **Spark historik Server** under **kluster instrument paneler**på översikts bladet.
   
    ![Starta Spark-historik server1](./media/apache-spark-job-debugging/launch-spark-history-server.png "Starta Spark-historik server1")
   
   > [!TIP]  
   > Du kan också starta Spark-historik serverns användar gränssnitt från Ambari-ANVÄNDARGRÄNSSNITTET. Starta Ambari-ANVÄNDARGRÄNSSNITTET från översikts bladet genom att klicka på **Ambari start** under **kluster instrument paneler**. Klicka på **Spark**från AMBARI-användargränssnittet, klicka på **snabb länkar**och klicka sedan på **Server gränssnitt för Spark-historik**.

2. Alla slutförda program visas. Klicka på ett program-ID för att öka detalj nivån i ett program för mer information.
   
    ![Starta Spark-historik Server2](./media/apache-spark-job-debugging/view-completed-applications.png "Starta Spark-historik Server2")

## <a name="see-also"></a>Se också
*  [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
*  [Felsöka Apache Spark jobb med utökad Spark-historik Server](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>För data analyser

* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program insiktering telemetri data analys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe på Azure HDInsight Spark för distribuerad djup inlärning](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
