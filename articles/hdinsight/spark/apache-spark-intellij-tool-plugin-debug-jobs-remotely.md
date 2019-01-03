---
title: 'Azure Toolkit för IntelliJ: Felsöka via en fjärranslutning i HDInsight Spark-program '
description: Lär dig hur använda HDInsight-verktyg i Azure Toolkit för IntelliJ för att felsöka Spark-program som körs på HDInsight-kluster via VPN.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: e57257c6965f0da8c2d6ce990d2425847b73884f
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605802"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Felsöka Apache Spark-program via fjärranslutning i HDInsight via VPN-anslutning med hjälp av Azure Toolkit för IntelliJ

Vi rekommenderar att felsöka [Apache Spark](https://spark.apache.org/) program via fjärranslutning via SSH. Anvisningar finns i [Fjärrfelsöka Apache Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Den här artikeln innehåller stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ för att skicka in ett Spark-jobb på ett HDInsight Spark-kluster och felsöka den via en fjärranslutning från din stationära dator. Om du vill utföra dessa uppgifter, måste du utföra följande anvisningar:

1. Skapa en plats-till-plats eller punkt-till-plats Azure virtuellt nätverk. I det här dokumentet förutsätter vi att du använder ett nätverk för plats-till-plats.
1. Skapa ett Spark-kluster i HDInsight som ingår i det virtuella nätverket för plats-till-plats.
1. Kontrollera anslutningen mellan klustrets huvudnod och skrivbordet.
1. Skapa ett Scala-program i IntelliJ IDEA och sedan konfigurera den för fjärrfelsökning.
1. Köra och felsöka programmet.

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Mer information finns i [skaffa en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Ett Apache Spark-kluster i HDInsight**. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Du kan installera det från den [Oracle webbplats](https://aka.ms/azure-jdks).
* **IntelliJ IDEA**. Den här artikeln använder version 2017.1. Du kan installera det från den [JetBrains webbplats](https://www.jetbrains.com/idea/download/).
* **HDInsight-verktygen i Azure Toolkit för IntelliJ**. HDInsight tools för IntelliJ är tillgängliga som en del av Azure Toolkit för IntelliJ. Anvisningar om hur du installerar Azure Toolkit finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Logga in på Azure-prenumerationen från IntelliJ IDEA**. Följ instruktionerna i [Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md).
* **Undantag lösning**. När du kör Spark Scala-programmet för fjärrfelsökning på en Windows-dator, kan du få ett undantag. Det här undantaget förklaras i [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) och inträffar på grund av en saknad WinUtils.exe-fil i Windows. Du kan undvika det här felet, måste du [ladda ned den körbara filen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg till en **HADOOP_HOME** miljövariabeln, och ange sedan värdet för variabeln för att **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Steg 1: Skapa en Azure-nätverk
Följ instruktionerna från följande länkar för att skapa en Azure-nätverk och kontrollera anslutningen mellan din stationära dator och det virtuella nätverket:

* [Skapa ett VNet med en plats-till-plats VPN-anslutning med Azure portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Skapa ett VNet med en plats-till-plats VPN-anslutning med hjälp av PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Steg 2: Skapa ett HDInsight Spark-kluster
Vi rekommenderar att du också skapa ett Apache Spark-kluster i Azure HDInsight som är en del av Azure-nätverk som du skapade. Använd informationen i [skapa Linux-baserade kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Som en del av valfri konfiguration, Välj den Azure-nätverk som du skapade i föregående steg.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Steg 3: Kontrollera anslutningen mellan klustrets huvudnod och skrivbordet
1. Hämta IP-adressen för huvudnoden. Öppna Ambari UI för klustret. Klusterbladet väljer du **instrumentpanelen**.

    ![Välj instrumentpanelen i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Ambari UI, Välj **värdar**.

    ![Välj värdar i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. Du kan se en lista över huvudnoder, arbetsnoder och zookeeper-noder. Huvudnoderna har en **hn*** prefix. Välj den första huvudnoden.

    ![Hitta huvudnoden i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Från den **sammanfattning** rutan längst ned på sidan som öppnas, kopiera den **IP-adress** för huvudnoden och **värdnamn**.

    ![Hitta IP-adressen i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Lägg till IP-adressen och värdnamnet för huvudnoden till det **värdar** filen på datorn där du vill köra och felsöka Spark-jobbet. På så sätt kan du kommunicera med klustrets huvudnod via IP-adress, samt värdnamnet.

   a. Öppna en fil i anteckningar med förhöjd behörighet. Från den **filen** menyn och välj **öppna**, och sedan söka efter i hosts-filen. I en Windows-dator är platsen **C:\Windows\System32\Drivers\etc\hosts**.

   b. Lägg till följande information till den **värdar** fil:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. Kontrollera att du kan pinga huvudnoderna med hjälp av IP-adress, samt värdnamnet från den dator som du är ansluten till Azure-nätverket som används av HDInsight-klustret.
1. Använda SSH för att ansluta till klustrets huvudnod genom att följa instruktionerna i [ansluta till ett HDInsight-kluster med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Pinga IP-adressen för den stationära datorn från klustrets huvudnod. Testa anslutningen till båda IP-adresserna som tilldelats datorn:

    - En för nätverksanslutningen
    - En för virtuella Azure-nätverket

1. Upprepa stegen för andra huvudnoden.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Steg 4: Skapa ett Apache Spark Scala-program med hjälp av HDInsight-verktyg i Azure Toolkit för IntelliJ och konfigurera den för fjärrfelsökning
1. Öppna IntelliJ IDEA och skapa ett nytt projekt. Gör följande i dialogrutan **Nytt projekt**:

    ![Välj projektmallen i IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Välj **HDInsight** > **Spark i HDInsight (Scala)**.

    b. Välj **Nästa**.
1. I nästa **nytt projekt** dialogrutan gör du följande och välj sedan **Slutför**:

    - Ange ett projektnamn och en plats.

    - I listrutan **Projekt-SDK** väljer du **Java 1.8** för Spark 2.x-klustret, eller **Java 1.7** för Spark 1.x-klustret.

    - I den **Spark-version** listrutan, Scala projektguiden är rätt version för Spark-SDK och Scala-SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Välj Projektversion SDK och Spark](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Spark-projekt skapar automatiskt en artefakt. Om du vill visa artefakten, gör du följande:

    a. I menyn **Arkiv** väljer du **Projektstruktur**.

    b. I den **projektstruktur** dialogrutan **artefakter** att visa den standard-artefakt som har skapats. Du kan också skapa egna artefakt genom att välja plus-tecknet (**+**).

   ![Skapa JAR-fil](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Lägg till bibliotek i projektet. Om du vill lägga till ett bibliotek, gör du följande:

    a. Högerklicka på projektnamnet i projektträdet och välj sedan **öppna modulinställningarna**. 

    b. I den **projektstruktur** dialogrutan **bibliotek**, Välj den (**+**) symbol och välj sedan **från Maven** .

    ![Lägga till ett bibliotek](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. I den **hämta biblioteket från Maven lagringsplats** dialogrutan rutan, Sök efter och Lägg till följande bibliotek:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Kopiera `yarn-site.xml` och `core-site.xml` från klustret gå noden och lägga till dem i projektet. Använd följande kommandon för att kopiera filerna. Du kan använda [Cygwin](https://cygwin.com/install.html) att köra följande `scp` kommandon för att kopiera filer från klustret huvudnoder:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Eftersom vi redan lagts till klustrets huvudnod IP-adress och värdnamn för hosts-filen på skrivbordet, vi kan använda den `scp` kommandon på följande sätt:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Om du vill lägga till de här filerna i projektet, kopierar du dem under den **/src** mapp i ditt projektträdet, till exempel `<your project directory>\src`.
1. Uppdatera den `core-site.xml` filen för att göra följande ändringar:

   a. Ersätt den krypterade nyckeln. Den `core-site.xml` -filen innehåller den krypterade nyckeln till lagringskontot som är associerade med klustret. I den `core-site.xml` -fil som du lagt till i projektet, ersätter den krypterade nyckeln med faktiska lagringskontots åtkomstnyckel som är associerade med standardkontot för lagring. Mer information finns i [hantera dina lagringsåtkomstnycklar](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Ta bort följande poster från `core-site.xml`:

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
   c. Spara filen.
1. Lägg till main-klass för ditt program. Från den **Projektutforskaren**, högerklicka på **src**, peka på **New**, och välj sedan **Scala klass**.

    ![Välj main-klass](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. I den **Skapa ny klass med Scala** dialogrutan rutan, ange ett namn, Välj **objekt** i den **typ** och väljer sedan **OK**.

    ![Skapa ny Scala-klass](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. I den `MyClusterAppMain.scala` filen, klistra in följande kod. Den här koden skapar Spark kontext och öppnar en `executeJob` metod från den `SparkSample` objekt.

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

1. Upprepa steg 8 och 9 för att lägga till ett nytt Scala-objekt som kallas `*SparkSample`. Lägg till följande kod i den här klassen. Den här koden läser data från HVAC.csv (tillgängligt i alla HDInsight Spark-kluster). Hämtas de rader som endast innehåller en siffra i den sjunde kolumnen i CSV-filen och sedan skriver utdata till **/HVACOut** under standardbehållare för lagring för klustret.

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
1. Upprepa steg 8 och 9 för att lägga till en ny klass med namnet `RemoteClusterDebugging`. Den här klassen implementerar test Spark-ramverket som används för att felsöka program. Lägg till följande kod till den `RemoteClusterDebugging` klass:

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

     Det finns några viktiga saker att tänka på:

      * För `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, se till att Spark-sammansättningen JAR är tillgängliga i klusterlagringen på den angivna sökvägen.
      * För `setJars`, ange den plats där artefakten JAR skapas. Det är vanligtvis `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. I den`*RemoteClusterDebugging` klass, högerklicka på den `test` nyckelord och välj sedan **skapa RemoteClusterDebugging Configuration**.

    ![Skapa en fjärransluten konfiguration](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. I den **skapa RemoteClusterDebugging Configuration** dialogrutan, ange ett namn för konfigurationen och välj sedan **testa typ** som den **testnamn**. Lämna de andra värdena som standardinställningar. Tryck på **Tillämpa** och välj sedan **OK**.

    ![Lägg till konfigurationsinformationen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Du bör nu se en **Remote kör** configuration listrutan på menyraden.

    ![Remote utskriftsjobb körningslistan](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Steg 5: Kör programmet i felsökningsläge
1. Öppna i projektet IntelliJ IDEA `SparkSample.scala` och skapa en brytpunkt bredvid `val rdd1`. I den **skapa brytpunkt för** popup-menyn och välj **rad i funktionen executeJob**.

    ![Lägga till en brytpunkt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. För att köra programmet, Välj den **Felsök kör** knappen bredvid den **Remote kör** configuration nedrullningsbara listan.

    ![Välj knappen Felsök kör](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. När programkörningen når brytpunkten kan du se en **felsökare** flik längst ned i fönstret.

    ![Visa fliken felsökare](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Om du vill lägga till en bevakning, Välj den (**+**) ikonen.

    ![Välj den ikonen +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    I det här exemplet avbrutits programmet innan du variabeln `rdd1` skapades. Med den här watch kan vi kan se de fem första raderna i variabeln `rdd`. Välj **Retur**.

    ![Kör programmet i felsökningsläge](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Det som visas i föregående bild är att du kan fråga terabyte data och felsökning vid körning, hur dina program utvecklas. Till exempel i de utdata som visas i föregående bild, ser du att den första raden i utdata är en rubrik. Baserat på dessa utdata kan ändra du din programkod för att hoppa över rubrikraden, om det behövs.
1. Nu kan du välja den **återuppta programmet** ikon för att fortsätta med ditt program körs.

    ![Välj Fortsätt Program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Om programmet har slutförts bör du se utdata som liknar följande:

    ![Konsolutdata](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

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
* [Felsöka Apache Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktygen i Azure Toolkit för Eclipse för att skapa Apache Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Spåra och felsöka jobb som körs på ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
