---
title: 'Azure Toolkit: Felsöka Apache Spark-appar på distans - Azure HDInsight'
description: Lär dig hur du använder HDInsight Tools i Azure Toolkit för IntelliJ för att fjärrsöka Spark-program som körs på HDInsight-kluster via VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935024"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans i HDInsight via VPN

Vi rekommenderar att du felsöker [Apache Spark-program](https://spark.apache.org/) på distans via SSH. Instruktioner finns [i Fjärrfelsöka Apache Spark-program i ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Den här artikeln innehåller steg-för-steg-vägledning om hur du använder HDInsight-verktygen i Azure Toolkit för IntelliJ för att skicka ett Spark-jobb på ett HDInsight Spark-kluster och sedan felsöka det på distans från din stationära dator. För att kunna utföra dessa uppgifter måste du utföra följande steg på hög nivå:

1. Skapa ett virtuellt nätverk för plats till plats eller punkt till plats. Stegen i det här dokumentet förutsätter att du använder ett nätverk från plats till plats.
1. Skapa ett Spark-kluster i HDInsight som är en del av det virtuella nätverket plats till plats.
1. Kontrollera anslutningen mellan klusterhuvudnoden och skrivbordet.
1. Skapa ett Scala-program i IntelliJ IDEA och konfigurera det för fjärrfelsökning.
1. Kör och felsöka programmet.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Mer information finns i [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Ett Apache Spark-kluster i HDInsight**. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java utveckling kit**. Du kan installera den från [Oracles webbplats](https://aka.ms/azure-jdks).
* **IntelliJ IDÉ**. I den här artikeln används version 2017.1. Du kan installera den från [JetBrains webbplats](https://www.jetbrains.com/idea/download/).
* **HDInsight-verktyg i Azure Toolkit för IntelliJ**. HDInsight-verktyg för IntelliJ är tillgängliga som en del av Azure Toolkit för IntelliJ. Instruktioner om hur du installerar Azure Toolkit finns i [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Logga in på din Azure-prenumeration från IntelliJ IDEA**. Följ instruktionerna i [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md).
* **Undantagsarbetsning**. När du kör Spark Scala-programmet för fjärrfelsökning på en Windows-dator kan du få ett undantag. Det här undantaget förklaras i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) och inträffar på grund av en saknad WinUtils.exe-fil i Windows. För att komma runt det här felet måste du hämta [Winutils.exe](https://github.com/steveloughran/winutils) till en plats som **C:\WinUtils\bin**. Lägg till en **HADOOP_HOME** miljövariabel och ange sedan variabelns värde på **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Steg 1: Skapa ett virtuellt Azure-nätverk

Följ instruktionerna från följande länkar för att skapa ett virtuellt Azure-nätverk och verifiera sedan anslutningen mellan din stationära dator och det virtuella nätverket:

* [Skapa ett VNet med en PLATS-till-plats-VPN-anslutning med Azure-portalen](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Skapa ett VNet med en VPN-anslutning från plats till plats med PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Steg 2: Skapa ett HDInsight Spark-kluster

Vi rekommenderar att du även skapar ett Apache Spark-kluster i Azure HDInsight som ingår i det virtuella Azure-nätverk som du har skapat. Använd informationen som finns i [Skapa Linux-baserade kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Som en del av valfri konfiguration väljer du det virtuella Azure-nätverk som du skapade i föregående steg.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Steg 3: Verifiera anslutningen mellan klusterhuvudnoden och skrivbordet

1. Hämta IP-adressen för huvudnoden. Öppna Ambari UI för klustret. Välj **Instrumentpanel**i klusterbladet .

    ![Välj instrumentpanel i Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Välj **Värdar**i Ambari-användargränssnittet .

    ![Välj värdar i Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Du ser en lista över huvudnoder, arbetsnoder och zookeepernoder. Huvudnoderna har ett **hn*** prefix. Markera den första huvudnoden.

    ![Hitta huvudnoden i Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. Kopiera **IP-adressen** för huvudnoden och **värdnamnet**från **fönstret Sammanfattning** längst ned på sidan som öppnas.

    ![Hitta IP-adressen i Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Lägg till IP-adressen och värdnamnet för huvudnoden i **hosts-filen** på datorn där du vill köra och fjärrsã¶k felsöka Spark-jobbet. På så sätt kan du kommunicera med huvudnoden med hjälp av IP-adressen samt värdnamnet.

   a. Öppna en Anteckningarfil med förhöjda behörigheter. På **Arkiv-menyn** väljer du **Öppna**och hittar sedan värdfilens plats. På en Windows-dator är platsen **C:\Windows\System32\Drivers\etc\hosts**.

   b. Lägg till följande **hosts** information i hosts-filen:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Från datorn som du anslöt till det virtuella Azure-nätverket som används av HDInsight-klustret kontrollerar du att du kan pinga huvudnoderna med hjälp av IP-adressen samt värdnamnet.

1. Använd SSH för att ansluta till klusterhuvudnoden genom att följa instruktionerna i [Anslut till ett HDInsight-kluster med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Från klusterhuvudnoden pingar du IP-adressen för den stationära datorn. Testa anslutningen till båda IP-adresserna som tilldelats datorn:

    - En för nätverksanslutningen
    - En för det virtuella Azure-nätverket

1. Upprepa stegen för den andra huvudnoden.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Steg 4: Skapa ett Apache Spark Scala-program med hjälp av HDInsight Tools i Azure Toolkit för IntelliJ och konfigurera det för fjärrfelsökning

1. Öppna IntelliJ IDEA och skapa ett nytt projekt. Gör följande i dialogrutan **Nytt projekt**:

    ![Välj den nya projektmallen i IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Välj **HDInsight** > **Spark på HDInsight (Scala)**.

    b. Välj **Nästa**.
1. Gör följande i nästa dialogrutan **Nytt projekt** och välj sedan **Slutför:**

    - Ange ett projektnamn och en plats.

    - I listrutan **Projekt-SDK** väljer du **Java 1.8** för Spark 2.x-klustret, eller **Java 1.7** för Spark 1.x-klustret.

    - I listrutan **Spark-version** integrerar scala-projektskapandeguiden rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Välj projektet SDK- och Spark-version](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Spark-projektet skapar automatiskt en artefakt åt dig. Så här visar du artefakten:

    a. Välj **Projektstruktur**på **Arkiv-menyn** .

    b. I dialogrutan **Projektstruktur** väljer du **Artefakter** för att visa standardartefakten som skapas. Du kan också skapa en egen artefakt genom att välja plustecknet (**+**).

   ![IntelliJ IDEA artefakter skapa burk](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Lägg till bibliotek i projektet. Så här lägger du till ett bibliotek:

    a. Högerklicka på projektnamnet i projektträdet och välj sedan **Öppna modulinställningar**.

    b. Välj Bibliotek i dialogrutan **Projektstruktur**, välj**+** symbolen ( ) och välj sedan **Från Maven**. **Project Structure**

    ![IntelliJ IDEA ladda ner bibliotek](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Sök efter och lägg till följande bibliotek i dialogrutan **Hämta bibliotek från Maven-databas:**

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Kopiera `yarn-site.xml` `core-site.xml` och från klusterhuvudnoden och lägg till dem i projektet. Använd följande kommandon för att kopiera filerna. Du kan använda [Cygwin](https://cygwin.com/install.html) `scp` för att köra följande kommandon för att kopiera filerna från klusterhuvudnoderna:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Eftersom vi redan har lagt till IP-adressen för klusterhuvudnoden och värdnamnen för hosts-filen på skrivbordet kan vi använda `scp` kommandona på följande sätt:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Om du vill lägga till dessa filer i projektet kopierar du `<your project directory>\src`dem under mappen **/src** i projektträdet, till exempel .

1. Uppdatera `core-site.xml` filen så att du gör följande ändringar:

   a. Byt ut den krypterade nyckeln. Filen `core-site.xml` innehåller den krypterade nyckeln till lagringskontot som är associerat med klustret. I `core-site.xml` filen som du lade till i projektet ersätter du den krypterade nyckeln med den faktiska lagringsnyckeln som är associerad med standardlagringskontot. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Ta bort följande `core-site.xml`poster från:

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Spara filen.

1. Lägg till huvudklassen för ditt program. Högerklicka på **Src**i **Project Explorer**och peka på **Nytt**och välj sedan **klassen Scala**.

    ![IntelliJ IDEA Välj huvudklass](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Ange ett namn i dialogrutan **Skapa ny scala-klass,** välj **Objekt** i rutan **Sort** och välj sedan **OK**.

    ![IntelliJ IDEA Skapa ny Scala-klass](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. Klistra `MyClusterAppMain.scala` in följande kod i filen. Den här koden skapar Spark-kontexten och öppnar en `executeJob` metod från `SparkSample` objektet.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Upprepa steg 8 och 9 om du `*SparkSample`vill lägga till ett nytt Scala-objekt med namnet . Lägg till följande kod i den här klassen. Den här koden läser data från HVAC.csv (finns i alla HDInsight Spark-kluster). Raderna som bara har en siffra i den sjunde kolumnen i CSV-filen hämtas och sedan skrivs utdata till **/HVACOut** under standardlagringsbehållaren för klustret.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Upprepa steg 8 och 9 om `RemoteClusterDebugging`du vill lägga till en ny klass som heter . Den här klassen implementerar Spark-testramverket som används för att felsöka programmen. Lägg till följande `RemoteClusterDebugging` kod i klassen:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Det finns ett par viktiga saker att notera:

      * För `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`kontrollerar du att Spark-monterings-JAR är tillgänglig på klusterlagringen vid den angivna sökvägen.
      * För `setJars`anger du den plats där artefakt-JAR skapas. Vanligtvis är `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`det .

1. Högerklicka`*RemoteClusterDebugging` på nyckelordet i `test` klassen och välj sedan **Konfigurera Fjärrkrypningskonfiguration**.

    ![IntelliJ IDEA Skapa en fjärrkonfiguration](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Ange ett namn för konfigurationen i dialogrutan **Skapa fjärrkrypningskonfiguration** och välj sedan **Testslag** som **testnamn**. Lämna alla andra värden som standardinställningar. Tryck på **Tillämpa** och välj sedan **OK**.

    ![Skapa konfiguration för fjärrklusterdebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Du bör nu se en listruta för **fjärrkörningskonfiguration** i menyraden.

    ![IntelliJ Listrutan Fjärrkörning](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Steg 5: Kör programmet i felsökningsläge

1. Öppna och skapa en brytpunkt `SparkSample.scala` bredvid `val rdd1`i Projektet IntelliJ IDEA. På popup-menyn **Skapa brytpunkt för** väljer du linje i **funktionsutnröman.**

    ![IntelliJ IDEA Lägg till en brytpunkt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Om du vill köra programmet väljer du knappen **Felsökningskörning** bredvid listrutan **Fjärrkörningskonfiguration.**

    ![IntelliJ IDEA Välj knappen Felsökningskörning](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. När programkörningen når brytpunkten visas en **felsökningsflik** i det nedre fönstret.

    ![IntelliJ IDEA Visa fliken Felsökning](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Om du vill lägga**+** till en klocka väljer du ( ) ikonen.

    ![IntelliJ felsökning-tillägg-watch-variabel](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    I det här exemplet bröts `rdd1` programmet innan variabeln skapades. Genom att använda den här klockan kan vi `rdd`se de första fem raderna i variabeln . Välj **Ange**.

    ![IntelliJ Kör programmet i felsökningsläge](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Vad du ser i föregående bild är att vid körning kan du fråga terabyte data och felsöka hur ditt program fortskrider. I utdata som visas i föregående bild kan du till exempel se att den första raden i utdata är ett sidhuvud. Baserat på den här utdata kan du ändra programkoden för att hoppa över rubrikraden om det behövs.

1. Du kan nu välja ikonen **Återuppta program** för att fortsätta med programkörningen.

    ![IntelliJ IDEA Välj Återuppta Program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Om programmet har slutförts bör du se utdata som följande:

    ![Utdata från intellij IDEA-felsökningskonsolen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Nästa steg

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
* [Använd Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använd HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i ett Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Spåra och felsöka jobb som körs på ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
