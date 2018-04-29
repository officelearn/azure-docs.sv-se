---
title: Hur du använder MMLSpark Machine Learning | Microsoft Docs
description: Anvisningar hur du använder MMLSpark bibliotek med Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 253f58721b709b044069c388521daf351264f74d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Hur du använder Microsoft Machine Learning-biblioteket för Apache Spark

## <a name="introduction"></a>Introduktion

[Microsoft Machine Learning-biblioteket för Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) tillhandahåller verktyg som gör att du enkelt skapa skalbara machine learning-modeller för stora datauppsättningar. Den omfattar integrering av SparkML pipelines med den [Microsoft kognitiva Toolkit ](https://github.com/Microsoft/CNTK) och [OpenCV](http://www.opencv.org/), så att du kan: 
 * Ingång och före processer bilddata
 * Featurize bilder och text med före tränats djup learning-modeller
 * Träna och betygsätta klassificering och regression modeller som använder implicit featurization.

## <a name="prerequisites"></a>Förutsättningar

Om du vill gå igenom den här instruktioner, måste du:
- [Installera Azure Machine Learning arbetsstationen](../service/quickstart-installation.md)
- [Konfigurera Azure HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Kör experimentet i Dockerbehållare

Din Azure Machine Learning-arbetsstationen är konfigurerad för att använda MMLSpark när du kör experiment i dockerbehållare, antingen lokalt eller i fjärranslutna VM. Den här funktionen kan du enkelt kan felsöka och experimentera med PySpark modeller, innan du kör dem på skalan på ett kluster. 

Om du vill komma igång med ett exempel, skapa ett nytt projekt och välj ”MMLSpark på vuxna inventering” exempeltext. Välj ”Docker” som beräknings-kontext från instrumentpanelen för projektet och klicka på ”Kör”. Azure Machine Learning arbetsstationen bygger dockerbehållare för att köra PySpark-programmet och kör programmet.

När körningen har slutförts kan visa du resultaten i vyn kör tidigare Azure Machine Learning-arbetsstationen.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Installera MMLSpark på Azure HDInsight Spark-kluster.

För att slutföra det här och följande steg måste du första [skapa ett Azure HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Som standard installerar Azure Machine Learning arbetsstationen MMLSpark paketet på ditt kluster när du kör experimentet. Du kan kontrollera det här beteendet och installera andra paket i Spark genom att redigera en fil med namnet _aml_config/spark_dependencies.yml_ i projektmappen.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

Du kan också installera MMLSpark direkt på din HDInsight Spark-kluster med [skriptåtgärd](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Konfigurera Azure HDInsight Spark-kluster som Compute-mål

Öppna fönstret CLI från Azure Machine Learning arbetsstationen genom att gå till Arkiv-menyn och klicka på ”Öppna Kommandotolken”.

I fönstret CLI kör du följande kommandon:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Klustret är nu tillgängligt som compute mål för projektet.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Kör experimentet på Azure HDInsight Spark-kluster.

Gå tillbaka till instrumentpanelen för projektet ”MMLSpark på vuxna inventering”-exemplet. Välj ditt kluster som mål för beräkning och klicka på Kör.

Azure Machine Learning arbetsstationen skickar spark-jobb till klustret. Du kan övervaka förloppet och visa sedan resultaten i vyn kör tidigare.

## <a name="next-steps"></a>Nästa steg
Information om MMLSpark bibliotek och exempel finns [MMLSpark GitHub-lagringsplatsen](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark och Spark® är antingen registrerade varumärken eller varumärken som tillhör Apache Software Foundation i USA och i andra länder.*
