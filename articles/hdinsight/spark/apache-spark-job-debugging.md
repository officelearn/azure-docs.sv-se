---
title: Felsöka Apache Spark-jobb som körs på Azure HDInsight | Microsoft Docs
description: Använda YARN-Användargränssnittet, Spark UI och Spark historik server att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 1ddf8cd38b4d62dc206a9f27e0620f8c7b232ec3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519390"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark-jobb som körs på Azure HDInsight

I den här artikeln får lära du att spåra och felsöka Spark-jobb som körs på HDInsight-kluster med hjälp av YARN-Användargränssnittet, Spark användargränssnitt och Spark historik Server. Du startar en Spark-jobb med hjälp av en bärbar dator som är tillgängliga med Spark-kluster **Maskininlärning: förutsägbar analys på mat inspektion data med hjälp av MLLib**. Du kan använda följande steg för att spåra ett program som du skickas med någon annan metod, till exempel **spark-skicka**.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Du bör den bärbara datorn har startats  **[Maskininlärning: förutsägbar analys på mat inspektion data med hjälp av MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Instruktioner om hur du kör den här anteckningsboken på länken.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i YARN-Användargränssnittet
1. Starta YARN-Användargränssnittet. Klicka på **Klusterinstrumentpanel**, och klicka sedan på **YARN**.
   
    ![Starta YARN-Användargränssnittet](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Alternativt kan du starta YARN-Användargränssnittet från Ambari UI. Om du vill starta Ambari UI, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **instrumentpanelen för HDInsight-klustret**. Ambari UI, klicka på **YARN**, klickar du på **snabblänkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager UI**.    
   > 
   > 
2. Eftersom du startade jobbet Spark med Jupyter-anteckningsböcker programmet har namnet **remotesparkmagics** (detta är namnet för alla program som startas från de bärbara datorerna). Klicka på det program-ID mot namnet på programmet för mer information om jobbet. Detta startar programmet vyn.
   
    ![Hitta Spark-program-ID](./media/apache-spark-job-debugging/find-application-id.png)
   
    För sådana program som startas från Jupyter-anteckningsböcker status är alltid **kör** tills du avslutar den bärbara datorn.
3. Från vyn programmet, kan du detaljnivån ta reda på de behållare som är associerade med programmet och loggfiler (stdout/stderr). Du kan också starta Användargränssnittet för Spark genom att klicka på det länkade som motsvarar den **spårning URL**, enligt nedan. 
   
    ![Hämta loggar för behållaren](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-Gränssnittet
I Spark-Användargränssnittet, kan du gå till Spark-jobb som skapas av programmet som du tidigare har startats.

1. Om du vill starta Användargränssnittet för Spark från vyn programmet, klickar du på länken mot den **spårning URL**som visas i den här skärmbilden ovan. Du kan se alla Spark-jobb som startats av det program som körs i Jupyter-anteckningsboken.
   
    ![Visa Spark jobb](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Klicka på den **Executors** fliken för att se information om bearbetning och lagring för varje utförare. Du kan också hämta anropsstacken genom att klicka på den **tråd Dump** länk.
   
    ![Visa Spark executors](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klicka på den **steg** fliken för att se de steg som är associerade med programmet.
   
    ![Visa Spark faser](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Varje steg kan ha flera uppgifter som du kan visa statistik för körning, som visas nedan.
   
    ![Visa Spark faser](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Från sidan steget information kan du starta DAG visualiseringen. Expandera den **DAG visualiseringen** länken längst upp på sidan som visas nedan.
   
    ![Visa Spark steg DAG visualiseringen](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG eller direkt Aclyic diagrammet representerar de olika stegen i programmet. Varje ruta blått i diagrammet representerar en Spark-åtgärd som anropas från programmet.
5. Du kan starta programmet tidslinjevyn från sidan steget information. Expandera den **händelse tidslinjen** länken längst upp på sidan som visas nedan.
   
    ![Visa Spark steg händelse tidslinjen](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Detta visar Spark-händelser i form av en tidslinje. Tidslinjevyn är tillgänglig på tre nivåer över jobb inom ett jobb, och ett steg. Bilden ovan avbildar tidslinjevyn för ett visst stadium.
   
   > [!TIP]
   > Om du väljer den **aktivera** kryssrutan, du kan rulla åt vänster och höger över tidslinjevyn.
   > 
   > 
6. Andra flikar i Spark-Användargränssnittet innehåller användbar information om Spark-instans.
   
   * Fliken lagring – om programmet skapar en RDDs hittar du information om de på fliken lagring.
   * Fliken miljö - den här fliken ger mycket användbar information om din Spark-instans som den 
     * Scala version
     * Händelseloggen directory associeras med klustret
     * Antal kärnor utförare för programmet
     * O.s.v.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med hjälp av Spark historik Server
När ett jobb har slutförts sparas information om jobbet i Spark historik Server.

1. Om du vill starta Spark historik Server från klustret-bladet klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Spark historik Server**.
   
    ![Starta Spark historik Server](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Alternativt kan du starta Spark historik Server UI från Ambari UI. Om du vill starta Ambari UI, kluster-bladet klickar du på **Klusterinstrumentpanel**, och klicka sedan på **instrumentpanelen för HDInsight-klustret**. Ambari UI, klicka på **Spark**, klickar du på **snabblänkar**, och klicka sedan på **Spark historik Server UI**.
   > 
   > 
2. Du kan se alla slutförda programmen. Klicka på ett program-ID och öka detaljnivån till ett program för mer information.
   
    ![Starta Spark historik Server](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Se också
*  [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetridataanalys i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe för distribuerade djup learning på Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)


