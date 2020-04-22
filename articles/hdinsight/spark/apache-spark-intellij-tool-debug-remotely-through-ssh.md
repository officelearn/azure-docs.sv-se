---
title: 'Azure Toolkit för IntelliJ: Debug Spark-appar med SSH - HDInsight'
description: Steg-för-steg-vägledning om hur du använder HDInsight Tools i Azure Toolkit för IntelliJ för att felsöka program på distans på HDInsight-kluster via SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: a012c3ce8f7c9e105a42d8383a502f3608c84070
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732909"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Apache Spark-program i ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH

Den här artikeln innehåller steg-för-steg-vägledning om hur du använder HDInsight-verktyg i [Azure Toolkit för IntelliJ för](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) att felsöka program på distans i ett HDInsight-kluster. Om du vill felsöka projektet kan du även visa [Debug HDInsight Spark-programmen med Azure Toolkit för IntelliJ-video.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Se [Skapa ett Apache Spark-kluster](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* För Windows-användare: När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag, vilket förklaras i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget inträffar eftersom WinUtils.exe saknas i Windows.

    LÃ¶s problemet genom att ã¤lja [winutils.exe](https://github.com/steveloughran/winutils) till en plats som **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME**och ange variabelns värde på **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Gemenskapen upplagan är gratis.).

* [Azure Toolkit för IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Scala plugin för IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Skapa ett Spark Scala-program

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

1. Välj **Apache Spark/HDInsight** i den vänstra rutan.

1. Välj **Spark Project med exempel (Scala)** i huvudfönstret.

1. Från listrutan **Byggverktyg** väljer du något av följande:

    * **Maven** för guidestöd när du skapar Scala-projekt.
    * **SBT** för att hantera beroenden när du skapar Scala-projektet.

     ![Intellij Skapa nytt projekt Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Välj **Nästa**.

1. Ange följande information i nästa nya **projektfönster:**

    |Egenskap |Beskrivning |
    |---|---|
    |Projektnamn|Ange ett namn. Denna promenad `myApp`genom användningsområden .|
    |Projektplats|Ange önskad plats för att spara projektet.|
    |Projekt-SDK|Om du är tom väljer du **Ny...** och navigerar till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x.**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij New Project välj Spark-version](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Välj **Slutför**. Det kan ta några minuter innan projektet blir tillgängligt. Titta i det nedre högra hörnet för framsteg.

1. Expandera projektet och navigera till **src** > **main** > **scala** > **sample**. Dubbelklicka **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Utför lokal körning

1. Högerklicka på skriptredigeraren från **SparkCore_WasbIOTest** skriptet och välj sedan alternativet **Kör SparkCore_WasbIOTest** för att utföra lokal körning.

1. När den lokala körningen är klar kan du se att utdatafilen sparas i din aktuella **project explorer-data** > **__som standard__**.

    ![Resultat för lokalt körning i Intellij-projekt](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Våra verktyg har ställt in standardkonfigurationen för lokal körning automatiskt när du utför den lokala körningen och den lokala felsökningen. Öppna konfigurationen **[Spark på HDInsight] XXX** i det övre högra hörnet, kan du se **[Spark på HDInsight] XXX** redan skapats under **Apache Spark på HDInsight**. Växla till fliken **Lokalt kör.**

    ![Intellij Kör felsökningskonfigurationer lokal körning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Miljövariabler:](#prerequisites)Om du redan har ställt in systemmiljövariabeln **HADOOP_HOME** till **C:\WinUtils**kan den automatiskt upptäcka att inget behov av att manuellt lägga till.
    - [WinUtils.exe Plats:](#prerequisites)Om du inte har ställt in systemmiljövariabeln kan du hitta platsen genom att klicka på dess knapp.
    - Välj bara något av två alternativ och de behövs inte på MacOS och Linux.

1. Du kan också ställa in konfigurationen manuellt innan du utför lokal körning och lokal felsökning. I föregående skärmbild väljer du**+** plustecknet ( ). Välj sedan alternativet **Apache Spark på HDInsight.** Ange information för **Namn**, **Huvudklassnamn** som ska sparas och klicka sedan på den lokala körknappen.

## <a name="perform-local-debugging"></a>Utföra lokal felsökning

1. Öppna **SparkCore_wasbloTest** skriptet, ange brytpunkter.

1. Högerklicka på skriptredigeraren och välj sedan alternativet **Felsöka "[Spark på HDInsight]XXX"** för att utföra lokal felsökning.

## <a name="perform-remote-run"></a>Utföra fjärrkörning

1. Navigera till **Kör** > **redigeringskonfigurationer...**. På den här menyn kan du skapa eller redigera konfigurationerna för fjärrfelsökning.

1. Välj **Run/Debug Configurations** plustecknet (**+**). Välj sedan alternativet **Apache Spark på HDInsight.**

   ![Intellij Lägg till ny konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Växla till **Fjärrkörning på** fliken Kluster. Ange information för **namn,** **sparkkluster**och **huvudklassnamn**. Klicka sedan på **Avancerad konfiguration (fjärrfelsökning)**. Våra verktyg stöder felsökning med **Executors.** **NumExectors**är standardvärdet 5. Det är bäst att du inte ställer in högre än 3.

   ![Felsökningskonfigurationer för intellij-körning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. I delen **Avancerad konfiguration (fjärrfelsökning)** väljer du **Aktivera fjärrfelsökning för Spark**. Ange SSH-användarnamnet och ange sedan ett lösenord eller använd en privat nyckelfil. Om du vill utföra fjärrfelsökning måste du ställa in den. Det finns ingen anledning att ställa in den om du bara vill använda fjärrkörning.

   ![Intellij Avancerad konfiguration möjliggör spark fjärrfelsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurationen sparas nu med det namn du angav. Om du vill visa konfigurationsinformationen väljer du konfigurationsnamnet. Om du vill göra ändringar väljer du **Redigera konfigurationer**.

1. När du har slutfört konfigurationsinställningarna kan du köra projektet mot fjärrklustret eller utföra fjärrfelsökning.

   ![Knappen Intellij Debug Remote Spark Job Remote run button](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klicka på knappen **Koppla från** att inlämningsloggarna inte visas på den vänstra panelen. Det är dock fortfarande körs på backend.

   ![Intellij Debug Remote Spark Job Remote kör resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Utföra fjärrfelsökning

1. Konfigurera brytpunkter och klicka sedan på ikonen **För fjärrfelsökning.** Skillnaden med fjärrinlämning är att SSH användarnamn / lösenord måste konfigureras.

   ![Ikon för debug-felsökning av fjärr sparkjobb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. När programkörningen når brytpunkten visas fliken **Drivrutin** och två **executorflikar** i **felsökningsfönstret.** Välj ikonen **Återuppta program** om du vill fortsätta köra koden, som sedan når nästa brytpunkt. Du måste växla till rätt **executor-flik** för att hitta målut executor att felsöka. Du kan visa körningsloggarna på motsvarande **konsolflik.**

   ![Fliken Felsöka intellij-felsökning av fjärr sparkjobb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Utföra fjärrfelsökning och felfixering

1. Ställ in två brytpunkter och välj sedan ikonen **Felsökning** för att starta fjärrfelsökningsprocessen.

1. Koden stannar vid den första brytpunkten och parameter- och variabelinformationen visas i fönstret **Variabler.**

1. Välj ikonen **Återuppta program** för att fortsätta. Koden stannar vid den andra punkten. Undantaget fångas som förväntat.

   ![Intellij Debug Remote Spark Job kasta fel](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Välj ikonen **Återuppta program** igen. **HdInsight Spark Submission-fönstret** visar ett fel i felet "jobbkörning misslyckades".

   ![Intellij Debug Remote Spark Job Error inlämning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Om du vill uppdatera variabelvärdet dynamiskt med hjälp av funktionen Felsökning av IntelliJ väljer du **Felsöka** igen. **Fönstret Variabler** visas igen.

1. Högerklicka på målet på fliken **Felsökning** och välj sedan **Ange värde**. Ange sedan ett nytt värde för variabeln. Välj sedan **Retur** för att spara värdet.

   ![Ange värde för Intellij Debug Remote Spark Job](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Välj ikonen **Återuppta program** om du vill fortsätta att köra programmet. Den här gången fångas inget undantag. Du kan se att projektet körs utan några undantag.

   ![Intellij Debug Remote Spark Job utan undantag](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Skapa Scala-projekt (video): [Skapa Apache Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsökning (video): [Använd Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program i ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperaturen med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
