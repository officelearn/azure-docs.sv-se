---
title: "Felsöka problem med Apache Spark-kluster i Azure HDInsight | Microsoft Docs"
description: "Lär dig om problem relaterade till Apache Spark-kluster i Azure HDInsight och hur du undviker de."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: de7847055c00fe9d0d1cc08cf5ba5d2ab54a9fc0
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Kända problem med Apache Spark-kluster i HDInsight

Det här dokumentet håller reda på kända problem för HDInsight Spark public preview.  

## <a name="livy-leaks-interactive-session"></a>Livius läcker interaktiva sessionen
När Livius startas (från Ambari eller på grund av headnode 0 virtuella omstart) med en interaktiv session fortfarande lever har en interaktiv jobbet session läckts. Därför nya jobb kan ha fastnat i tillståndet godkända och kan inte startas.

**Lösning:**

Använd följande procedur för att lösa problemet:

1. SSH i headnode. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Kör följande kommando för att hitta program-ID för de interaktiva jobb startas genom Livius. 
   
        yarn application –list
   
    Jobbet standardnamnen måste Livius om jobb startades med en interaktiv session Livius med inga explicit namn anges. Jobbnamnet börjar med remotesparkmagics_ * för Livius sessionen startas av Jupyter-anteckningsbok. 
3. Kör följande kommando för att avsluta dessa jobb. 
   
        yarn application –kill <Application ID>

Nya jobb startas. 

## <a name="spark-history-server-not-started"></a>Spark historik Server inte har startats
Spark historik Server startas inte automatiskt efter ett kluster skapas.  

**Lösning:** 

Starta servern historik manuellt från Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problem i Spark loggkatalogen
När hdiuser skickar ett jobb med spark-submit, det finns ett fel java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (behörighet nekas) och drivrutin loggfilerna skrivs inte. 

**Lösning:**

1. Lägga till hdiuser i Hadoop-gruppen. 
2. Ange 777 behörigheter på /var/log/spark när klustret skapas. 
3. Uppdatera spark logg med Ambari ska vara en katalog med 777 behörigheter.  
4. Kör spark-skicka som sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark Phoenix anslutningen stöds inte

Spark Phoenix anslutningen stöds för närvarande inte med ett HDInsight Spark-kluster.

**Lösning:**

I stället måste du använda Spark-HBase-anslutningen. Instruktioner finns i avsnittet [använda Spark HBase connector](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problem relaterade till Jupyter-anteckningsböcker
Följande är några kända problem som rör Jupyter-anteckningsböcker.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Bärbara datorer med icke-ASCII-tecken i filnamn
Jupyter-anteckningsböcker som kan användas i HDInsight Spark-kluster ska inte ha icke-ASCII-tecken i filnamn. Om du försöker överföra en fil med Jupyter UI, som har ett icke-ASCII-filnamn, misslyckas tyst (det vill säga Jupyter kan inte överföra filen, men felet visas inte utlösa antingen). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fel vid inläsning av bärbara datorer av större storlek
Ett fel kan uppstå  **`Error loading notebook`**  när du läser in anteckningsböcker som är större.  

**Lösning:**

Om du får det här felet kan innebär det inte dina data är skadad eller förlorade.  Dina anteckningsböcker finns kvar på disken i `/var/lib/jupyter`, och du kan SSH i klustret för att komma åt dem. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du har anslutit till klustret med SSH, kan du kopiera dina anteckningsböcker från ditt kluster på den lokala datorn (med SCP eller WinSCP) som en säkerhetskopia för att förhindra förlust av viktiga data i den bärbara datorn. Du kan sedan SSH-tunnel i din headnode på port 8001 att komma åt Jupyter utan att gå via gatewayen.  Därifrån kan du rensa utdata från din bärbara dator och spara det för att minimera storleken på den bärbara datorn.

Du måste följa några metoder för att förhindra att det här felet inträffar i framtiden:

* Det är viktigt att hålla anteckningsboken små. Alla utdata från Spark-jobb som skickas tillbaka till Jupyter sparas i den bärbara datorn.  Det är bäst med Jupyter i allmänhet att undvika `.collect()` på stora RDDS eller dataframes; om du vill granska en RDD innehållet i stället körs `.take()` eller `.sample()` så att dina utdata inte blir för stor.
* Dessutom när du sparar en bärbar dator, utdata avmarkera alla cellerna om du vill minska storleken.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Bärbar dator första start tar längre tid än väntat
Den första koden instruktionen i Jupyter-anteckningsbok med Spark magic kan ta mer än en minut.  

**Förklaring:**

Detta beror på att när den första kodcellen körs. I bakgrunden detta startar sessionskonfigurationen och Spark, SQL och Hive sammanhang anges. När dessa sammanhang anges, den första satsen körs och det ger intryck som tog lång tid att slutföra i instruktionen.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter notebook timeout för att skapa sessionen
När Spark-kluster har slut på resurser, kommer Spark och PySpark-kernel i Jupyter-anteckningsbok Tidsgränsen nåddes vid försök att skapa sessionen. 

**Åtgärder:** 

1. Fri resurser i Spark-kluster genom att:
   
   * Stoppa andra Spark bärbara datorer genom att gå till menyn Stäng och stopp eller klicka Stäng i anteckningsboken explorer.
   * Stoppa andra Spark-program från YARN.
2. Starta om den bärbara datorn som du försöker starta. Tillräckligt med resurser ska vara tillgänglig för dig att skapa en session nu.

## <a name="see-also"></a>Se också
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

