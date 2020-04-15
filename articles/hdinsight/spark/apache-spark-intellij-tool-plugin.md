---
title: 'Azure Toolkit för IntelliJ: Spark-appen - HDInsight'
description: Använd Azure Toolkit för IntelliJ för att utveckla Spark-program skrivna i Scala och skicka dem till ett HDInsight Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a3884fdfbbc215c305053d8615d690880f4026ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314152"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för HDInsight-kluster

Den här artikeln visar hur du utvecklar Apache Spark-program på Azure HDInsight med hjälp av **Plugin-programmet Azure Toolkit** för IntelliJ IDE. [Azure HDInsight](../hdinsight-overview.md) är en hanterad analystjänst med öppen källkod i molnet. Med tjänsten kan du använda ramverk med öppen källkod som Hadoop, Apache Spark, Apache Hive och Apache Kafka.

Du kan använda plugin-programmet **Azure Toolkit** på några olika sätt:

* Utveckla och skicka in ett Scala Spark-program till ett HDInsight Spark-kluster.
* Få tillgång till dina Azure HDInsight Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Använda plugin-programmet Azure Toolkit för IntelliJ
> * Utveckla Apache Spark-program
> * Skicka ett program till Azure HDInsight-kluster

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  I den här artikeln används Java version 8.0.202.

* Intellij IDÉ. I den här artikeln används [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Se [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA

Steg för att installera Scala plugin:

1. Öppna IntelliJ IDEA.

2. På välkomstskärmen**navigerar** du till **Konfigurera** > plugins för att öppna **plugins-fönstret.**

    ![IntelliJ IDEA möjliggör scala plugin](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Välj **Installera** för det Scala-plugin-program som visas i det nya fönstret.  

    ![IntelliJ IDEA installerar scala plugin](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Du måste starta om IDE när plugin-programmet har installerats.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Spark Scala-program för ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Azure Spark/HDInsight** i den vänstra rutan.

3. Välj **Spark-projekt (Scala)** i huvudfönstret.

4. Välj något av följande alternativ i listrutan **Byggverktyg:**
   * **Maven** för guidestöd när du skapar Scala-projekt.
   * **SBT** för att hantera beroenden när du skapar Scala-projektet.

     ![Dialogrutan IntelliJ IDEA Nytt projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Välj **Nästa**.

6. I fönstret **Nytt projekt** anger du följande information:  

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Projektnamn| Ange ett namn.  Den här `myApp`artikeln använder .|  
    |Projektplats| Ange den plats där du vill spara projektet.|
    |Projekt-SDK| Det här fältet kan vara tomt när du använder IDEA första gången.  Välj **Nytt... ** och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Välja Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Välj **Slutför**.  Det kan ta några minuter innan projektet blir tillgängligt.

8. Spark-projektet skapar automatiskt en artefakt åt dig. Så här visar du artefakten:

   a. Från menyraden navigerar du till > **Arkivprojektstruktur...**. **File**

   b. Välj **Artefakter**i fönstret **Projektstruktur** .  

   c. Välj **Avbryt** när du har visat artefakten.

      ![Artefaktinformation i dialogrutan](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Lägg till programmets källkod genom att göra följande:

    a. Från Project navigerar du till **myApp** > **src** > **main** > **scala**.  

    b. Högerklicka på **scala**och navigera sedan till **Ny** > **Scala-klass**.

   ![Kommandon för att skapa en Scala-klass från Project](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Ange ett namn i dialogrutan **Skapa ny scala-klass,** välj **Objekt** i listrutan **Typ** och välj sedan **OK**.

     ![Dialogrutan Skapa ny scala-klass](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Filen **myApp.scala** öppnas sedan i huvudvyn. Ersätt standardkoden med koden nedan:  

    ```scala
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
    ```

    Koden läser data från HVAC.csv (tillgänglig på alla HDInsight Spark-kluster), hämtar raderna som bara har en siffra i den `/HVACOut` sjunde kolumnen i CSV-filen och skriver utdata till under standardlagringsbehållaren för klustret.

## <a name="connect-to-your-hdinsight-cluster"></a>Anslut till ditt HDInsight-kluster

Användaren kan antingen [logga in på Azure-prenumeration](#sign-in-to-your-azure-subscription)eller [länka ett HDInsight-kluster](#link-a-cluster). Använd Ambaris användarnamn/lösenord eller domänen som anslutits för att ansluta till ditt HDInsight-kluster.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. På menyraden navigerar du till **Visa** > **verktyg i Utforskaren** > i**Utforskaren**.

   ![IntelliJ IDEA visar azure explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Högerklicka på Azure-noden **Azure** i Azure Explorer och välj sedan **Logga in**.

   ![IntelliJ IDEA explorer högerklickar på azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. I dialogrutan **Azure-inloggning** väljer du **Enhetsinloggning**och väljer sedan **Logga in**.

    !["IntelliJ IDEA azure inloggningsenhet inloggning"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Klicka på **Kopiera&öppna**i dialogrutan Azure Device **Login** .

   !["IntelliJ IDEA azure device login"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Klistra in koden i webbläsargränssnittet och klicka sedan på **Nästa**.

   !["Microsoft ange koddialogruta för HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Ange dina Azure-autentiseringsuppgifter och stäng sedan webbläsaren.

   !["Microsoft anger e-postdialogruta för HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. När du är inloggad visas alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna i dialogrutan **Välj prenumerationer.** Välj din prenumeration och välj sedan knappen **Välj.**

    ![Dialogrutan Select Subscriptions (Välj prenumerationer)](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Expandera **HDInsight** från **Azure Explorer**för att visa HDInsight Spark-kluster som finns i dina prenumerationer.

    ![Huvudvy för IntelliJ IDEA Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Om du vill visa de resurser (till exempel lagringskonton) som är associerade med klustret kan du expandera ytterligare en klusternamnnod.

    ![Lagringskonton för Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett HDInsight-kluster med hjälp av apacheambari-hanterade användarnamn. På samma sätt kan du länka för ett domänanslutet HDInsight-kluster med hjälp av domänen och användarnamnet, till exempel `user1@contoso.com`. Du kan också länka Livy Service-klustret.

1. På menyraden navigerar du till **Visa** > **verktyg i Utforskaren** > i**Utforskaren**.

1. Högerklicka på **HDInsight-noden** i Azure Explorer och välj sedan **Länka ett kluster**.

   ![Snabbmeny för Azure Explorer-länkkluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. De tillgängliga alternativen i fönstret **Länka ett kluster** varierar beroende på vilket värde du väljer i listrutan **Länkresurstyp.**  Ange dina värden och välj sedan **OK**.

    * **HDInsight-kluster**  
  
        |Egenskap |Värde |
        |----|----|
        |Resurstyp för länk|Välj **HDInsight-kluster** i listrutan.|
        |Url för klusternamn/-,| Ange klusternamn.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standard är admin.|
        |lösenord| Ange lösenord för användarnamn.|

        ![IntelliJ IDEA länkar en klusterdialogruta](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service**  
  
        |Egenskap |Värde |
        |----|----|
        |Resurstyp för länk|Välj **Livy Service** i listrutan.|
        |Livy slutpunkt| Ange Livy Slutpunkt|
        |Klusternamn| Ange klusternamn.|
        |Slutpunkt för garn|Valfri.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standard är admin.|
        |lösenord| Ange lösenord för användarnamn.|

        ![IntelliJ IDEA länk Livy kluster dialog](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Du kan se ditt länkade kluster från **HDInsight-noden.**

   ![Azure Explorer-länkat kluster1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Du kan också ta bort länken till ett kluster från **Azure Explorer**.

   ![Olänkat kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Köra ett Spark Scala-program i ett HDInsight Spark-kluster

När du har skapat ett Scala-program kan du skicka det till klustret.

1. Från Project, navigera till **myApp** > **src** > **huvud** > **scala** > **myApp**.  Högerklicka på **myApp**och välj **Skicka Spark Application** (Det kommer sannolikt att finnas längst ner i listan).

      ![Kommandot Skicka Spark-program till HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Välj 1 i dialogrutan **Skicka sparkprogram.** ** Gnista på HDInsight**.

3. Ange följande värden i **fönstret Redigera** konfiguration och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Spark kluster (endast Linux)|Välj det HDInsight Spark-kluster som du vill köra programmet på.|
    |Välj en artefakt som ska skickas|Lämna standardinställningen.|
    |Huvudklassnamn|Standardvärdet är huvudklassen från den valda filen. Du kan ändra klassen genom att välja ellips(**...**)  och välja en annan klass.|
    |Jobbkonfigurationer|Du kan ändra standardnycklarna och eller värdena. Mer information finns i [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argument på kommandoraden|Du kan ange argument avgränsade med blanksteg för huvudklassen om det behövs.|
    |Refererade burkar och refererade filer|Du kan ange sökvägarna för de refererade burkarna och filerna om sådana finns. Du kan också bläddra bland filer i Azures virtuella filsystem, som för närvarande bara stöder ADLS Gen 2-kluster. För mer information: [Apache Spark Konfiguration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Se även [så här laddar du upp resurser till klustret](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Lagring av jobbuppladdning|Expandera för att visa ytterligare alternativ.|
    |Lagringstyp|Välj **Använd Azure Blob för att ladda upp** från listrutan.|
    |Lagringskonto|Ange ditt lagringskonto.|
    |Lagringsnyckel|Ange din lagringsnyckel.|
    |Behållare för lagring|Välj lagringsbehållaren i listrutan när **lagringskontot** och **lagringsnyckeln** har angetts.|

    ![Dialogrutan Spark-inlämning](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Välj **SparkJobRun** om du vill skicka projektet till det valda klustret. På fliken **Fjärr spark-jobb i kluster** visas jobbkörningsstatusen längst ned. Du kan stoppa programmet genom att klicka på den röda knappen.

    ![Fönstret Apache Spark Inlämning](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Felsöka Apache Spark-program lokalt eller på distans i ett HDInsight-kluster

Vi rekommenderar också ett annat sätt att skicka Spark-programmet till klustret. Du kan göra det genom att ange parametrarna i **IDE:s kör-/felsökningskonfigurationer.** Se [Felsökning av Apache Spark-program lokalt eller på distans i ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ

Du kan utföra olika åtgärder med hjälp av Azure Toolkit för IntelliJ.  De flesta av åtgärderna startas från **Azure Explorer**.  På menyraden navigerar du till **Visa** > **verktyg i Utforskaren** > i**Utforskaren**.

### <a name="access-the-job-view"></a>Få tillgång till jobbvyn

1. Från Azure Explorer navigerar du till **HDInsight** > \<ditt kluster> > **jobb**.

    ![Nod för IntelliJ Azure Explorer-jobbvy](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. På den högra rutan visar fliken **Spark Job View** alla program som kördes i klustret. Välj namnet på det program som du vill visa mer information om.

    ![Information om spark-jobbvyprogram](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Om du vill visa grundläggande jobbinformation som körs håller du muspekaren över jobbdiagrammet. Om du vill visa fasdiagrammet och information som varje jobb genererar väljer du en nod i projektdiagrammet.

    ![Sceninformation om Spark Job View-jobb](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Om du vill visa ofta använda loggar, till exempel *Driver Stderr,* *Driver Stdout*och *Kataloginformation,* väljer du fliken **Logg.**

    ![Logga information om spark-jobbvy](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Du kan visa användargränssnittet för Spark-historik och YARN-användargränssnittet (på programnivå). Välj en länk högst upp i fönstret.

### <a name="access-the-spark-history-server"></a>Få tillgång till Spark-historikservern

1. Expandera **HDInsight**från Azure Explorer , högerklicka på spark-klusternamnet och välj sedan **Öppna Spark-historikgränssnittet**.  
2. När du uppmanas att ange klustrets administratörsautentiseringsuppgifter, som du angav när du konfigurerade klustret.

3. På instrumentpanelen för Spark-historik kan du använda programnamnet för att leta efter det program som du just har kört. I koden ovan anger du programnamnet `val conf = new SparkConf().setAppName("myApp")`med hjälp av . Din Spark ansökan namn är **myApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portalen

1. Expandera **HDInsight**från Azure Explorer , högerklicka på spark-klusternamnet och välj sedan **Öppna klusterhanteringsportal(Ambari)**.  

2. När du uppmanas att ange administratörsautentiseringsuppgifterna för klustret. Du angav dessa autentiseringsuppgifter under klusterinstallationsprocessen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard listar Azure Toolkit för IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange vilka prenumerationer som du vill komma åt.  

1. Högerklicka på Azure-rotnoden i Azure Explorer och välj sedan **Välj prenumerationer**. **Azure**  

2. Avmarkera **kryssrutorna** bredvid de prenumerationer som du inte vill komma åt i fönstret Välj prenumerationer och välj sedan **Stäng**.

## <a name="spark-console"></a>Spark-konsol

Du kan köra Spark Local Console(Scala) eller köra Spark Livy Interactive Session Console(Scala).

### <a name="spark-local-consolescala"></a>Spark lokal konsol (Scala)

Se till att du har uppfyllt WINUTILS. EXE-förutsättning.

1. På menyraden navigerar du till **Kör** > **redigeringskonfigurationer...**.

2. Från fönstret **Kör/debug-konfigurationer,** i den vänstra rutan, navigera till **Apache Spark på HDInsight** > **[Spark på HDInsight] myApp**.

3. Välj fliken i **`Locally Run`** huvudfönstret.

4. Ange följande värden och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Huvudklass för jobb|Standardvärdet är huvudklassen från den valda filen. Du kan ändra klassen genom att välja ellips(**...**)  och välja en annan klass.|
    |Miljövariabler|Kontrollera att värdet för HADOOP_HOME är korrekt.|
    |PLATS FÖR WINUTILS.exe|Kontrollera att banan är korrekt.|

    ![Konfiguration av lokal konsoluppsättning](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Från Project, navigera till **myApp** > **src** > **huvud** > **scala** > **myApp**.  

6. På menyraden navigerar du till **Verktyg** > **Spark Console** > **Run Spark Local Console(Scala).**

7. Sedan kan två dialogrutor visas för att fråga dig om du vill åtgärda beroenden automatiskt. Om så är fallet väljer du **Åtgärda automatiskt**.

    ![Dialogrutan IntelliJ IDEA Spark Auto Fix1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![Dialogrutan IntelliJ IDEA Spark Auto Fix2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Konsolen ska se ut ungefär som bilden nedan. I konsolfönstret `sc.appName`skriver du och trycker sedan på ctrl+Retur.  Resultatet visas. Du kan avsluta den lokala konsolen genom att klicka på den röda knappen.

    ![IntelliJ IDEA lokalt konsolresultat](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy interaktiv sessionskonsol (Scala)

1. På menyraden navigerar du till **Kör** > **redigeringskonfigurationer...**.

2. Från fönstret **Kör/debug-konfigurationer,** i den vänstra rutan, navigera till **Apache Spark på HDInsight** > **[Spark på HDInsight] myApp**.

3. Välj fliken i **`Remotely Run in Cluster`** huvudfönstret.

4. Ange följande värden och välj sedan **OK:**

    |Egenskap |Värde |
    |----|----|
    |Spark kluster (endast Linux)|Välj det HDInsight Spark-kluster som du vill köra programmet på.|
    |Huvudklassnamn|Standardvärdet är huvudklassen från den valda filen. Du kan ändra klassen genom att välja ellips(**...**)  och välja en annan klass.|

    ![Konfiguration av interaktiv konsoluppsättning](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Från Project, navigera till **myApp** > **src** > **huvud** > **scala** > **myApp**.  

6. På menyraden navigerar du till **Verktyg** > **Spark Console** > **Run Spark Livy Interactive Session Console(Scala).**

7. Konsolen ska se ut ungefär som bilden nedan. I konsolfönstret `sc.appName`skriver du och trycker sedan på ctrl+Retur.  Resultatet visas. Du kan avsluta den lokala konsolen genom att klicka på den röda knappen.

    ![Intellij IDEA Interaktiv konsol resultat](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markering till Spark Console

Det är bekvämt för dig att förutse skriptresultatet genom att skicka lite kod till den lokala konsolen eller Livy Interactive Session Console (Scala). Du kan markera en del kod i Scala-filen och sedan högerklicka på **Skicka markering till Spark Console**. Den valda koden skickas till konsolen. Resultatet visas efter koden i konsolen. Konsolen kontrollerar felen om den finns.  

   ![Skicka markering till Spark Console](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrera med HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Anslut till ditt HDInsight ESP-kluster med ID Broker (HIB)

Du kan följa de normala stegen för att logga in på Azure-prenumeration för att ansluta till ditt HDInsight ESP-kluster med ID Broker (HIB). När du har loggat in visas klusterlistan i Azure Explorer. Mer instruktioner finns i [Anslut till ditt HDInsight-kluster](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Kör ett Spark Scala-program på ett HDInsight ESP-kluster med ID Broker (HIB)

Du kan följa de normala stegen för att skicka jobb till HDInsight ESP-kluster med ID Broker (HIB). Mer information finns i [Kör ett Spark Scala-program i ett HDInsight Spark-kluster.](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)

Vi laddar upp nödvändiga filer till en mapp som heter ditt inloggningskonto och du kan se uppladdningssökvägen i konfigurationsfilen.

   ![uppladdningssökvägen i konfigurationen](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-konsol på ett HDInsight ESP-kluster med ID Broker (HIB)

Du kan köra Spark Local Console(Scala) eller köra Spark Livy Interactive Session Console(Scala) på ett HDInsight ESP-kluster med ID Broker (HIB). Mer information finns i [Spark Console.](#spark-console)

   > [!NOTE]  
   > För HDInsight ESP-klustret med ID Broker (HIB) går det inte att [länka ett kluster](#link-a-cluster) och [felsöka Apache Spark-program på distans.](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster)

## <a name="reader-only-role"></a>Roll endast för läsare

När användare skickar jobb till ett kluster med rollbehörighet endast för läsare krävs Ambari-autentiseringsuppgifter.

### <a name="link-cluster-from-context-menu"></a>Länka kluster från snabbmenyn

1. Logga in med rollkonto med endast läsare.

2. Expandera **HDInsight** från **Azure Explorer**för att visa HDInsight-kluster som finns i din prenumeration. Klustren märkta **"Roll:Reader"** har bara rollbehörighet för läsare.

    !['IntelliJ Azure Explorer-roll:Reader'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Högerklicka på klustret med rollbehörighet för endast läsare. Välj **Länka det här klustret** från snabbmenyn för att länka klustret. Ange användarnamnet och lösenordet för Ambari.

    ![IntelliJ Azure Explorer länkar det här klustret](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Om klustret har länkats uppdateras HDInsight.
   Scenen i klustret kommer att länkas.
  
    ![Länkad dialogruta för IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Länka klustret genom att expandera jobbnod

1. Klicka på **Jobbnod,** **fönstret Klusterjobbsåtkomst nekad.**

2. Klicka på **Länka det här klustret** om du vill länka klustret.

    ![klusterjobbåtkomst nekad dialogruta](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Fönstret Länka kluster från fönstret Kör/felsöka konfigurationer

1. Skapa en HDInsight-konfiguration. Välj sedan **Fjärrkörning i kluster**.

2. Välj ett kluster som har rollbehörighet endast för läsare för **Spark-kluster(endast Linux)**. Varningsmeddelandet visas. Du kan klicka på **Länka det här klustret** om du vill länka klustret.

   ![IntelliJ IDEA kör/felsökning konfiguration skapa](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Visa lagringskonton

* För kluster med endast läsare rollbehörighet klickar du på **Noden Lagringskonton,** **fönstret Nekad lagringsåtkomst** dyker upp. Du kan klicka på **Öppna Azure Storage Explorer** för att öppna Storage Explorer.

   !["Intellij IDEA Storage Access Nekad"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Knappen Nekad intellij IDEA Storage Access](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* För länkade kluster klickar du på **Noden Lagringskonton,** **fönstret Nekad lagringsåtkomst** visas. Du kan klicka på **Öppna Azure Storage** för att öppna Storage Explorer.

   !["IntelliJ IDEA Storage Access Nekad2"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Knappen IntelliJ IDEA Storage Access Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ IDEA-program för att använda Azure Toolkit för IntelliJ

Du kan konvertera de befintliga Spark Scala-programmen som du skapade i IntelliJ IDEA för att vara kompatibla med Azure Toolkit för IntelliJ. Du kan sedan använda plugin-programmet för att skicka programmen till ett HDInsight Spark-kluster.

1. Öppna den associerade `.iml` filen för ett befintligt Spark Scala-program som skapades via IntelliJ IDEA.

2. På rotnivå är ett **modulelement** som följande text:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   Redigera elementet `UniqueKey="HDInsightTool"` så att **modulelementet** ser ut som följande text:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Spara ändringarna. Ditt program bör nu vara kompatibelt med Azure Toolkit för IntelliJ. Du kan testa det genom att högerklicka på projektnamnet i Project. Popup-menyn har nu alternativet **Skicka Spark Application till HDInsight**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort klustret som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan över HDInsight-kluster som visas väljer du **det ...** bredvid klustret som du skapade för den här artikeln.

1. Välj **Ta bort**. Välj **Ja**.

![Azure portal tar bort HDInsight-kluster](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder plugin-programmet Azure Toolkit for IntelliJ för att utveckla Apache Spark-program skrivna i [Scala](https://www.scala-lang.org/). Skickade dem sedan till ett HDInsight Spark-kluster direkt från IntelliJ-integrerade utvecklingsmiljön (IDE). Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI.

> [!div class="nextstepaction"]
> [Analysera Apache Spark-data med Power BI](apache-spark-use-bi-tools.md)
