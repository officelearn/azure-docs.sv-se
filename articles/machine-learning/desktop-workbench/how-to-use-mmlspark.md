---
title: Hur du använder MMLSpark Machine Learning | Microsoft Docs
description: Får använda MMLSpark-bibliotek med Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 654b2559518cd52978153310fbb1e89a91838a8a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649419"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Hur du använder Microsoft Machine Learning-biblioteket för Apache Spark

## <a name="introduction"></a>Introduktion

[Microsoft Machine Learning-biblioteket för Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) tillhandahåller verktyg som låter dig enkelt skapa skalbar machine learning-modeller för stora datauppsättningar. Det omfattar integrering av SparkML pipelines med den [Microsoft Cognitive Toolkit ](https://github.com/Microsoft/CNTK) och [OpenCV](http://www.opencv.org/), så att du kan: 
 * Ingångshändelser och förprocess image
 * Funktionalisera bilder och text med hjälp av förtränade modeller för djupinlärning
 * Träna och betygsätta klassificerings- och regressionsmodeller modeller med implicit funktionalisering.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här guiden, måste du:
- [Installera Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [Konfigurera Azure HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Kör experimentet i Docker-behållare

Azure Machine Learning Workbench är konfigurerad för att använda MMLSpark när du kör experiment i Docker-behållare, antingen lokalt eller i en fjärransluten virtuell dator. Den här funktionen kan du enkelt kan felsöka och experimentera med PySpark-modeller, innan du kör dem på skala på ett kluster. 

Skapa ett nytt projekt för att komma igång med ett exempel, och välj ”MMLSpark på vuxet insamlade” exempeltext. Välj ”Docker” som beräkningskontexten från instrumentpanelen för projektet och klicka på ”Kör”. Azure Machine Learning Workbench skapar Docker-behållare för att köra programmet PySpark och därefter körs programmet.

När körningen har slutförts kan visa du resultatet i vy för körningshistorik för Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Installera MMLSpark i Azure HDInsight Spark-kluster.

För att slutföra det här och följande steg, måste du först [skapa ett Azure HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Som standard installerar Azure Machine Learning Workbench MMLSpark-paketet i ditt kluster när du kör experimentet. Du kan kontrollera det här beteendet och installera andra Spark-paket genom att redigera en fil med namnet _aml_config/spark_dependencies.yml_ i projektmappen.

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

Du kan också installera MMLSpark direkt på ditt HDInsight Spark-kluster med [skriptåtgärd](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Konfigurera Azure HDInsight Spark-kluster som Compute-mål

Öppna CLI-fönstret från Azure Machine Learning Workbench genom att gå till Arkiv-menyn och klicka på ”Öppna Kommandotolken”

Kör följande kommandon i CLI-fönstret:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Klustret är nu tillgänglig som beräkningsmål för projektet.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Kör experiment i Azure HDInsight Spark-kluster.

Gå tillbaka till instrumentpanelen för projektet för till exempel ”MMLSpark på vuxet inventering”. Välj ditt kluster som beräkningsmål och klicka på Kör.

Azure Machine Learning Workbench skickar spark-jobb i klustret. Du kan övervaka förloppet och visa resultaten i vy för körningshistorik.

## <a name="next-steps"></a>Nästa steg
Information om MMLSpark-bibliotek och exempel finns i [MMLSpark GitHub-lagringsplats](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark och Spark® är antingen registrerade varumärken eller varumärken som tillhör Apache Software Foundation i USA och/eller andra länder.*
