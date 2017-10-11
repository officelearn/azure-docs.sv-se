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
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Spark-program på ett HDInsight-kluster med Azure Toolkit för IntelliJ via SSH

Den här artikeln innehåller stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit för IntelliJ för att felsöka program via fjärranslutning på ett HDInsight-kluster. Om du vill felsöka ditt projekt, du kan också visa den [felsöka HDInsight Spark-program med Azure Toolkit för IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

**Förutsättningar**

* **HDInsight-verktyg i Azure Toolkit för IntelliJ**. Det här verktyget ingår i Azure Toolkit för IntelliJ. Mer information finns i [installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Azure Toolkit för IntelliJ**. Använd dessa verktyg för att skapa Spark-program för ett HDInsight-kluster. Mer information, följ instruktionerna i [Använd Azure Toolkit för IntelliJ till att skapa Spark-program för ett HDInsight-kluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **HDInsight SSH-tjänsten med hantering av användarnamn och lösenord**. Mer information finns i [Anslut till HDInsight (Hadoop) med hjälp av SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [använda SSH tunnlar för att komma åt Ambari web UI, jobbhistorik, NameNode, Oozie och andra webb-användargränssnitt](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Skapa ett Spark Scala-program och konfigurera den för fjärrfelsökning

1. Starta IntelliJ IDEA och sedan skapa ett projekt. I den **nytt projekt** dialogrutan Gör följande:

   a. Välj **HDInsight**. 

   b. Välj en mall för Java eller Scala baserat på dina inställningar. Välj mellan följande alternativ:

      - **Spark i HDInsight (Scala)**

      - **Spark i HDInsight (Java)**

      - **Spark i HDInsight-klustret kör exempel (Scala)**

      Det här exemplet används en **Spark på HDInsight-klustret kör sampel (Scala)** mall.

   c. I den **Build verktyget** väljer du något av följande enligt dina behov:

      - **Maven**, Scala skapa projekt guiden support

      -  **Segregerade Barlasttankar**, för att hantera beroenden och skapa för Scala-projekt 

      ![Skapa ett debug-projekt](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. Välj **nästa**.     
 
3. I nästa **nytt projekt** fönster, gör du följande:

   ![Välj Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. Ange ett projektnamn och projektets plats.

   b. I den **projekt SDK** listrutan, Välj **Java 1.8** för **Väck 2.x** kluster eller välj **Java 1.7** för **Väck 1.x**  klustret.

   c. I den **Spark Version** listrutan Scala projektguiden integrerar rätt version för Spark SDK och Scala SDK. Om den spark-kluster av versionen är äldre än 2.0 väljer **Väck 1.x**. Annars väljer **Väck 2.x.** Det här exemplet används **Spark punkt 2.0.2 (Scala 2.11.8)**.

   d. Välj **Slutför**.

4. Välj **src** > **huvudsakliga** > **scala** att öppna din kod i projektet. Det här exemplet används den **SparkCore_wasbloTest** skript.

5. Att få åtkomst till den **redigera konfigurationer** -menyn väljer du ikonen i det övre högra hörnet. Du kan skapa eller redigera konfigurationer för fjärrfelsökning från den här menyn.

   ![Redigera konfigurationer](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. I den **kör/Debug konfigurationer** dialogrutan Välj på plustecknet (**+**). Välj sedan den **skicka Spark jobbet** alternativet.

   ![Lägg till ny konfiguration](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. Ange information för **namn**, **Spark-kluster**, och **Main klassnamn**. Välj sedan **avancerad konfiguration**. 

   ![Kör debug-konfigurationer](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. I den **Spark skicka avancerad konfiguration** dialogrutan **aktivera Spark remote debug**. Ange SSH-användarnamn och ange ett lösenord eller Använd en fil för privat nyckel. Välj för att spara konfigurationen **OK**.

   ![Aktivera remote Spark-felsökning](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. Konfiguration sparas nu med det namn du angett. Välj namnet på konfigurationens om du vill visa konfigurationsinformationen. Om du vill göra ändringar, Välj **redigera konfigurationer**. 

10. När du har slutfört konfigurationsinställningarna för som kan du köra projektet mot fjärrklustret eller utföra fjärrfelsökning.

## <a name="learn-how-to-perform-remote-debugging"></a>Lär dig hur du utför fjärrfelsökning
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Utföra remote kör

I det här avsnittet hur vi du felsöker drivrutiner och executors.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. Ställ in senaste punkter och välj sedan den **felsöka** ikon.

   ![Välj debug-ikon](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. När programkörningen når den senaste punkten, visas en **drivrutinen** fliken och två **utföraren** flikarna i den **felsökare** fönstret. Välj den **återuppta programmet** ikon för att fortsätta att köra kod, som sedan når nästa brytpunkt och fokuserar på motsvarande **utföraren** fliken. Du kan granska körningsloggar på motsvarande **konsolen** fliken.

   ![Fliken felsökning](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>Scenario 2: Utföra fjärrfelsökning och åtgärda fel
I det här avsnittet hur vi du uppdatera dynamiskt variabelvärdet med IntelliJ felsökning kapaciteten för en enkel lösning. I följande kodexempel genereras ett undantag eftersom filen redan finns.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Att utföra fjärrfelsökning och åtgärda fel
1. Ställa in två bryta punkter och välj sedan den **felsöka** ikon för att starta felsökning fjärrprocessen.

2. Koden stannar vid den första platsen för senaste och parametern och variabel information visas i den **variabler** fönstret. 

3. Välj den **återuppta programmet** ikon för att fortsätta. Koden stannar vid den andra punkten. Det är undantagsfel som förväntat.

  ![Utlösa fel](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Välj den **återuppta programmet** ikonen igen. Den **HDInsight Spark skicka** fönstret visas felet ”jobbet körningen misslyckades”.

  ![Skicka fel](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Om du vill uppdatera dynamiskt variabelns värde med hjälp av IntelliJ felsökning kapaciteten, Välj **felsöka** igen. Den **variabler** fönstret visas igen. 

6. Högerklicka på målet på den **felsöka** och välj sedan **ange värde**. Ange sedan ett nytt värde för variabeln. Välj sedan **RETUR** att spara värdet. 

  ![Ange värde](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Välj den **återuppta programmet** ikon för att fortsätta att köra programmet. Nu är har inga undantag inträffat. Du kan se att projektet har körts utan undantag.

  ![Felsöka utan undantag](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Nästa steg
* [Översikt: Apache Spark i Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demo
* Skapa Scala projekt (video): [skapa Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärråtkomst debug (video): [Använd Azure Toolkit för IntelliJ till att felsöka Spark-program på ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att skapa realtid strömmade program](hdinsight-apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure Toolkit för IntelliJ för att skapa Spark-program för ett HDInsight-kluster](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Spark-klustret för HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](hdinsight-apache-spark-job-debugging.md)
