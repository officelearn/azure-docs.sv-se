---
title: "Script åtgärden - installera Python-paket med Jupyter på Azure HDInsight | Microsoft Docs"
description: "Stegvisa instruktioner om hur du använder skriptåtgärd till att konfigurera tillgängliga Jupyter-anteckningsböcker med HDInsight Spark-kluster att använda externa python-paket."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: ab1d48188d4e0ef1274eb175b49bafd1d63b8480
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använd skriptåtgärd till att installera externa Python-paket för Jupyter-anteckningsböcker i Apache Spark-kluster i HDInsight
> [!div class="op_single_selector"]
> * [Med hjälp av cell Magiskt tal](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Med skriptåtgärder](apache-spark-python-package-installation.md)
>
>

Lär dig använda Script Actions för att konfigurera ett Apache Spark-kluster i HDInsight (Linux) för att använda externa, community-bidragit **python** paket som inte ingår out box i klustret.

> [!NOTE]
> Du kan också konfigurera en Jupyter-anteckningsbok med hjälp av `%%configure` Magiskt tal för att använda externa paket. Instruktioner finns i [använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Du kan söka i [paketindexet](https://pypi.python.org/pypi) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som är tillgängliga via [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) eller [conda bedömningar](https://conda-forge.github.io/feedstocks.html).

I den här artikeln får du lära dig hur du installerar den [TensorFlow](https://www.tensorflow.org/) paketet med skriptåtgärder på klustret och använda den via Jupyter-anteckningsboken.

## <a name="prerequisites"></a>Krav
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Om du inte redan har ett Spark-kluster i HDInsight Linux kan du köra skriptåtgärder när klustret skapas. Finns i dokumentationen på [hur du använder anpassade skriptåtgärder](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. På startsidan i [Azure-portalen](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

2. Klicka på bladet Spark-kluster **skriptåtgärder** i den vänstra rutan. Kör den anpassade åtgärden som installerar TensorFlow i huvudnoderna och arbetsnoderna. Bash-skript kan refereras från: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh finns i dokumentationen på [hur du använder anpassade skriptåtgärder](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Det finns två python installationer i klustret. Spark använder Anaconda python installationen finns på `/usr/bin/anaconda/bin`. Referera till den installationen i din anpassade åtgärder via `/usr/bin/anaconda/bin/pip` och `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Öppna en PySpark Jupyter-anteckningsbok

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

5. Du kommer nu `import tensorflow` och kör ett hello world-exempel. 

    Kopiera följande kod:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Resultatet ser ut så här:
    
    ![TensorFlow kodkörning](./media/apache-spark-python-package-installation/execution.png "köra TensorFlow kod")



## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
