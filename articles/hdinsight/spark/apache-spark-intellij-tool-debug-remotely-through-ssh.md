---
title: "Azure Toolkit för IntelliJ: felsöka Spark-program via fjärranslutning via SSH | Microsoft Docs"
description: "Stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ för att felsöka program på HDInsight-kluster via SSH"
keywords: "Felsöka via fjärranslutning intellij, fjärrfelsökning intellij, ssh, intellij, hdinsight, felsöka intellij, felsökning"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 87cda776195dc93a35c6e978b18e823bf54c9ffb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH

Den här artikeln innehåller stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ för att felsöka program via fjärranslutning på ett HDInsight-kluster. Om du vill felsöka ditt projekt, du kan också visa den [felsöka HDInsight Spark-program med Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

**Förutsättningar**
* **HDInsight-verktyg i Azure Toolkit för IntelliJ**. Det här verktyget ingår i Azure Toolkit för IntelliJ. Mer information finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Och **Azure Toolkit för IntelliJ**. Använd dessa verktyg för att skapa Spark-program för ett HDInsight-kluster. Mer information, följ instruktionerna i [Använd Azure Toolkit för IntelliJ till att skapa Spark-program för ett HDInsight-kluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH-tjänsten med hantering av användarnamn och lösenord**. Mer information finns i [Anslut till HDInsight (Hadoop) med hjälp av SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [använda SSH tunnlar för att komma åt Ambari web UI, jobbhistorik, NameNode, Oozie och andra webb-användargränssnitt](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Lär dig hur du utför lokal kör och felsökning
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenario 1: Skapa ett Spark Scala-program 

1. Starta IntelliJ IDEA och sedan skapa ett projekt. I den **nytt projekt** dialogrutan Gör följande:

   a. Välj **HDInsight**. 

   b. Välj en mall för Java eller Scala baserat på dina inställningar. Välj mellan följande alternativ:

      - **Spark i HDInsight (Scala)**

      - **Spark i HDInsight (Java)**

      - **Spark i HDInsight exempel (Scala)**

      Det här exemplet används en **Spark på HDInsight sampel (Scala)** mall.

   c. I den **Build verktyget** väljer du något av följande enligt dina behov:

      - **Maven**, Scala skapa projekt guiden support

      -  **Segregerade Barlasttankar**, för att hantera beroenden och skapa för Scala-projekt 

      ![Skapa ett debug-projekt](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Välj **nästa**.     
 
2. I nästa **nytt projekt** fönster, gör du följande:

   ![Välj Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Ange ett projektnamn och projektets plats.

   b. I den **projekt SDK** listrutan, Välj **Java 1.8** för **Väck 2.x** kluster eller välj **Java 1.7** för **Väck 1.x**  klustret.

   c. I den **Spark Version** listrutan Scala projektguiden integrerar rätt version för Spark SDK och Scala SDK. Om den spark-kluster av versionen är äldre än 2.0 väljer **Väck 1.x**. Annars väljer **Väck 2.x.** Det här exemplet används **Spark punkt 2.0.2 (Scala 2.11.8)**.

   d. Välj **Slutför**.

3. Välj **src** > **huvudsakliga** > **scala** att öppna din kod i projektet. Det här exemplet används den **SparkCore_wasbloTest** skript.

### <a name="prerequisite-for-windows"></a>Krav för windows
När du kör programmet lokalt Spark Scala på en Windows-dator, kan du få ett undantag som beskrivs i [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget inträffar eftersom WinUtils.exe saknas i Windows. 

Du löser det här felet [ladda ned den körbara filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**, och ange värdet för variabeln för att **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenario 2: Utför lokal körning
1. Öppna den **SparkCore_wasbloTest** skript, högerklicka Skriptredigeraren och välj sedan alternativet **kör ”[Spark jobb] XXX”** att utföra lokal körning.
2. När lokal körning har slutförts, du kan se utdatafilen spara din aktuella Projektutforskaren **data** > **__standard__**.

    ![Lokal körning resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Vår verktyg har standardvärdet lokala kör konfiguration automatiskt när du utför lokalt kör och lokala felsöka. Öppna konfigurationen **[Spark jobb] XXX** i det övre högra hörnet ser du den **[Spark jobb] XXX** redan har skapats **Azure HDInsight Spark-jobbet**. Växla till **kör lokalt** fliken.

    ![Lokal kör konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Miljövariabler](#prerequisite-for-windows): Om du redan angett systemmiljövariabeln **HADOOP_HOME** till **C:\WinUtils**, automatiskt kan identifiera som du behöver inte lägga till manuellt.
    - [WinUtils.exe plats](#prerequisite-for-windows): Om du inte har angett systemmiljövariabeln hittar du platsen genom att klicka på knappen.
    - Välj något av två alternativ, och de behövs inte i MacOS och Linux.
4. Du kan också ange att konfigurationens manuellt innan du utför lokal körning och lokala debug. Klicka på plustecknet i den föregående skärmbilden (**+**). Välj sedan den **Azure HDInsight Spark-jobbet** alternativet. Ange information för **namn**, **Main klassnamn** för att spara, och klicka sedan på knappen för lokala kör.

### <a name="scenario-3-perform-local-debugging"></a>Scenario 3: Utför lokal felsökning
1. Öppna den **SparkCore_wasbloTest** skript, ange brytpunkter.
2. Högerklicka på Skriptredigeraren och välj sedan alternativet **Debug ”[Spark jobb] XXX”** att utföra lokala felsökning.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Lär dig att utföra kör och felsökning
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Utföra remote kör

1. Att få åtkomst till den **redigera konfigurationer** -menyn väljer du ikonen i det övre högra hörnet. Du kan skapa eller redigera konfigurationer för fjärrfelsökning från den här menyn.

   ![Redigera konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. I den **kör/Debug konfigurationer** dialogrutan Välj på plustecknet (**+**). Välj sedan den **Azure HDInsight Spark-jobbet** alternativet.

   ![Lägg till ny konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Växla till **fjärrköra i klustret** fliken. Ange information för **namn**, **Spark-kluster**, och **Main klassnamn**. Välj sedan **avancerad konfiguration**. Vår verktyg support debug med **Executors**. Den **numExectors**, standardvärdet är 5. Du skulle bättre inte ges ett högre värde än 3.

   ![Kör debug-konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. I den **Spark skicka avancerad konfiguration** dialogrutan **aktivera Spark remote debug**. Ange SSH-användarnamn och ange ett lösenord eller Använd en fil för privat nyckel. Välj för att spara konfigurationen **OK**. Om du vill utföra felsökning av fjärråtkomst måste du konfigurera den. Det finns inget behov av att om du vill använda Kör.

   ![Aktivera remote Spark-felsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. Konfiguration sparas nu med det namn du angett. Välj namnet på konfigurationens om du vill visa konfigurationsinformationen. Om du vill göra ändringar, Välj **redigera konfigurationer**. 

6. När du har slutfört konfigurationsinställningarna för som kan du köra projektet mot fjärrklustret eller utföra fjärrfelsökning.
   
   ![Kör fjärrkontroll](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Klicka på den **frånkoppling** knappen Skicka loggarna inte visas i den vänstra panelen. Det dock fortfarande körs på serverdelen.

   ![Kör fjärrkontroll](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Utföra fjärrfelsökning
1. Ställ in senaste punkter och klicka sedan på den **Remote debug** ikon. Skillnaden med fjärråtkomst överföring är som SSH-användarnamn/lösenord måste konfigureras.

   ![Välj debug-ikon](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. När programkörningen når den senaste punkten, visas en **drivrutinen** fliken och två **utföraren** flikarna i den **felsökare** fönstret. Välj den **återuppta programmet** ikon för att fortsätta att köra kod, som når nästa brytpunkten. Du måste växla till rätt **utföraren** att hitta målet utförare för felsökning. Du kan visa körningsloggar på motsvarande **konsolen** fliken.

   ![Fliken felsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Utför fjärrfelsökning och åtgärda fel
1. Ställa in två bryta punkter och välj sedan den **felsöka** ikon för att starta felsökning fjärrprocessen.

2. Koden stannar vid den första platsen för senaste och parametern och variabel information visas i den **variabler** fönstret. 

3. Välj den **återuppta programmet** ikon för att fortsätta. Koden stannar vid den andra punkten. Det är undantagsfel som förväntat.

   ![Utlösa fel](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Välj den **återuppta programmet** ikonen igen. Den **HDInsight Spark skicka** fönstret visas felet ”jobbet körningen misslyckades”.

   ![Skicka fel](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Om du vill uppdatera dynamiskt variabelns värde med hjälp av IntelliJ felsökning kapaciteten, Välj **felsöka** igen. Den **variabler** fönstret visas igen. 

6. Högerklicka på målet på den **felsöka** och välj sedan **ange värde**. Ange sedan ett nytt värde för variabeln. Välj sedan **RETUR** att spara värdet. 

   ![Ange värde](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Välj den **återuppta programmet** ikon för att fortsätta att köra programmet. Nu är har inga undantag inträffat. Du kan se att projektet har körts utan undantag.

   ![Felsöka utan undantag](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala projekt (video): [skapa Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärråtkomst debug (video): [Använd Azure Toolkit för IntelliJ till att felsöka Spark-program på ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att skapa realtid strömmade program](apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure Toolkit för IntelliJ för att skapa Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
