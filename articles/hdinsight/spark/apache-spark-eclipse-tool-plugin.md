---
title: 'Azure Toolkit for Eclipse: Skapa Scala-program för HDInsight Spark '
description: Använd HDInsight-verktyg i Azure Toolkit for Eclipse för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från de Sol förmörkelse som IDE.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: b027ef911b65931f7c96ad45fae66656b3586730
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994631"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit for Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster

Använd HDInsight-verktyg i Azure Toolkit [för att](https://www.eclipse.org/) utveckla [Apache Spark](https://spark.apache.org/) program som är skrivna i [Scala](https://www.scala-lang.org/) och skicka dem till ett Azure HDInsight Spark-kluster direkt från de Sol förmörkelse IDE. Du kan använda plugin-programmet för HDInsight-verktyg på ett par olika sätt:

* För att utveckla och skicka ett Scala Spark-program på ett HDInsight Spark-kluster.
* För att få åtkomst till Azure HDInsight Spark kluster resurser.
* För att utveckla och köra ett Scala Spark-program lokalt.

> [!IMPORTANT]  
> Du kan använda det här verktyget för att skapa och skicka program endast för ett HDInsight Spark-kluster i Linux.

## <a name="prerequisites"></a>Förutsättningar

* Apache Spark kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit version 8, som används för en Sol förmörkelse IDE-körning. Du kan ladda ned den från [Oracle-webbplatsen](https://aka.ms/azure-jdks).
* Sol förmörkelse IDE. I den här artikeln används Sol förmörkelse Neon. Du kan installera det från [Sol förmörkelse-webbplatsen](https://www.eclipse.org/downloads/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Installera HDInsight-verktyg i Azure Toolkit for Eclipse och plugin-programmet Scala

### <a name="install-azure-toolkit-for-eclipse"></a>Installera Azure Toolkit for Eclipse

HDInsight-verktyg för Sol förmörkelse är tillgängligt som en del av Azure Toolkit for Eclipse. Installations anvisningar finns i [installera Azure Toolkit for Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Installera plugin-programmet Scala

När du öppnar Sol förmörkelse identifierar HDInsight-verktyget automatiskt om du har installerat Scala-plugin-programmet. Välj **OK** för att fortsätta och följ sedan anvisningarna för att installera plugin-programmet från Marketplace för Sol förmörkelse.

![Automatisk installation av Scala-plugin-programmet](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

Användaren kan antingen [Logga in i Azure-prenumerationen](#sign-in-to-your-azure-subscription)eller [Länka ett HDInsight-kluster](#link-a-cluster) med hjälp av Ambari användar namn/lösen ord eller domänanslutna autentiseringsuppgifter för start.

## <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Starta Sol förmörkelse IDE och öppna Azure Explorer. Välj **Visa vy**på **fönster** -menyn och välj sedan **annat**. I dialog rutan som öppnas expanderar du **Azure**, väljer **Azure Explorer**och väljer sedan **OK**.

   ![Visa dialog rutan Visa](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)
1. Högerklicka på **Azure** -noden och välj sedan **Logga**in.
1. I dialog rutan **Azure-inloggning** väljer du autentiseringsmetod, sedan **Logga in**och anger dina autentiseringsuppgifter för Azure.

   ![Dialog rutan för Azure-inloggning](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. När du har loggat in visar dialog rutan **Välj prenumerationer** alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Stäng dialog rutan genom att klicka på **Välj** .

   ![Dialog rutan Välj prenumerationer](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. På fliken **Azure Explorer** expanderar du **HDInsight** för att se HDInsight Spark-kluster under din prenumeration.

   ![HDInsight Spark-kluster i Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Du kan ytterligare expandera noden kluster namn för att se de resurser (till exempel lagrings konton) som är associerade med klustret.

   ![Expandera ett kluster namn om du vill se resurser](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett vanligt kluster med Ambari-hanterade användar namn. På samma sätt kan du länka med hjälp av domänen och användar namnet, till exempel `user1@contoso.com`, för ett domänanslutet HDInsight-kluster.

1. Välj **Länka ett kluster** från **Azure Explorer**.

   ![snabb meny för länk kluster](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Ange **kluster namn**, **användar namn** och **lösen ord**och klicka sedan på OK för att länka klustret. Du kan också ange lagrings konto, lagrings nyckel och välja lagrings behållare för lagrings Utforskaren som ska fungera i den vänstra trädvyn

   ![dialog rutan länka kluster](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Vi använder den länkade lagrings nyckeln, användar namnet och lösen ordet om klustret både är inloggat i Azure-prenumerationen och länkade ett kluster.
   > ![lagrings Utforskaren i Sol förmörkelse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Du kan se ett länkat kluster i **HDInsight** -noden när du har klickat på OK, om indatan är rätt. Nu kan du skicka ett program till det här länkade klustret.

   ![länkat kluster](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Du kan också ta bort länken mellan ett kluster och **Azure Explorer**.

   ![olänkat kluster](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster

1. I fönstret Sol förmörkelse i IDE-arbetsytan väljer du **Arkiv**, Välj **ny**och sedan **projekt**.

1. I guiden Nytt projekt expanderar du **HDInsight**, väljer **Spark på HDInsight (Scala)** och väljer sedan **Nästa**.

   ![Välja Scala-projekt (Spark on HDInsight)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Guiden skapa Scala-projekt identifierar automatiskt om du har installerat plugin-programmet Scala. Välj **OK** för att fortsätta ladda ned Scala-plugin-programmet och följ sedan anvisningarna för att starta om Sol förmörkelse.

   ![Scala-kontroll](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala2.png)

1. Ange följande värden i dialog rutan **nytt HDInsight Scala-projekt** och välj sedan **Nästa**:
   * Ange ett namn för projektet.
   * I **JRE** -ytan kontrollerar du att **Använd en körnings miljö JRE** är inställt på **Java-1,7** eller senare.
   * I **biblioteket för Spark-bibliotek** kan du välja **Använd Maven för att konfigurera Spark SDK** -alternativet.  Vårt verktyg integrerar rätt version för Spark SDK och Scala SDK. Du kan också välja alternativet för att **lägga till Spark SDK manuellt** , hämta och lägga till Spark SDK genom att manuellt.

   ![Dialog rutan nytt HDInsight Scala-projekt](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. I nästa dialog ruta väljer du **Slutför**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala-program för ett HDInsight Spark-kluster

1. I de Sol förmörkelse-IDE: en i paket Utforskaren expanderar du projektet som du skapade tidigare, högerklickar på **src**, pekar på **ny**och väljer sedan **annat**.

1. I dialog rutan **Välj en guide** expanderar du **Scala-guider**, väljer **Scala-objekt**och väljer sedan **Nästa**.

   ![Dialog rutan Välj en guide](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)
1. I dialog rutan **Skapa ny fil** anger du ett namn för objektet och väljer sedan **Slutför**.

   ![Dialog rutan skapa ny fil](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)
1. Klistra in följande kod i text redigeraren:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasb:///HVACOut")
        }        
    }
    ```

1. Kör programmet på ett HDInsight Spark-kluster:

   a. I Package Explorer högerklickar du på projekt namnet och väljer **Skicka Spark-program till HDInsight**.

   b. Ange följande värden i dialog rutan **Spark-överföring** och välj sedan **Skicka**:

   * För **kluster namn**väljer du det HDInsight Spark-kluster som du vill köra ditt program på.
   * Välj en artefakt från Sol förmörkelse-projektet eller Välj en från en hård disk. Standardvärdet beror på vilket objekt du högerklickar på från Package Explorer.
   * I list rutan för **huvud klass namn** visas alla objekt namn från projektet i guiden Skicka. Välj eller ange en som du vill köra. Om du har valt en artefakt från en hård disk måste du ange huvud klass namnet manuellt. 
   * Eftersom program koden i det här exemplet inte kräver några kommando rads argument eller referens jar v7 eller filer, kan du lämna de återstående text rutorna tomma.

     ![Dialog rutan för Spark-överföring](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Fliken **Spark-överföring** ska börja visa förloppet. Du kan stoppa programmet genom att välja knappen röd i fönstret **Spark-överföring** . Du kan också visa loggarna för det här specifika programmet genom att välja ikonen för jordglob (med den blå rutan i bilden).

   ![Spark-överförings fönster](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Få åtkomst till och hantera HDInsight Spark-kluster med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse

Du kan utföra olika åtgärder med hjälp av HDInsight-verktyg, inklusive åtkomst till jobbets utdata.

### <a name="access-the-job-view"></a>Öppna vyn jobb

1. Expandera **HDInsight**i Azure Explorer, expandera kluster namnet Spark och välj sedan **jobb**.

   ![Nod i vyn jobb](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Välj noden **jobb** . Om Java-versionen är lägre än **1,8**så påminnelser HDInsight-verktyg automatiskt att du installerar E-plugin-programmet **(FX)** . Välj **OK** för att fortsätta och följ sedan guiden för att installera den från Marketplace för Sol förmörkelse och starta om Sol förmörkelse. 

   ![Installera E-klipp (FX)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Öppna vyn jobb från noden **jobb** . I den högra rutan visar fliken **Spark Job View** alla program som kördes på klustret. Välj namnet på det program som du vill visa mer information om.

   ![Programinformation](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Du kan sedan vidta någon av följande åtgärder:

   * Hovra över jobb diagrammet. Den visar grundläggande information om jobbet som körs. Välj jobb diagrammet och du kan se de faser och information som varje jobb genererar.

     ![Information om jobb steg](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Välj fliken **logg** om du vill visa vanliga loggar, inklusive **driv rutin stderr**, **driv Rutins-STDOUT**och **katalog information**.

     ![Logg information](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Öppna gränssnittet Spark-historik och Apache Hadoop garn gränssnitt (på program nivå) genom att välja hyperlänkarna överst i fönstret.

### <a name="access-the-storage-container-for-the-cluster"></a>Åtkomst till lagrings behållaren för klustret

1. I Azure Explorer expanderar du **HDInsight** -rotnoden för att se en lista över HDInsight Spark-kluster som är tillgängliga.

1. Expandera kluster namnet om du vill se lagrings kontot och standard lagrings behållaren för klustret.

   ![Lagrings konto och standard lagrings behållare](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Välj det lagrings behållar namn som är associerat med klustret. I den högra rutan dubbelklickar du på mappen **HVAC** . Öppna en **av filerna för att se** resultatet av programmet.

### <a name="access-the-spark-history-server"></a>Få åtkomst till Spark historik Server

1. I Azure Explorer högerklickar du på namnet på ditt Spark-kluster och väljer sedan **Öppna användar gränssnitt för Spark-historik**. När du uppmanas till det anger du administratörens autentiseringsuppgifter för klustret. Du angav dessa när du etablerade klustret.

1. På instrument panelen för Spark-historiken använder du program namnet för att leta efter programmet som du precis har kört. I föregående kod ställer du in program namnet med hjälp av `val conf = new SparkConf().setAppName("MyClusterApp")`. Därför var ditt Spark-programnamn **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Starta Apache Ambari-portalen

1. I Azure Explorer högerklickar du på namnet på ditt Spark-kluster och väljer sedan **Öppna kluster hanteringsportal (Ambari)** .

1. När du uppmanas till det anger du administratörens autentiseringsuppgifter för klustret. Du angav dessa när du etablerade klustret.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard visar HDInsight-verktyget i Azure Toolkit for Eclipse Spark-klustren från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill använda för att komma åt klustret.

1. I Azure Explorer högerklickar du på noden **Azure** -rotnod och väljer sedan **Hantera prenumerationer**.

1. I dialog rutan avmarkerar du kryss rutorna för den prenumeration som du inte vill komma åt och väljer sedan **Stäng**. Du kan också välja **Logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Köra ett Spark Scala-program lokalt

Du kan använda HDInsight-verktyg i Azure Toolkit for Eclipse för att köra Spark Scala-program lokalt på din arbets Station. Normalt behöver dessa program inte åtkomst till kluster resurser, till exempel en lagrings behållare, och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Krav

När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag som beskrivs i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Detta undantag beror på att **WinUtils. exe** saknas i Windows.

För att lösa det här felet måste du [Hämta den körbara filen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**och sedan lägga till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln till **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Köra ett lokalt Spark Scala-program

1. Starta Sol förmörkelse och skapa ett projekt. I dialog rutan **nytt projekt** gör du följande val och väljer sedan **Nästa**.

   * Välj **HDInsight** i den vänstra fönsterrutan.
   * I den högra rutan väljer du **Spark på lokala HDInsight-testexempel (Scala)** .

   ![Dialogrutan Nytt projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Om du vill ange projekt information följer du steg 3 till 6 i det tidigare avsnittet [Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Mallen lägger till en exempel kod (**LogQuery**) under **src** -mappen som du kan köra lokalt på datorn.

   ![Plats för LogQuery](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Högerklicka på programmet **LogQuery** , peka på **Kör som**och välj sedan **ett Scala program**. Utdata som detta visas på fliken **konsol** :

   ![Spark-programmets lokala körnings resultat](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Roll som endast läsare

När användare skickar jobb till ett kluster med endast läsar roll behörighet krävs Ambari-autentiseringsuppgifter.

### <a name="link-cluster-from-context-menu"></a>Länka kluster från snabb menyn

1. Logga in med roll kontot endast läsare.

2. Expandera **HDInsight** i **Azure Explorer**för att Visa HDInsight-kluster som finns i din prenumeration. Kluster som har marker ATS med **rollen "roll: läsare"** har bara behörighet som endast läsare.

    ![HDInsight Spark-kluster i Azure Explorer-roll läsaren](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Högerklicka på klustret med roll behörigheten endast läsare. Välj **länka det här klustret** från snabb menyn för att länka klustret. Ange Ambari användar namn och lösen ord.

    ![HDInsight Spark-kluster i Azure Explorer-länk](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Om klustret har länkats kommer HDInsight att uppdateras.
   Klustrets steg kommer att länkas.
  
    ![HDInsight Spark-kluster i Azure Explorer länkade](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Länka kluster genom att expandera jobb-noden

1. Klicka på noden **jobb** , fönstret **åtkomst nekad åtkomst till kluster jobb** .

2. Klicka på **länka det här klustret** för att länka klustret.

    ![HDInsight Spark-kluster i Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Länka kluster från Spark-fönstret

1. Skapa ett HDInsight-projekt.

2. Högerklicka på paketet. Välj sedan **Skicka Spark-program till HDInsight**.

   ![HDInsight Spark-kluster i Azure Explorer skicka](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Välj ett kluster som har behörighet för endast läsare för **kluster namn**. Varnings meddelandet visas. Du kan klicka på **Länka** klustret om du vill länka klustret.

   ![HDInsight Spark-kluster i Azure Explorer länka detta](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Visa lagrings konton

* För kluster med behörighet för endast läsare, klickar du på noden **lagrings konton** , fönstret **åtkomst nekas för lagring** .

   ![HDInsight Spark-kluster i Azure Explorer-lagring](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![HDInsight Spark-kluster i Azure Explorer nekas](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* För länkade kluster klickar du på noden **lagrings konton** , fönstret **lagrings åtkomst nekad** öppnas.

   ![HDInsight Spark-kluster i Azure Explorer denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Kända problem

När du länkar ett kluster kan jag föreslå att du anger autentiseringsuppgifter för lagring.

![Interaktiv inloggning](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Det finns två lägen för att skicka jobben. Om du har angett autentiseringsuppgifter för lagring kommer batch-läget att användas för att skicka jobbet. Annars kommer interaktivt läge att användas. Om klustret är upptaget kan du få felet nedan.

![Sol förmörkelse får ett fel meddelande när klustret är upptaget](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Sol förmörkelse får ett fel meddelande när klustret är upptaget")

![Sol förmörkelse får ett fel meddelande när klustrets upptagna garn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "Sol förmörkelse får ett fel meddelande när klustrets upptagna garn")

## <a name="seealso"></a>Se även

* [: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit for IntelliJ för att skapa och skicka Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
