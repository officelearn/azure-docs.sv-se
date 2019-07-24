---
title: 'Azure Toolkit for IntelliJ: Felsöka Spark-program via SSH '
description: Stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit for IntelliJ att felsöka program via fjärr anslutning i HDInsight-kluster via SSH
keywords: Felsöka fjärran IntelliJ, fjärrfelsökning IntelliJ, SSH, IntelliJ, HDInsight, felsöka IntelliJ, fel sökning
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 0bca6c16124f886d9df9e88e651f3f7450f51a1a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876356"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Apache Spark program lokalt eller via fjärr anslutning i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH

Den här artikeln innehåller steg-för-steg-anvisningar om hur du använder HDInsight-verktyg i [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) för att felsöka program via en fjärr anslutning i ett HDInsight-kluster. Om du vill felsöka ditt projekt kan du även visa [fel söknings programmet HDInsight Spark med Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

**Krav**
* **HDInsight-verktyg i Azure Toolkit for IntelliJ**. Det här verktyget är en del av Azure Toolkit for IntelliJ. Mer information finns i [installera Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Och **Azure Toolkit for IntelliJ**. Använd det här verktyget för att skapa Apache Spark program för ett HDInsight-kluster. Mer information får du genom att följa anvisningarna i [använda Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH-tjänsten med användar namn och lösen ords hantering**. Mer information finns i [ansluta till HDInsight (Apache Hadoop) med SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [använda SSH-tunnlar för att komma åt AMBARI-webbgränssnittet, JobHistory, NameNode, Apache Oozie och andra webb-UIS](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Lär dig hur du utför lokal körning och fel sökning
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenario 1: Skapa ett Spark Scala-program 

1. Starta IntelliJ IDEA och skapa sedan ett projekt. Gör följande i dialogrutan **Nytt projekt**:

   a. Välj **Azure Spark/HDInsight**. 

   b. Välj en Java-eller Scala-mall baserat på din preferens. Välj mellan följande alternativ:

   - **Spark-projekt (Java)**

   - **Spark-projekt (Scala)**

   - **Spark-projekt med exempel (Scala)**

   - **Spark-projekt med felsöknings exempel för fel aktivitet (för hands version) (Scala)**

     I det här exemplet används en **Spark-projekt med exempel (Scala)-** mall.

   c. I listan med **byggverktyg** väljer du något av följande enligt dina behov:

   - **Maven**, för guidestöd när du skapar Scala-projekt

   - **SBT**, för att hantera beroenden när du skapar Scala-projekt 

     ![Skapa ett fel söknings projekt](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Välj **Nästa**.     
 
1. I nästa **nya projekt** fönster gör du följande:

   ![Välj Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Ange ett projekt namn och en projekt plats.

   b. I list rutan **Project SDK** väljer du **Java 1,8** för **Spark 2. x** -kluster eller väljer **Java 1,7** för **Spark 1. x** -kluster.

   c. I list rutan **Spark-version** integrerar guiden skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Spark-klustrets version är tidigare än 2,0 väljer du **Spark 1. x**. Annars väljer du **Spark 2. x.** I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)** .

   d. Välj **Slutför**.

1. Välj **src** > main- > **Scala** för att öppna din kod i projektet. I det här exemplet används **SparkCore_wasbloTest** -skriptet.

### <a name="prerequisite-for-windows"></a>Förutsättning för Windows
När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag, enligt beskrivningen i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget beror på att WinUtils. exe saknas i Windows. 

Lös problemet genom att [Ladda ned den körbara filen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats, till exempel **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**och ange värdet för variabeln till **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenario 2: Utför lokal körning
1. Öppna **SparkCore_wasbloTest** -skriptet, högerklicka på skript redigeraren och välj sedan alternativet **kör [Spark Job] XXX** för att utföra lokal körning.
1. När den lokala körningen är klar kan du se utdatafilen Spara till din aktuella **__standard__** för Project Explorer- **data** > .

    ![Lokalt körnings resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Våra verktyg har ställt in standard konfigurationen för lokal körning automatiskt när du utför den lokala körningen och lokal fel sökning. Öppna konfigurationen **[Spark på HDInsight] XXX** i det övre högra hörnet, så kan du se **[Spark på HDInsight] XXX** redan har skapats under **Apache Spark i HDInsight**. Växla till fliken **lokalt körning** .

    ![Lokal körnings konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Miljövariabler](#prerequisite-for-windows): Om du redan anger system miljö variabeln **HADOOP_HOME** till **C:\WinUtils**kan den automatiskt identifiera att du inte behöver lägga till manuellt.
    - [Plats för WinUtils. exe](#prerequisite-for-windows): Om du inte har angett system miljö variabeln kan du hitta platsen genom att klicka på knappen.
    - Du behöver bara välja något av två alternativ och de behövs inte på MacOS och Linux.
1. Du kan också ange konfigurationen manuellt innan du utför lokal körning och lokal fel sökning. Välj plus tecknet ( **+** ) i föregående skärm bild. Välj sedan alternativet **Apache Spark på HDInsight** . Ange information om **namn**, **huvud klass namn** som ska sparas och klicka sedan på knappen lokal körning.

### <a name="scenario-3-perform-local-debugging"></a>Scenario 3: Utför lokal fel sökning
1. Öppna **SparkCore_wasbloTest** -skriptet och ange Bryt punkter.
1. Högerklicka på skript redigeraren och välj sedan alternativet **Felsök [Spark på HDInsight] XXX** för att utföra lokal fel sökning.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Lär dig hur du utför fjärrkörning och fel sökning
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Utför fjärrkörning

1. Välj ikonen i det övre högra hörnet för att komma åt menyn **Redigera konfigurationer** . Från den här menyn kan du skapa eller redigera konfigurationer för fjärrfelsökning.

   ![Redigera konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. I dialog rutan **Kör/Felsök konfigurationer** väljer du plus tecknet ( **+** ). Välj sedan alternativet **Apache Spark på HDInsight** .

   ![Lägg till ny konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Växla till **fjärran sluten på kluster** -fliken. Ange information om **namn**, **Spark-kluster**och **huvud klass namn**. Klicka sedan på **Avancerad konfiguration (fjärrfelsökning)** . Våra verktyg stöder fel sökning med körningar. Standardvärdet är 5 i **numExectors**. Du är bättre på att inte ange högre än 3.

   ![Kör fel söknings konfigurationer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. I delen **Avancerad konfiguration (Remote-felsökning)** väljer du **Aktivera Spark**-fjärrfelsökning. Ange SSH-användarnamnet och ange ett lösen ord eller Använd en privat nyckel fil. Om du vill utföra fjärrfelsökning måste du ange den. Du behöver inte ange det om du bara vill använda fjärran sluten.

   ![Aktivera fjärrfelsökning med Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurationen sparas nu med det namn du angav. Välj konfigurations namnet om du vill visa konfigurations informationen. Välj **Redigera konfigurationer**om du vill göra ändringar. 

1. När du har slutfört konfigurations inställningarna kan du köra projektet mot fjärrklusteret eller utföra fjärrfelsökning.
   
   ![Fjärrkörning (knapp)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Klicka på knappen **Koppla från** om du inte vill att sändnings loggarna ska visas i den vänstra panelen. Den körs dock fortfarande på Server delen.

   ![Fjärrkörning (knapp)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Utför fjärrfelsökning
1. Konfigurera Bryt punkter och klicka sedan på ikonen **fjärrfelsökning** . Skillnaden med fjärrsändning är att SSH username/Password måste konfigureras.

   ![Välj ikonen Felsök](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. När program körningen når Bryt punkten ser du fliken **driv rutin** och två **utförar** -flikar i **fel söknings** fönstret. Välj ikonen **återuppta program** för att fortsätta att köra koden, som sedan når nästa Bryt punkt. Du måste växla till rätt **utförar** -flik för att hitta mål utförar för fel sökning. Du kan visa körnings loggarna på motsvarande **konsol** flik.

   ![Fliken fel sökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Utför fjärrfelsökning och åtgärda fel
1. Konfigurera två Bryt punkter och välj sedan **fel söknings** ikonen för att starta fjärrfelsökning.

1. Koden stannar vid den första Bryt punkten och parameter-och variabel informationen visas i fönstret **variabler** . 

1. Välj ikonen **återuppta program** för att fortsätta. Koden stoppas vid den andra punkten. Undantaget upptäcktes som förväntat.

   ![Utlös fel](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Välj ikonen **återuppta program** igen. I fönstret **HDInsight Spark-sändning** visas fel meddelandet "Det gick inte att köra jobb".

   ![Fel vid överföring](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Om du vill uppdatera variabelvärdet dynamiskt genom att använda IntelliJ fel söknings funktion väljer du **Felsök** igen. Fönstret **variabler** visas igen. 

1. Högerklicka på målet på fliken **Felsök** och välj sedan **Ange värde**. Ange sedan ett nytt värde för variabeln. Spara värdet genom att välja **RETUR** . 

   ![Ange värde](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Välj ikonen **för** att fortsätta köra programmet. Den här gången har inget undantag påträffats. Du kan se att projektet har körts utan undantag.

   ![Felsök utan undantag](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Nästa steg
* [: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala-projekt (video): [Skapa Apache Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsökning (video): [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning i ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda spark i HDInsight för att analysera skapande temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använd Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Skapa Apache Spark-program med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängligt för Jupyter Notebook i Apache Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
