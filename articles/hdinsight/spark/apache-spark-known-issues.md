---
title: Felsöka problem med Apache Spark-kluster i Azure HDInsight
description: Lär dig mer om problem relaterade till Apache Spark-kluster i Azure HDInsight och hur du kan komma runt dem.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494525"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Kända problem för Apache Spark-kluster på HDInsight

Det här dokumentet håller reda på alla kända problem för den offentliga förhandsversionen av HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy läcker interaktiv session
När [Apache Livy](https://livy.incubator.apache.org/) startar om (från [Apache Ambari](https://ambari.apache.org/) eller på grund av headnode 0 virtuell maskin omstart) med en interaktiv session fortfarande lever, en interaktiv jobbsession läcker. Därför kan nya jobb fastna i tillståndet Accepterad.

**Lindring:**

Använd följande procedur för att komma runt problemet:

1. Ssh i headnode. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Kör följande kommando för att hitta program-ID:erna för de interaktiva jobb som startats via Livy.

        yarn application –list

    Standardjobbnamnen blir Livy om jobben startades med en interaktiv livy-session utan explicita namn angivna. För Livy-sessionen som startades av [Jupyter Notebook](https://jupyter.org/)börjar jobbnamnet med `remotesparkmagics_*`.

3. Kör följande kommando för att döda dessa jobb.

        yarn application –kill <Application ID>

Nya jobb börjar fungera.

## <a name="spark-history-server-not-started"></a>Spark-historikservern har inte startats
Spark History Server startas inte automatiskt när ett kluster har skapats.  

**Lindring:**

Starta historikservern manuellt från Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Behörighetsproblem i Spark-loggkatalogen
hdiuser får följande fel när du skickar in ett jobb med spark-skicka:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

Och ingen förarlogg skrivs.

**Lindring:**

1. Lägg till hdiuser i Hadoop-gruppen.
2. Ange 777 behörigheter för /var/log/spark när klustret har skapats.
3. Uppdatera gnistloggplatsen med Ambari som en katalog med 777 behörigheter.  
4. Kör spark-submit som sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix-kontakten stöds inte

HDInsight Spark-kluster stöder inte Spark-Phoenix-kontakten.

**Lindring:**

Du måste använda Spark-HBase-kontakten i stället. Instruktioner finns i [Så här använder du Spark-HBase-kontakt](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problem med Jupyters anteckningsböcker

Följande är några kända problem relaterade till Jupyter bärbara datorer.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Anteckningsböcker med icke-ASCII-tecken i filnamn

Använd inte icke-ASCII-tecken i Filnamnen för Bärbara Jupyter. Om du försöker ladda upp en fil via Jupyter UI, som har en icke-ASCII filnamn, misslyckas det utan felmeddelande. Jupyter låter dig inte ladda upp filen, men det kastar inte ett synligt fel heller.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fel vid inläsning av anteckningsböcker av större storlekar

Du kan se **`Error loading notebook`** ett fel när du läser in anteckningsböcker som är större i storlek.  

**Lindring:**

Om du får det här felet betyder det inte att dina data är skadade eller förlorade.  Dina anteckningsböcker finns `/var/lib/jupyter`fortfarande på disken i och du kan SSH i klustret för att komma åt dem. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du har anslutit till klustret med SSH kan du kopiera dina anteckningsböcker från klustret till den lokala datorn (med SCP eller WinSCP) som en säkerhetskopia för att förhindra förlust av viktiga data i anteckningsboken. Du kan sedan SSH tunnel i din headnode vid port 8001 för att komma åt Jupyter utan att gå igenom gatewayen.  Därifrån kan du rensa utdata från din bärbara dator och spara om den för att minimera den bärbara datorns storlek.

Om du vill förhindra att det här felet inträffar i framtiden måste du följa några metodtips:

* Det är viktigt att hålla storleken på den bärbara datorn liten. Alla utdata från spark-jobben som skickas tillbaka till Jupyter sparas i anteckningsboken.  Det är en bästa praxis med Jupyter i allmänhet för att undvika att köras `.collect()` på stora RDD eller dataramar; Om du i stället vill titta på innehållet i `.take()` `.sample()` en rdd kan du överväga att köra eller så att utdata inte blir för stora.
* När du sparar en anteckningsbok rensar du också alla utdataceller för att minska storleken.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Första start för bärbara datorer tar längre tid än förväntat

Första kod uttalande i Jupyter anteckningsbok med Spark magi kan ta mer än en minut.  

**Förklaring:**

Detta beror på att när den första kodcellen körs. I bakgrunden initierar detta sessionskonfiguration och Spark-, SQL- och Hive-kontexter anges. När dessa sammanhang har ställts in körs den första satsen och detta ger intryck av att uttalandet tog lång tid att slutföra.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Timeout för Jupyter-anteckningsbok när du skapar sessionen

När Spark-klustret har på resurser tar spark- och PySpark-kärnorna i jupyter-anteckningsboken timeout när de försöker skapa sessionen.

**Mitigations:**

1. Frigör resurser i Spark-klustret genom att:

   * Stoppa andra Spark-anteckningsböcker genom att gå till menyn Stäng och Stoppa eller klicka på Avstängning i utforskaren för den bärbara datorn.
   * Stoppa andra Spark-applikationer från YARN.

2. Starta om anteckningsboken som du försökte starta. Det bör finnas tillräckligt med resurser för att du ska kunna skapa en session nu.

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)