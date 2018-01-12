---
title: "Microsoft kognitiva Toolkit med Azure HDInsight Spark för djup learning | Microsoft Docs"
description: "Lär dig hur en tränad modell Microsoft kognitiva Toolkit djup learning kan tillämpas på en datamängd med Spark Python API i ett Azure HDInsight Spark-kluster."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 036efd040370a821befbbd57beec24372fd0d204
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Använd Microsoft kognitiva Toolkit djup Lär modell med Azure HDInsight Spark-kluster

Gör följande steg i den här artikeln.

1. Köra ett anpassat skript för att installera Microsoft kognitiva Toolkit på ett Azure HDInsight Spark-kluster.

2. Överför en Jupyter-anteckningsbok till Spark-kluster för att se hur du använder en tränad modell Microsoft kognitiva Toolkit djup learning till filer i en Azure Blob Storage-konto med hjälp av den [Spark Python-API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

* **Azure HDInsight Spark-kluster**. Skapa ett Spark 2.0-kluster i den här artikeln. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hur går den här lösningen?

Den här lösningen delas mellan den här artikeln och en Jupyter-anteckningsbok som du överför som en del av den här kursen. I den här artikeln kan du utföra följande steg:

* Kör en skriptåtgärd på ett HDInsight Spark-klustret för att installera Microsoft kognitiva Toolkit och Python-paket.
* Överför Jupyter-anteckningsbok som kör lösningen till HDInsight Spark-klustret.

De följande stegen beskrivs i Jupyter-anteckningsboken.

- Läsa in exempel bilder i en distribuerad Spark Resiliant Dataset eller RDD
   - Läsa in moduler och definiera förinställningar
   - Hämta dataset lokalt på Spark-kluster
   - Konvertera datauppsättningen till en RDD
- Poängsätta bilder med hjälp av en trained model kognitiva Toolkit
   - Hämta den tränade modellen kognitiva Toolkit till Spark-kluster
   - Definiera funktioner som ska användas av arbetsnoder
   - Poängsätta avbildningar på arbetsnoder
   - Utvärdera modellen noggrannhet


## <a name="install-microsoft-cognitive-toolkit"></a>Installera Microsoft kognitiva Toolkit

Du kan installera Microsoft kognitiva Toolkit på ett Spark-kluster med skriptåtgärder. Skriptåtgärder använder anpassade skript för att installera komponenter i klustret som inte är tillgängliga som standard. Du kan använda anpassade skript från Azure-portalen genom att använda HDInsight .NET SDK eller med hjälp av Azure PowerShell. Du kan också använda skriptet för att installera verktyget antingen som en del av klustret har skapats eller när klustret är igång. 

Vi använder portalen för att installera verktygen, när klustret har skapats i den här artikeln. Andra sätt att köra skriptet finns [anpassa HDInsight-kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Använda Azure-portalen

Anvisningar om hur du använder Azure Portal för att köra skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Kontrollera att du anger följande indata för att installera Microsoft kognitiva Toolkit.

* Ange ett värde för namnet på åtgärden.

* För **Bash skript URI**, ange `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Kontrollera att du kör skriptet endast på head- och arbetsroller noder och avmarkera alla andra kryssrutor.

* Klicka på **Skapa**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Överför Jupyter-anteckningsbok till Azure HDInsight Spark-kluster

Om du vill använda Microsoft kognitiva Toolkit med Azure HDInsight Spark-kluster måste du ladda Jupyter-anteckningsbok **CNTK_model_scoring_on_Spark_walkthrough.ipynb** i Azure HDInsight Spark-klustret. Den här anteckningsboken finns på GitHub på [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Klona lagringsplatsen GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Anvisningar att klona finns [kloning av en databas](https://help.github.com/articles/cloning-a-repository/).

2. Öppna bladet Spark-kluster som du redan etablerats, klickar du på Azure-portalen **Klusterinstrumentpanel**, och klicka sedan på **Jupyter-anteckningsbok**.

    Du kan också starta Jupyter-anteckningsbok genom att gå till URL: en `https://<clustername>.azurehdinsight.net/jupyter/`. Ersätt \<klusternamn > med namnet på ditt HDInsight-kluster.

3. Från Jupyter-anteckningsbok klickar du på **överför** i övre högra hörn och gå sedan till den plats där du har klonat GitHub-lagringsplatsen.

    ![Överför Jupyter-anteckningsbok till Azure HDInsight Spark-kluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "överför Jupyter-anteckningsbok till Azure HDInsight Spark-kluster")

4. Klicka på **överför** igen.

5. När den bärbara datorn har överförts klickar du på namnet på den bärbara datorn och följ sedan anvisningarna i den bärbara datorn sig själv att läsa in datauppsättningen och genomföra kursen.

## <a name="see-also"></a>Se också
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetridataanalys i HDInsight](apache-spark-analyze-application-insight-logs.md)

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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
