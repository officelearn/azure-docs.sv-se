---
title: 'Azure Toolkit för IntelliJ: Skapa Spark-program för ett HDInsight-kluster '
description: Använd Azure Toolkit för IntelliJ för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: a22dd1114b6ad49695b1ce7cab2ff26f23b7e1be
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447862"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster

Använd Azure Toolkit för IntelliJ plugin-programmet för att utveckla [Apache Spark](https://spark.apache.org/) program som skrivits i [Scala](https://www.scala-lang.org/), och sedan skicka dem till ett HDInsight Spark-kluster direkt från IntelliJ integrerad utveckling Environment (IDE). Du kan använda plugin-programmet på flera sätt:

* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till dina Azure HDInsight Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  Den här kursen använder Java version 8.0.202.
* IntelliJ IDEA. Den här artikeln använder [IntelliJ IDEA communityversion  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Se [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS. EXE.  Se [problem att köra Hadoop på Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA
Installera Scala-plugin-programmet via följande steg:

1. Öppna IntelliJ IDEA.

2. På välkomstskärmen går du till **Konfigurera** > **Plugin-program** för att öppna fönstret **Plugin-program**.
   
    ![Aktivera plugin-programmet Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Välj **Installera** för det Scala-plugin-program som visas i det nya fönstret.  

    ![Installera plugin-programmet Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Du måste starta om IDE när plugin-programmet har installerats.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala Spark-program för ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Azure Spark/HDInsight** i den vänstra rutan.

3. Välj **Spark-projekt (Scala)** i huvudfönstret.

4. Från listrutan **Byggverktyg** väljer du något av följande:
      * **Maven** för guidestöd när du skapar Scala-projekt.
      * **SBT** för att hantera beroenden när du skapar Scala-projektet.

    ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Välj **Nästa**.

6. I fönstret **Nytt projekt** anger du följande information:  

    |  Egenskap    | Beskrivning   |  
    | ----- | ----- |  
    |Projektnamn| Ange ett namn.  I den här självstudien används `myApp`.|  
    |Projektplats| Ange önskad plats för att spara projektet.|
    |Projekt-SDK| Det kan vara tomma på första gången du använder idé.  Välj **Nytt...**  och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Välj Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Välj **Slutför**.  Det kan ta några minuter innan projektet blir tillgänglig.

8. Spark-projekt skapar automatiskt en artefakt. Om du vill visa artefakten, gör du följande:

   a. Från menyraden navigerar du till **filen** > **projektstruktur...** .

   b. Från den **projektstruktur** väljer **artefakter**.  

   c. Välj **Avbryt** när du visar artefakten.

      ![Artefakten information i dialogrutan](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Lägg till din programkällkod genom att göra följande:

    a. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**.  

    b. Högerklicka på **scala**, och gå sedan till **New** > **Scala klass**.

   ![Kommandon för att skapa en Scala-klass från projektet](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. I den **Skapa ny klass med Scala** dialogrutan rutan, ange ett namn, Välj **objekt** i den **typ** listrutan och välj sedan **OK**.

     ![Skapa ny klass med Scala-dialogrutan](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Den **myApp.scala** sedan öppnas i vyn huvudsakliga filen. Ersätt standardkoden med koden finns nedan:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Koden läser data från HVAC.csv (tillgängligt i alla HDInsight Spark-kluster), hämtar de rader som innehåller endast en siffra i den sjunde kolumnen i CSV-filen och skriver utdata till `/HVACOut` under standardbehållare för lagring för klustret.

## <a name="connect-to-your-hdinsight-cluster"></a>Ansluta till ditt HDInsight-kluster
Användaren kan antingen [logga in på Azure-prenumeration](#sign-in-to-your-azure-subscription), eller [länka ett HDInsight-kluster](#link-a-cluster) med Ambari användarnamn/lösenord eller domänanslutna autentiseringsuppgift som ska ansluta till ditt HDInsight-kluster.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.
       
   ![Azure Explorer-länk](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Azure Explorer högerklickar du på den **Azure** noden och välj sedan **logga In**.

3. I den **Azure-inloggning** dialogrutan **logga in**, och ange dina autentiseringsuppgifter för Azure.

    ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. När du har loggat in, den **Välj prenumerationer** dialogrutan visar en lista över alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Välj din prenumeration och välj sedan den **Välj** knappen.

    ![Dialogrutan Välj prenumerationer](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Från **Azure Explorer**, expandera **HDInsight** visa HDInsight Spark-kluster som finns i din prenumeration.

    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Du kan ytterligare expandera en nod för klustrets namn om du vill visa de resurser (till exempel lagringskonton) som är associerade med klustret.

    ![En expanderad klusternamnet nod](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Länka ett kluster
Du kan länka ett HDInsight-kluster med hjälp av Apache Ambari hanteras användarnamnet. På samma sätt för ett domänanslutet HDInsight-kluster du kan länka med hjälp av domänen och användarnamnet, till exempel user1@contoso.com. Du kan också länka Livy Service-kluster.

1. Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.

2. Azure Explorer högerklickar du på den **HDInsight** noden och välj sedan **Link A kluster**.

   ![länken snabbmenyn för kluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. De tillgängliga alternativen i den **Link A kluster** fönstret varierar beroende på vilket värde som du väljer från den **Länkresurstyp** listrutan.  Ange värdena och välj sedan **OK**.

    * **HDInsight-kluster**  
  
        |Egenskap  |Värde |
        |----|----|
        |Länkresurstyp|Välj **HDInsight-kluster** från den nedrullningsbara listan.|
        |Klustrets namn/URL| Ange klusternamnet.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standardvärdet är administratör.|
        |Lösenord| Ange lösenordet för användarnamnet.|
    
        ![länka hdinsight-kluster dialog](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy-tjänsten**  
  
        |Egenskap  |Värde |
        |----|----|
        |Länkresurstyp|Välj **Livy Service** från den nedrullningsbara listan.|
        |Livy-slutpunkt| Ange Livy-slutpunkt|
        |Klusternamn| Ange klusternamnet.|
        |Yarn-slutpunkt|Valfri.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standardvärdet är administratör.|
        |Lösenord| Ange lösenordet för användarnamnet.|

        ![länka livy kluster dialogrutan](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Du kan se din länkade kluster från den **HDInsight** noden.

   ![länkade kluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Du kan också Avlänka ett kluster från **Azure Explorer**.

   ![ta bort kopplingen kluster](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Kör ett Scala Spark-program på ett HDInsight Spark-kluster
När du har skapat ett Scala-program, kan du skicka den till klustret.

1. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  Högerklicka på **myApp**, och välj **skicka Spark-programmet** (den sannolikt är placerad längst ned i listan).
    
      ![Spark-programmet skicka till HDInsight-kommando](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. I den **skicka Spark-programmet** dialogruta, väljer **1. Spark i HDInsight**.

3. I den **Upravit konfiguraci** fönstret anger du följande värden och välj sedan **OK**:

    |Egenskap  |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj HDInsight Spark-klustret som du vill köra ditt program.|
    |Välj en artefakt att skicka|Lämna standardinställningen.|
    |Namn på main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...** ) och välja en annan klass.|
    |Jobbkonfigurationer|Du kan ändra standardnycklar och/eller värden. Mer information finns i [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Kommandoradsargument|Du kan ange argument avgränsade med blanksteg för huvudklass om det behövs.|
    |Refererade JAR-filer och refererade filer|Du kan ange sökvägarna för den refererade JAR-filer och filer eventuellt. Mer information: [Apache Spark-konfigurationen](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Se även [ladda upp resurser ska ingå i klustret](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Ladda upp Jobblagring|Expandera för att visa ytterligare alternativ.|
    |Lagringstyp|Välj **använda Azure Blob för att ladda upp** från den nedrullningsbara listan.|
    |Lagringskonto|Ange ditt storage-konto.|
    |Lagringsnyckel|Ange din lagringsnyckeln.|
    |Lagringscontainer|Välj din lagringsbehållare från den nedrullningsbara listan när **Lagringskonto** och **Lagringsnyckeln** har angetts.|

    ![Dialogrutan Skicka Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Välj **SparkJobRun** att skicka ditt projekt till det markerade klustret. Den **Remote Spark-jobb i kluster** fliken visas jobbkörningens förlopp längst ned på sidan. Du kan stoppa programmet genom att klicka på den röda knappen. Läs hur du kommer åt jobbutdata i den ”åtkomst och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ” senare i den här artikeln.  
      
    ![Fönstret Spark bidrag](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Felsöka Apache Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster 
Vi rekommenderar också ett annat sätt för att skicka in Spark-programmet till klustret. Du kan göra det genom att ange parametrarna i den **kör/Debug konfigurationer** IDE. Mer information finns i [felsöka Apache Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ
Du kan utföra olika åtgärder med hjälp av Azure Toolkit för IntelliJ.  De flesta åtgärder initieras från **Azure Explorer**.  Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Få åtkomst till jobbvyn

1. Från Azure Explorer navigerar du till **HDInsight** > \<Your kluster >> **jobb**.

    ![Jobbet visa nod](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. I den högra rutan i **Spark Jobbvy** fliken visar alla program som körs i klustret. Välj namnet på programmet som du vill se mer information.

    ![Programinformation](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Hovra över jobbdiagram om du vill visa grundläggande information om som körs. Välj en nod på jobbdiagram om du vill visa faser graph och information som alla jobb genererar.

    ![Information om steg](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Visa ofta används loggfiler, till exempel *drivrutinen Stderr*, *drivrutinen Stdout*, och *Directory information*väljer den **Log** fliken.

    ![Logginformation](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Du kan också visa historik för Spark Användargränssnittet och YARN-Användargränssnittet (på programnivå) genom att välja en länk längst ned i fönstret.

### <a name="access-the-spark-history-server"></a>Access Spark-historikserver

1. Från Azure Explorer, expandera **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna Användargränssnittet för Spark-historik**.  
2. När du uppmanas ange administratörsautentiseringsuppgifter för klustrets och som du angav när du ställer in i klustret.

3. Du kan använda namnet på programmet för att söka efter programmet att du bara körts på instrumentpanelen för Spark historik server. I föregående kod, ange namnet på programmet med hjälp av `val conf = new SparkConf().setAppName("myApp")`. Programnamnet Spark är därför **myApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portalen

1. Från Azure Explorer, expandera **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna klustret Management Portal(Ambari)**.  

2. När du uppmanas, ange administratörsautentiseringsuppgifterna för klustret. Du har angett autentiseringsuppgifterna under klusterkonfigurationen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer
Som standard visar Azure Toolkit för IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill komma åt.  

1. Azure Explorer högerklickar du på den **Azure** root node och välj sedan **Välj prenumerationer**.  

2. Från den **Välj prenumerationer** fönstret avmarkerar kryssrutorna bredvid prenumerationerna som du inte vill få åtkomst till och välj sedan **Stäng**.

## <a name="spark-console"></a>Spark-konsolen
Du kan köra Spark lokala Console(Scala) eller köra Spark Livy interaktiva sessionen Console(Scala).

### <a name="spark-local-consolescala"></a>Spark lokala Console(Scala)
Se till att du har uppfyllt WINUTILS. EXE-krav.

1. Från menyraden navigerar du till **kör** > **redigera konfigurationer...** .

2. Från den **kör/Debug konfigurationer** i den vänstra rutan i fönstret går du till **Apache Spark i HDInsight** > **[Spark på HDInsight] myApp**.

3. I huvudfönstret, Välj den **lokalt kör** fliken.

4. Ange följande värden och välj sedan **OK**:

    |Egenskap  |Värde |
    |----|----|
    |Jobbet main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...** ) och välja en annan klass.|
    |Miljövariabler|Kontrollera värdet för HADOOP_HOME är korrekt.|
    |WINUTILS.exe plats|Se till att sökvägen är korrekt.|

    ![Konfiguration av lokal konsol](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  

6. Från menyraden navigerar du till **verktyg** > **Spark konsolen** > **kör Spark lokala Console(Scala)**.

7. Sedan ska två dialogrutor visas om du vill ställa du om du vill att automatiskt åtgärda beroenden. I så, fall Välj **automatiskt åtgärda**.

    ![Spark automatisk Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark automatisk Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Konsolen ska se ut som i bilden nedan. I konsolen fönstret typen `sc.appName`, och tryck på ctrl + RETUR.  Resultatet visas. Du kan säga upp den lokala konsolen genom att klicka på röd knapp.

    ![Lokala konsolen resultat](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark-Livy interaktiva sessionen Console(Scala)
Den stöds bara på IntelliJ 2018.2 och 2018.3.

1. Från menyraden navigerar du till **kör** > **redigera konfigurationer...** .

2. Från den **kör/Debug konfigurationer** i den vänstra rutan i fönstret går du till **Apache Spark i HDInsight** > **[Spark på HDInsight] myApp**.

3. I huvudfönstret, Välj den **fjärrköra i kluster** fliken.

4. Ange följande värden och välj sedan **OK**:

    |Egenskap  |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj HDInsight Spark-klustret som du vill köra ditt program.|
    |Namn på main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...** ) och välja en annan klass.|

    ![Konfiguration av interaktiv konsol](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  

6. Från menyraden navigerar du till **verktyg** > **Spark konsolen** > **kör Spark Livy interaktiva sessionen Console(Scala)**.

7. Konsolen ska se ut som i bilden nedan. I konsolen fönstret typen `sc.appName`, och tryck på ctrl + RETUR.  Resultatet visas. Du kan säga upp den lokala konsolen genom att klicka på röd knapp.

    ![Interaktiv konsol resultat](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markeringen till Spark-konsolen

Det är bra att förutse resultatet skriptet genom att skicka kod till den lokala konsolen eller Livy interaktiva sessionen Console(Scala). Du kan markera kod i filen Scala och sedan högerklickar du på **skicka markeringen till Spark konsolen**. Den markerade koden kommer att skickas till konsolen och utföras. Resultatet visas efter den i konsolen. Konsolen ska kontrollera felen om befintlig.  

   ![Skicka markeringen till Spark-konsolen](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ IDEA-programmen kan använda Azure Toolkit för IntelliJ

Du kan konvertera befintliga Spark Scala program som du skapade i IntelliJ IDEA till att vara kompatibel med Azure Toolkit för IntelliJ. Du kan sedan använda plugin-programmet för att skicka program till ett HDInsight Spark-kluster.

1. Öppna filen associerade .iml för ett befintligt Spark Scala-program som har skapats via IntelliJ IDEA.

1. I roten nivån är en **modulen** elementet enligt följande:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Redigera elementet så att lägga till `UniqueKey="HDInsightTool"` så att den **modulen** ser ut så här:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Spara ändringarna. Ditt program bör nu vara kompatibla med Azure Toolkit för IntelliJ. Du kan testa den genom att högerklicka på projektnamnet i projektet. Snabbmenyn har nu alternativet **skicka Spark-program till HDInsight**.

## <a name="troubleshooting"></a>Felsökning

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Fel i lokal körning: *Använda en större heap-storlek*
Om du använder en 32-bitars Java SDK vid lokal körning i Spark 1.6 kan du uppstå följande fel:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Dessa fel inträffa heap-storleken inte är är tillräckligt stort för Spark kan köras. Spark kräver minst 471 MB. (Mer information finns i [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) En enkel lösning är att använda en 64-bitars Java SDK. Du kan också ändra inställningarna för JVM i IntelliJ genom att lägga till följande alternativ:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Att lägga till alternativ i rutan ”alternativ för virtuell dator” i IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Om klustret är upptagen, kan du få felet nedan.

![Intellij får fel när klustret upptagen](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij får fel när klustret upptagen](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Feedback och kända problem
För närvarande stöds visa Spark utdata direkt inte.

Om du har förslag eller feedback, eller om du stöter på problem när du använder det här plugin-programmet, maila hdivstool@microsoft.com.

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala-projekt (video): [Skapa Apache Spark Scala-appar](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsök (video): [Felsöka Apache Spark-program via fjärranslutning på HDInsight-kluster med hjälp av Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Felsöka Apache Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Felsöka Apache Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktygen i Azure Toolkit för Eclipse för att skapa Apache Spark-program](apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>För att hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
