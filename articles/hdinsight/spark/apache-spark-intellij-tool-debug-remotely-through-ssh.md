---
title: 'Azure Toolkit för IntelliJ: Felsöka Spark-program via fjärranslutning via SSH '
description: Stegvisa anvisningar för hur du använder HDInsight-verktygen i Azure Toolkit för IntelliJ till att felsöka program via en fjärranslutning på HDInsight-kluster via SSH
keywords: fjärrfelsök intellij, fjärrfelsökning intellij, ssh, intellij, hdinsight, felsöka intellij, felsökning
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: ef2507a15579ea3d145bfe37df281e2c044d181c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124313"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Apache Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH

Den här artikeln innehåller stegvisa anvisningar om hur du använder HDInsight Tools i [Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) kan felsöka program via fjärranslutning på ett HDInsight-kluster. Om du vill felsöka ditt projekt, du kan också visa den [felsöka HDInsight Spark-program med Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

**Förutsättningar**
* **HDInsight-verktygen i Azure Toolkit för IntelliJ**. Det här verktyget är en del av Azure Toolkit för IntelliJ. Mer information finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Och **Azure Toolkit för IntelliJ**. Använd dessa verktyg för att skapa Apache Spark-program för ett HDInsight-kluster. Mer information följer du anvisningarna i [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH-tjänsten med användarnamn och lösenord management**. Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [använda SSH-tunnel för att komma åt Ambari web UI, JobHistory, NameNode, Apache Oozie och andra web UIs](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Lär dig hur du utför lokal kör och felsökning
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenario 1: Skapa ett Scala Spark-program 

1. Starta IntelliJ IDEA och skapa sedan ett projekt. Gör följande i dialogrutan **Nytt projekt**:

   a. Välj **Azure Spark/HDInsight**. 

   b. Välj en mall för Java eller Scala baserat på dina inställningar. Välj mellan följande alternativ:

   - **Spark-projekt (Java)**

   - **Spark-projekt (Scala)**

   - **Spark-projekt med exempel (Scala)**

   - **Spark-projekt med fel uppgift Felsökningsexempel (förhandsversion) (Scala)**

     Det här exemplet används en **Spark-projekt med exempel (Scala)** mall.

   c. I listan med **byggverktyg** väljer du något av följande enligt dina behov:

   - **Maven**, för guidestöd när du skapar Scala-projekt

   - **SBT**, för att hantera beroenden när du skapar Scala-projekt 

     ![Skapa ett projekt för felsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Välj **Nästa**.     
 
1. I nästa **nytt projekt** fönstret gör du följande:

   ![Välj Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Ange ett projektnamn och projektets plats.

   b. I den **projekt SDK** listrutan, väljer **Java 1.8** för **Spark 2.x** kluster eller välj **Java 1.7** för **Spark 1.x**  kluster.

   c. I den **Spark-Version** listrutan, Scala projektguiden är rätt version för Spark-SDK och Scala SDK. Om den spark-kluster-versionen är tidigare än 2.0 väljer **Spark 1.x**. Annars väljer **Spark 2.x.** I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.

   d. Välj **Slutför**.

1. Välj **src** > **huvudsakliga** > **scala** att öppna din kod i projektet. Det här exemplet används den **SparkCore_wasbloTest** skript.

### <a name="prerequisite-for-windows"></a>Krav för Windows
När du använder den lokala Spark Scala-appen på en Windows-dator kan du få ett undantag som beskrivs i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget inträffar eftersom WinUtils.exe saknas på Windows. 

Du löser det här felet [ladda ned den körbara filen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**, och ange värdet på variabeln och **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenario 2: Utför lokal körning
1. Öppna den **SparkCore_wasbloTest** skript och högerklicka på Skriptredigeraren väljer alternativet **kör ”[Spark-jobb] XXX”** att utföra lokal körning.
1. När lokal körning har slutförts, som du kan se utdatafilen spara till din aktuella Projektutforskaren **data** > **__standard__**.

    ![Lokal körning resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Våra verktyg har standardvärdet lokala körningskonfiguration automatiskt när du utför lokal körning och lokal felsökning. Öppna konfigurationen **[Spark på HDInsight] XXX** på det övre högra hörnet ser du den **[Spark på HDInsight] XXX** redan har skapats **Apache Spark i HDInsight**. Växla till **lokalt kör** fliken.

    ![Lokala körningskonfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Miljövariabler](#prerequisite-for-windows): Om du redan har ställt in systemmiljövariabeln **HADOOP_HOME** till **C:\WinUtils**, automatiskt kan identifiera som du behöver inte lägga till manuellt.
    - [WinUtils.exe Location](#prerequisite-for-windows): Om du inte har angett systemmiljövariabeln hittar du platsen genom att klicka på knappen.
    - Välj något av två alternativ, och de behövs inte på MacOS och Linux.
1. Du kan också ange konfigurationen manuellt innan du utför lokal körning och lokal felsökning. I föregående skärmbild, klicka på plustecknet (**+**). Välj sedan den **Apache Spark i HDInsight** alternativet. Ange information för **namn**, **Main klassnamn** för att spara, och klicka sedan på knappen för lokal körning.

### <a name="scenario-3-perform-local-debugging"></a>Scenario 3: Utför lokal felsökning
1. Öppna den **SparkCore_wasbloTest** skript och ange brytpunkter.
1. Högerklicka på Skriptredigeraren och välj sedan alternativet **felsöka ' [Spark på HDInsight] XXX'** att utföra lokal felsökning.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Lär dig hur du utför remote kör och felsökning
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Utföra fjärr körningen

1. Åtkomst till den **redigera konfigurationer** menyn väljer du ikonen i det övre högra hörnet. Den här menyn kan du skapa eller redigera konfigurationer för fjärrfelsökning.

   ![Redigera konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. I den **kör/Debug konfigurationer** dialogrutan väljer du på plustecknet (**+**). Välj sedan den **Apache Spark i HDInsight** alternativet.

   ![Lägg till ny konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Växla till **fjärrköra i kluster** fliken. Ange information för **namn**, **Spark-kluster**, och **Main klassnamn**. Klicka sedan på **avancerad konfiguration (fjärrfelsökning)**. Våra verktyg stöd för felsökning med **Executors**. Den **numExectors**, standardvärdet är 5. Du skulle bättre inte ange högre än 3.

   ![Kör debug-konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. I den **avancerad konfiguration (fjärrfelsökning)** del, väljer **aktivera Spark fjärrfelsök**. Ange SSH-användarnamn och ange ett lösenord eller Använd en fil för privat nyckel. Om du vill utföra fjärrfelsök måste du konfigurera den. Det finns inget behov att ställa in det om du bara vill använda fjärransluten kör.

   ![Aktivera Spark fjärrfelsök](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurationen sparas med det namn du angett. Välj Konfigurationsnamnet om du vill visa konfigurationsinformationen. Om du vill göra ändringar, Välj **redigera konfigurationer**. 

1. När du har slutfört konfigurationsinställningarna kan du köra projektet mot fjärrklustret eller utföra fjärrfelsökning.
   
   ![Kör fjärrkontroll](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Klicka på den **Disconnect** knappen Skicka loggarna inte visas i den vänstra panelen. Det dock fortfarande körs på serverdelen.

   ![Kör fjärrkontroll](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Utför fjärrfelsökning
1. Konfigurera brytpunkterna och klicka sedan på den **fjärrfelsök** ikon. Skillnaden med remote överföring är som SSH-användarnamn/lösenord behöver konfigureras.

   ![Välj ikonen för felsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. När programkörningen når den senaste punkten, visas en **drivrutinen** fliken och två **Executor** flikarna i den **felsökare** fönstret. Välj den **återuppta programmet** ikon för att fortsätta att köra kod, som når nästa brytpunkt. Du måste växla till rätt **Executor** fliken mål-executor för felsökning. Du kan visa loggarna för jobbkörning på motsvarande **konsolen** fliken.

   ![Fliken felsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Utföra fjärrfelsökning och åtgärda fel
1. Konfigurera två brytpunkterna och välj sedan den **felsöka** ikonen för att starta fjärrprocess för felsökning.

1. Koden stoppar vid den första större punkten och parametern och Variabelinformation visas i den **variabler** fönstret. 

1. Välj den **återuppta programmet** ikon för att fortsätta. Koden stoppar på den andra punkten. Undantaget har fastnat som förväntat.

   ![Genererar fel](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Välj den **återuppta programmet** ikonen igen. Den **HDInsight Spark bidrag** visas jobbfelet ”körningen misslyckades”.

   ![Fel vid överföring](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Om du vill uppdatera dynamiskt variabelns värde med hjälp av IntelliJ felsökning funktionen, Välj **felsöka** igen. Den **variabler** fönstret visas igen. 

1. Högerklicka på målet på den **felsöka** fliken och välj sedan **ange värde**. Ange sedan ett nytt värde för variabeln. Välj sedan **RETUR** att spara värdet. 

   ![Ange värde](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Välj den **återuppta programmet** ikon för att fortsätta att köra programmet. Den här tiden kan påträffades inga undantag. Du kan se att projektet har körts utan undantag.

   ![Felsöka utan undantag](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala-projekt (video): [Skapa Apache Spark Scala-appar](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsök (video): [Felsöka Apache Spark-program via fjärranslutning på ett HDInsight-kluster med hjälp av Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Felsöka Apache Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktygen i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
