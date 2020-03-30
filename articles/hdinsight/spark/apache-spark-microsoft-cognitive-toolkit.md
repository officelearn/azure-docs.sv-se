---
title: Microsoft Cognitive Toolkit med Apache Spark - Azure HDInsight
description: Lär dig hur en tränad Microsoft Cognitive Toolkit-djupinlärningsmodell kan tillämpas på en datauppsättning med Spark Python-API:et i ett Azure HDInsight Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206562"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Använda Microsoft Cognitive Toolkit deep learning-modell med Azure HDInsight Spark-kluster

I den här artikeln gör du följande steg.

1. Kör ett anpassat skript för att installera [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) i ett Azure HDInsight Spark-kluster.

2. Ladda upp en [Jupyter-anteckningsbok](https://jupyter.org/) till [Apache Spark-klustret](https://spark.apache.org/) för att se hur du använder en tränad Microsoft Cognitive Toolkit-djupinlärningsmodell på filer i ett Azure Blob Storage-konto med [Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Se [Skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [Läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Hur flyter den här lösningen?

Denna lösning är uppdelad mellan den här artikeln och en Jupyter anteckningsbok som du laddar upp som en del av den här artikeln. I den här artikeln kan du slutföra följande steg:

* Kör en skriptåtgärd på ett HDInsight Spark-kluster för att installera Microsoft Cognitive Toolkit- och Python-paket.
* Ladda upp den jupyter-anteckningsbok som kör lösningen till HDInsight Spark-klustret.

Följande återstående steg beskrivs i den jupyterda anteckningsboken.

* Läs in exempelbilder i en Spark Resilient Distributed Dataset eller RDD.
  * Läs in moduler och definiera förinställningar.
  * Hämta datauppsättningen lokalt i Spark-klustret.
  * Konvertera datauppsättningen till en RDD.
* Betygsätta bilderna med hjälp av en tränad Kognitiv Verktygslåda modell.
  * Hämta den tränade Cognitive Toolkit-modellen till Spark-klustret.
  * Definiera funktioner som ska användas av arbetsnoder.
  * Betyg på bilderna på arbetsnoder.
  * Utvärdera modellens noggrannhet.

## <a name="install-microsoft-cognitive-toolkit"></a>Installera Microsoft Cognitive Toolkit

Du kan installera Microsoft Cognitive Toolkit i ett Spark-kluster med hjälp av skriptåtgärd. Skriptåtgärden använder anpassade skript för att installera komponenter i klustret som inte är tillgängliga som standard. Du kan använda det anpassade skriptet från Azure-portalen, med hdinsight .NET SDK eller genom att använda Azure PowerShell. Du kan också använda skriptet för att installera verktygslådan antingen som en del av klusterskapandet eller när klustret är igång.

I den här artikeln använder vi portalen för att installera verktygslådan, efter att klustret har skapats. Andra sätt att köra det anpassade skriptet finns i [Anpassa HDInsight-kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Instruktioner om hur du använder Azure-portalen för att köra skriptåtgärd finns i [Anpassa HDInsight-kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Se till att du anger följande indata för att installera Microsoft Cognitive Toolkit. Använd följande värden för skriptåtgärden:

|Egenskap |Värde |
|---|---|
|Skripttyp|- Anpassad|
|Namn| Installera MCT|
|Bash skript URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Nodtyper:|Chef, Arbetare|
|Parametrar|Inget|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Ladda upp Jupyter-anteckningsboken till Azure HDInsight Spark-klustret

Om du vill använda Microsoft Cognitive Toolkit med Azure HDInsight Spark-klustret måste du läsa in jupyter-anteckningsboken **CNTK_model_scoring_on_Spark_walkthrough.ipynb** i Azure HDInsight Spark-klustret. Den här anteckningsboken är [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)tillgänglig på GitHub på .

1. Ladda ner och [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)packa upp .

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter`, var `CLUSTERNAME` är namnet på klustret.

1. I den jupyterdateckningsboken väljer du **Ladda upp** i det `CNTK_model_scoring_on_Spark_walkthrough.ipynb`övre högra hörnet och navigerar sedan till nedladdningen och väljer fil .

    ![Ladda upp Jupyter-anteckningsboken till Azure HDInsight Spark-kluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Ladda upp Jupyter-anteckningsboken till Azure HDInsight Spark-kluster")

1. Välj **Ladda upp** igen.

1. När anteckningsboken har laddats upp klickar du på namnet på anteckningsboken och följer sedan instruktionerna i själva anteckningsboken om hur du läser in datauppsättningen och utför artikeln.

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program Insight telemetri dataanalys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)

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
