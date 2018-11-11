---
title: Microsoft Cognitive Toolkit med Azure HDInsight Spark för djupinlärning
description: Lär dig hur en tränad modell med djupinlärning för Microsoft Cognitive Toolkit kan användas på en datauppsättning med hjälp av Spark Python-API i ett Azure HDInsight Spark-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 4bcf6df37e7341baf227b9c77b718a955526823d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010941"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Använd Microsoft Cognitive Toolkit deep learning-modell med Azure HDInsight Spark-kluster

I den här artikeln får göra du följande steg.

1. Köra ett anpassat skript för att installera Microsoft Cognitive Toolkit på ett Azure HDInsight Spark-kluster.

2. Ladda upp en Jupyter-anteckningsbok till Spark-kluster för att se hur du använder en träningsmodell Microsoft Cognitive Toolkit djupinlärning för filer i en Azure Blob Storage-konto med hjälp av den [Spark Python-API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

* **Azure HDInsight Spark-kluster**. Skapa ett Spark 2.0-kluster i den här artikeln. Anvisningar finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Hur går den här lösningen?

Den här lösningen delas mellan den här artikeln och en Jupyter-anteckningsbok som du laddar upp som en del av den här självstudien. I den här artikeln får utföra du följande steg:

* Kör en skriptåtgärd på ett HDInsight Spark-kluster för att installera Microsoft Cognitive Toolkit och Python-paket.
* Ladda upp Jupyter-anteckningsboken som kör lösningen till HDInsight Spark-kluster.

De följande stegen beskrivs i Jupyter-anteckningsboken.

- Läsa in Exempelbilder i en Spark Resiliant distribuerade datauppsättning eller RDD
   - Läsa in moduler och definiera förinställningar
   - Hämta datauppsättningen lokalt på Spark-kluster
   - Konvertera datauppsättningen till en RDD
- Bedöma bilder med hjälp av en trained model Cognitive Toolkit
   - Ladda ned den tränade modellen Cognitive Toolkit till Spark-kluster
   - Definiera funktioner som ska användas av arbetsnoder
   - Bedöma bilderna på arbetsnoder
   - Utvärdera modellens Precision


## <a name="install-microsoft-cognitive-toolkit"></a>Installera Microsoft Cognitive Toolkit

Du kan installera Microsoft Cognitive Toolkit på ett Spark-kluster med skriptåtgärd. Skriptåtgärd använder anpassade skript för att installera komponenter i klustret som inte är tillgängliga som standard. Du kan använda det anpassade skriptet från Azure-portalen med hjälp av HDInsight .NET SDK eller med hjälp av Azure PowerShell. Du kan också använda skriptet för att installera verktyget antingen som en del av klustret skapas eller när klustret är igång. 

I den här artikeln använder vi portalen du installerar detta toolkit när klustret har skapats. Andra sätt att köra anpassade skript, se [anpassa HDInsight-kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Använda Azure-portalen

Anvisningar om hur du använder Azure Portal för att köra skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Kontrollera att du anger följande indata för att installera Microsoft Cognitive Toolkit.

* Ange ett värde för namnet på åtgärden.

* För **Bash-skript-URI: N**, ange `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Kontrollera att du kör skriptet endast på huvud- och worker-noder och avmarkerar du andra kryssrutorna.

* Klicka på **Skapa**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Ladda upp Jupyter-anteckningsboken till Azure HDInsight Spark-kluster

Om du vill använda Microsoft Cognitive Toolkit med Azure HDInsight Spark-kluster, måste du läsa in Jupyter-anteckningsboken **CNTK_model_scoring_on_Spark_walkthrough.ipynb** till Azure HDInsight Spark-kluster. Den här anteckningsboken finns på GitHub på [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Klona GitHub-databasen [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Anvisningar för att klona finns i [kloning av en lagringsplats](https://help.github.com/articles/cloning-a-repository/).

2. Från Azure-portalen öppnar du bladet Spark-kluster som du redan har etablerat kan klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Jupyter-anteckningsbok**.

    Du kan också starta Jupyter-anteckningsboken genom att gå till URL: en `https://<clustername>.azurehdinsight.net/jupyter/`. Ersätt \<klusternamn > med namnet på ditt HDInsight-kluster.

3. Jupyter-anteckningsboken klickar du på **överför** i det övre högerkant hörnet och sedan navigera till den plats där du klonade GitHub-lagringsplatsen.

    ![Ladda upp Jupyter-anteckningsbok till Azure HDInsight Spark-kluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "överför Jupyter-anteckningsboken för att Azure HDInsight Spark-kluster")

4. Klicka på **överför** igen.

5. När den bärbara datorn har överförts klickar du på namnet på den bärbara datorn och följ sedan anvisningarna i anteckningsboken själva att läsa in datauppsättningen och utför i självstudiekursen.

## <a name="see-also"></a>Se också
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
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
