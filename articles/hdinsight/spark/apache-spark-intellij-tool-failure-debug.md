---
title: Felsök Spark-jobb med IntelliJ Azure Toolkit (förhandsversion) - HDInsight
description: Vägledning med HDInsight Tools i Azure Toolkit för IntelliJ för att felsöka program
keywords: debug distans intellij, fjärrfelsökning intellij, ssh, intellij, hdinsight, debug intellij, felsökning
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494596"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Fel spark jobb felsökning med Azure Toolkit för IntelliJ (förhandsvisning)

Den här artikeln innehåller steg-för-steg-vägledning om hur du använder HDInsight-verktyg i [Azure Toolkit för IntelliJ för](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) att köra **Spark Failure Debug-program.**

## <a name="prerequisites"></a>Krav

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Den här kursen använder Java version 8.0.202.
  
* Intellij IDÉ. I den här artikeln används [IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Se [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Anslut till ditt HDInsight-kluster. Se [Anslut till ditt HDInsight-kluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Se [Hämta Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Skapa ett projekt med felsökningsmall

Skapa ett spark2.3.2-projekt för att fortsätta felsöka, ta fel felsöka exempelfilen i det här dokumentet.

1. Öppna IntelliJ IDEA. Öppna fönstret **Nytt projekt.**

   a. Välj **Azure Spark/HDInsight** i den vänstra rutan.

   b. Välj **Spark Project med fel felsökning av aktivitet(Förhandsgranskning)(Scala)** i huvudfönstret.

     ![Intellij Skapa ett felsökningsprojekt](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Välj **Nästa**.

2. Gör följande i fönstret **Nytt projekt:**

   ![Intellij New Project välj Spark-version](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Ange ett projektnamn och en projektplats.

   b. I listrutan **Projekt SDK** väljer du **Java 1.8** för **Spark 2.3.2-kluster.**

   c. I listrutan **Spark Version** väljer du **Spark 2.3.2(Scala 2.11.8)**.

   d. Välj **Slutför**.

3. Välj **src** > **main** > **scala** för att öppna koden i projektet. I det här exemplet används skriptet **AgeMean_Div().**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Köra ett Spark Scala/Java-program på ett HDInsight-kluster

Skapa ett spark Scala/Java-program och kör sedan programmet på ett Spark-kluster genom att göra följande steg:

1. Klicka på **Lägg till konfiguration** för att öppna fönstret **Kör/felsöka konfigurationer.**

   ![HDI Intellij Lägg till konfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Välj **Run/Debug Configurations** plustecknet (**+**). Välj sedan alternativet **Apache Spark på HDInsight.**

   ![Intellij Lägg till ny konfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Växla till **Fjärrkörning på** fliken Kluster. Ange information för **namn,** **sparkkluster**och **huvudklassnamn**. Våra verktyg stöder felsökning med **Executors.** **NumExectors**, standardvärdet är 5 och du är bättre att inte ange högre än 3. Om du vill minska körtiden kan du lägga till **spark.yarn.maxAppAttempts** i **jobbkonfigurationer** och ange värdet till 1. Klicka på **OK** för att spara konfigurationen.

   ![Intellij Kör felsökningskonfigurationer nya](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfigurationen sparas nu med det namn du angav. Om du vill visa konfigurationsinformationen väljer du konfigurationsnamnet. Om du vill göra ändringar väljer du **Redigera konfigurationer**.

5. När du har slutfört konfigurationsinställningarna kan du köra projektet mot fjärrklustret.

   ![Knappen Intellij Debug Remote Spark Job Remote run button](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Du kan kontrollera program-ID:t från utdatafönstret.

   ![Intellij Debug Remote Spark Job Remote kör resultat](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Hämtning av misslyckade jobbprofiler

Om jobbinlämningen misslyckas kan du hämta den misslyckade jobbprofilen till den lokala datorn för ytterligare felsökning.

1. Öppna **Microsoft Azure Storage Explorer**, leta upp HDInsight-kontot för klustret för det misslyckade jobbet, hämta de misslyckade jobbresurserna från motsvarande plats: **\hdp\spark2-events\\.spark-failures\\\<application ID>** till en lokal mapp. **Aktivitetsfönstret** visar hämtningsframsteget.

   ![Hämtningsfel i Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Hämtningen av Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurera lokal felsökningsmiljö och felsöka vid fel

1. Öppna det ursprungliga projektet eller skapa ett nytt projekt och associera det med den ursprungliga källkoden. Endast spark2.3.2-version stöds för felfelsökning för närvarande.

1. Skapa en Spark Failure **Debug-config-fil** i IntelliJ IDEA och välj FTD-filen från de tidigare nedladdade misslyckade jobbresurserna för **platsfältet Spark Job Failure Context.**

   ![kreakursfelkonfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klicka på den lokala körknappen i verktygsfältet, visas felet i Fönstret Kör.

   ![kör-fel-konfiguration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![kör-fel-konfiguration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Ställ brytpunkt som loggen anger, klicka sedan på lokal felsökningsknapp för att göra lokal felsökning precis som dina vanliga Scala / Java-projekt i IntelliJ.

1. Efter felsökning, om projektet har slutförts, kan du skicka det misslyckade jobbet till din gnista på HDInsight-klustret.

## <a name="next-steps"></a><a name="seealso"></a>Nästa steg

* [Översikt: Felsökning av Apache Spark-program](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Skapa Scala-projekt (video): [Skapa Apache Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsökning (video): [Använd Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program i ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Gör interaktiv dataanalys med spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperaturen med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd HDInsight Tools för IntelliJ med Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använd HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
