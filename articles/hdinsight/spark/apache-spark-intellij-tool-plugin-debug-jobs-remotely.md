---
title: 'Azure Toolkit för IntelliJ: felsöka program via fjärranslutning i HDInsight Spark | Microsoft Docs'
description: Lär dig hur använda HDInsight Tools i Azure Toolkit för IntelliJ felsöka Spark-program som körs på HDInsight-kluster via VPN.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6ca69ccab6c9b526c86f6f7a8998089e52c6c939
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Använda Azure Toolkit för IntelliJ för att felsöka Spark-program från en fjärrdator i HDInsight via VPN-anslutning

Vi rekommenderar att felsöka spark-program via fjärranslutning via SSH. Instruktioner finns i [felsöka Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Den här artikeln innehåller stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ att skicka ett Spark-jobb på ett HDInsight Spark-kluster och felsöka den via fjärranslutning från din dator. Om du vill utföra dessa uppgifter, måste du utföra följande anvisningar:

1. Skapa en plats-till-plats eller en punkt-till-plats virtuella Azure-nätverket. Stegen i det här dokumentet förutsätts att du använder ett plats-till-plats-nätverk.
2. Skapa ett Spark-kluster i HDInsight som ingår i det virtuella nätverket för plats-till-plats.
3. Kontrollera anslutningen mellan klustrets huvudnod och ditt skrivbord.
4. Skapa en Scala-program i IntelliJ IDEA och konfigurera den för fjärrfelsökning.
5. Kör och felsöka programmet.

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Mer information finns i [skaffa en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Ett Apache Spark-kluster i HDInsight**. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Du kan installera det från den [Oracle webbplats](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. Den här artikeln använder version 2017.1. Du kan installera det från den [JetBrains webbplats](https://www.jetbrains.com/idea/download/).
* **HDInsight-verktyg i Azure Toolkit för IntelliJ**. HDInsight-verktyg för IntelliJ är tillgängliga som en del av Azure Toolkit för IntelliJ. Anvisningar för hur du installerar Azure Toolkit finns [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Logga in på Azure-prenumerationen från IntelliJ IDEA**. Följ instruktionerna i [Använd Azure Toolkit för IntelliJ till att skapa Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md).
* **Undantag lösning**. När du kör programmet Spark Scala för fjärrfelsökning på en Windows-dator, kan du få ett undantag. Det här undantaget förklaras i [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) och uppstår på grund av en saknad fil WinUtils.exe i Windows. Du kan undvika det här felet måste du [ladda ned den körbara filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats som **C:\WinUtils\bin**. Lägg till en **HADOOP_HOME** miljövariabeln, och sedan ange värdet för variabeln för att **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Steg 1: Skapa ett virtuellt Azure-nätverk
Följ anvisningarna i följande länkar för att skapa ett Azure-nätverk och kontrollera anslutningen mellan din dator och det virtuella nätverket:

* [Skapa ett VNet med en plats-till-plats VPN-anslutning med hjälp av Azure portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Skapa ett VNet med en plats-till-plats VPN-anslutning med hjälp av PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Steg 2: Skapa ett HDInsight Spark-kluster
Vi rekommenderar att du också skapa ett Apache Spark-kluster i Azure HDInsight som ingår i virtuella Azure-nätverket som du skapade. Använd informationen i [skapa Linux-baserade kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Som en del av valfri konfiguration, Välj det virtuella Azure-nätverket som du skapade i föregående steg.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Steg 3: Kontrollera anslutningen mellan klustrets huvudnod och skrivbordet
1. Hämta IP-adressen för huvudnoden. Öppna Ambari UI för klustret. Klusterbladet Välj **instrumentpanelen**.

    ![Välj en instrumentpanel i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. Ambari UI, Välj **värdar**.

    ![Välj värdar i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Du kan se en lista över huvudnoderna, arbetarnoder och zookeeper-noder. Huvudnoderna har en **hn*** prefix. Välj den första huvudnoden.

    ![Hitta huvudnoden i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Från den **sammanfattning** rutan längst ned på sidan som öppnas, kopiera den **IP-adress** av huvudnoden och **värdnamn**.

    ![Hitta IP-adressen i Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Lägg till IP-adressen och värdnamnet för huvudnoden till den **värdar** fil på datorn där du vill köra och felsöka Spark-jobbet. På så sätt kan du kommunicera med huvudnoden via IP-adress, samt värdnamnet.

   a. Öppna en fil i anteckningar med förhöjd behörighet. Från den **filen** väljer du **öppna**, och sedan söka efter hosts-filen. I en Windows-dator är platsen **C:\Windows\System32\Drivers\etc\hosts**.

   b. Lägg till följande information för den **värdar** fil:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. Kontrollera att du kan pinga huvudnoderna med hjälp av IP-adress, samt värdnamnet från datorn som du har anslutit till det virtuella Azure-nätverket som används av HDInsight-klustret.
7. Använda SSH för att ansluta till klustrets huvudnod genom att följa instruktionerna i [Anslut till ett HDInsight-kluster med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Pinga IP-adressen för den stationära datorn från klustrets huvudnod. Testa anslutningen till båda IP-adresser som tilldelats datorn:

    - En för nätverksanslutningen
    - En för det virtuella Azure-nätverket

8. Upprepa stegen för den andra huvudnoden.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Steg 4: Skapa ett Spark Scala-program med hjälp av HDInsight-verktyg i Azure Toolkit för IntelliJ och konfigurera den för fjärrfelsökning
1. Öppna IntelliJ IDEA och skapa ett nytt projekt. I den **nytt projekt** dialogrutan Gör följande:

    ![Välj den nya projektmallen för i IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Välj **HDInsight** > **Spark i HDInsight (Scala)**.

    b. Välj **Nästa**.
2. I nästa **nytt projekt** dialogrutan gör du följande och välj sedan **Slutför**:

    - Ange ett namn och plats.

    - I den **projekt SDK** listrutan, Välj **Java 1.8** för 2.x Spark-kluster eller välj **Java 1.7** för 1.x Spark-klustret.

    - I den **Spark version** listrutan Scala projektguiden integrerar rätt version för Spark-SDK och Scala SDK. Om den Spark-kluster av versionen är äldre än 2.0 väljer **Väck 1.x**. Annars väljer **Spark2.x**. Det här exemplet används **Spark punkt 2.0.2 (Scala 2.11.8)**.
  
   ![Välj den projekt SDK och Spark-versionen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Spark-projekt skapas automatiskt en artefakt. Om du vill visa artefakten gör du följande:

    a. Från den **filen** väljer du **projektstruktur**.

    b. I den **projektstruktur** dialogrutan **artefakter** att visa den standard-artefakt som har skapats. Du kan också skapa egna artefakt genom att välja på plustecknet (**+**).

   ![Skapa JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Lägg till bibliotek i projektet. Om du vill lägga till ett bibliotek, gör du följande:

    a. Högerklicka på projektnamnet i projektträdet och välj sedan **öppna Modulinställningar**. 

    b. I den **projektstruktur** dialogrutan **bibliotek**, Välj den (**+**) symbol och välj sedan **från Maven** .

    ![Lägga till ett bibliotek](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. I den **Download Library från Maven databasen** dialogrutan rutan, söka efter och lägga till följande bibliotek:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Kopiera `yarn-site.xml` och `core-site.xml` från klustret head nod och Lägg till dem i projektet. Använd följande kommandon för att kopiera filerna. Du kan använda [Cygwin](https://cygwin.com/install.html) att köra följande `scp` kommandon för att kopiera filer från klustret huvudnoder:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Eftersom vi redan har lagt till klustrets huvudnod IP-adress och värddatornamn för hosts-filen på skrivbordet, kan vi använda den `scp` kommandon på följande sätt:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Om du vill lägga till de här filerna i projektet, kopierar du dem under den **/src** mapp i ditt projektträdet, till exempel `<your project directory>\src`.
6. Uppdatera den `core-site.xml` filen att göra följande ändringar:

   a. Ersätt den krypterade nyckeln. Den `core-site.xml` filen innehåller den krypterade nyckeln till storage-konto som är associerade med klustret. I den `core-site.xml` -fil som du lagt till i projektet, Ersätt den krypterade nyckeln med faktiska lagringsnyckeln som är associerade med standardkontot för lagring. Mer information finns i [hantera åtkomstnycklar för lagring](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

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
7. Lägg till den huvudsakliga klassen för ditt program. Från den **Projektutforskaren**, högerklicka på **src**, peka på **ny**, och välj sedan **Scala klassen**.

    ![Välj den huvudsakliga klassen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. I den **skapa nya Scala klass** dialogrutan, ange ett namn, väljer **objekt** i den **typ** och välj sedan **OK**.

    ![Skapa ny Scala-klass](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. I den `MyClusterAppMain.scala` fil, klistra in följande kod. Den här koden skapar Spark kontexten och öppnar en `executeJob` metod från den `SparkSample` objekt.

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

10. Upprepa steg 8 och 9 för att lägga till ett nytt Scala-objekt som kallas `*SparkSample`. Lägg till följande kod i den här klassen. Den här koden läser data från HVAC.csv (tillgänglig i alla HDInsight Spark-kluster). Den hämtar de rader som bara innehåller en siffra i kolumnen sjunde i CSV-filen och sedan skriver utdata till **/HVACOut** under standardbehållare för lagring för klustret.

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
11. Upprepa steg 8 och 9 för att lägga till en ny klass med namnet `RemoteClusterDebugging`. Den här klassen implementerar Spark test framework som används för att felsöka program. Lägg till följande kod i den `RemoteClusterDebugging` klass:

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

      * För `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, kontrollera att sammansättningen Spark JAR är tillgänglig i klusterlagringen på den angivna sökvägen.
      * För `setJars`, ange platsen där artefakt JAR skapas. Vanligtvis är det `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. I den`*RemoteClusterDebugging` klassen, högerklicka på den `test` nyckelord och välj sedan **skapa RemoteClusterDebugging Configuration**.

    ![Skapa en konfiguration för fjärråtkomst](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. I den **skapa RemoteClusterDebugging Configuration** dialogrutan, ange ett namn för konfigurationen och välj sedan **testa typ** som den **testnamnet**. Lämna de andra värdena som standardinställningar. Välj **tillämpa**, och välj sedan **OK**.

    ![Lägg till konfigurationsinformationen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Du bör nu se en **Remote kör** configuration listrutan i menyraden.

    ![Den fjärranslutna kör listrutan](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Steg 5: Kör programmet i felsökningsläge
1. Öppna i projektet IntelliJ IDEA `SparkSample.scala` och skapa en brytpunkt bredvid `val rdd1`. I den **skapa brytpunkt för** popup-menyn och väljer **rad i funktionen executeJob**.

    ![Lägga till en brytpunkt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Om du vill köra programmet på **Debug kör** knappen bredvid den **Remote kör** configuration nedrullningsbara listan.

    ![Välj knappen Debug kör](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. När programkörningen når brytpunkten, visas en **felsökare** fliken längst ned i fönstret.

    ![Visa fliken felsökning](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Om du vill lägga till en titta på, Välj den (**+**) ikon.

    ![Välj den + -ikonen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    I det här exemplet programmet bröt mot innan variabeln `rdd1` skapades. Med hjälp av den här titta på det finns fem första raderna i variabeln `rdd`. Välj **ange**.

    ![Kör programmet i felsökningsläge](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Du ser i föregående bild är att du kan fråga terabyte data och felsökningsloggar vid körning, hur dina program pågår. Till exempel i utdata som visas i föregående bild, ser du att den första raden i resultatet är en rubrik. Du kan ändra din programkod för att hoppa över rubrikraden, om det behövs baserat på dessa utdata.
5. Nu kan du välja den **återuppta programmet** ikon för att fortsätta med ditt program körs.

    ![Välj återuppta Program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Om programmet slutförs bör du se utdata som liknar följande:

    ![Konsolutdata](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala projekt (video): [skapa Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärråtkomst debug (video): [Använd Azure Toolkit för IntelliJ till att felsöka Spark-program på ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure Toolkit för IntelliJ för att skapa Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs på ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
