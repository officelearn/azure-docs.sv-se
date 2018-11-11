---
title: Skriptåtgärd – installera Python-paket med Jupyter på Azure HDInsight
description: Stegvisa instruktioner om hur du använder skriptåtgärd till att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster att använda externa python-paket.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: af25dcff2302827f2291d50972f09b8b5fda6cd3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255446"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använd skriptåtgärd till att installera externa Python-paket för Jupyter notebook i Apache Spark-kluster i HDInsight
> [!div class="op_single_selector"]
> * [Med cellfunktioner](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Med skriptåtgärder](apache-spark-python-package-installation.md)
>
>

Lär dig hur du använder skriptåtgärder för att konfigurera ett Apache Spark-kluster på HDInsight (Linux) för att använda externa, communityn har bidragit med **python** paket som inte är inkluderat out-of the box i klustret.

> [!NOTE]
> Du kan också konfigurera en Jupyter-anteckningsbok med hjälp av `%%configure` magic att använda externa paket. Anvisningar finns i [använda externa paket med Jupyter notebooks i Apache Spark-kluster på HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Du kan söka i [paketindexet](https://pypi.python.org/pypi) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som har gjorts tillgängliga via [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) eller [conda-bedömningar](https://conda-forge.org/feedstocks/).

I den här artikeln får du lära dig hur du installerar den [TensorFlow](https://www.tensorflow.org/) paketera med skriptåtgärd i ditt kluster och använda den via Jupyter-anteckningsboken som ett exempel.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Om du inte redan har ett Spark-kluster i HDInsight Linux kan du köra skriptåtgärder när klustret skapas. Gå till dokumentationen på [hur du använder anpassade skriptåtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 
   
   ## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för öppen källkod-programvara som används i HDInsight-kluster

Microsoft Azure HDInsight-tjänsten använder ett ekosystem med öppen källkod-tekniker som skapats på rätt sätt runt Hadoop. Microsoft Azure tillhandahåller en allmän supportnivå för tekniker med öppen källkod. Mer information finns i den **supportens omfattning** delen av den [stöd för vanliga frågor och svar för Azure-webbplats](https://azure.microsoft.com/support/faq/). Tjänsten HDInsight ger en extra nivå av stöd för inbyggda komponenterna.

Det finns två typer av öppen källkod-komponenter som är tillgängliga i HDInsight-tjänsten:

* **Inbyggda komponenterna** -komponenterna är förinstallerade på HDInsight-kluster och tillhandahåller huvudfunktionerna i klustret. Till exempel YARN ResourceManager, Hive-frågespråket (HiveQL) och Mahout-biblioteket som hör till den här kategorin. En fullständig lista över komponenter i serverkluster finns i [Nyheter i Hadoop-klusterversionerna från HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning).
* **Anpassade komponenter** -du, som en användare i klustret, kan installera eller använda i din arbetsbelastning någon komponent som är tillgänglig i diskussionsgruppen eller skapats av dig.

> [!WARNING]
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske kan lösa problemet eller de kan be dig att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. På startsidan i [Azure-portalen](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

2. Spark-klusterbladet, klickar du på **skriptåtgärder** i den vänstra rutan. Använd skripttypen ”anpassad” och ange ett eget namn för skriptåtgärden. Kör skriptet på den **huvud- och worker noder** och lämna parametrar fältet tomt. Bash-skript kan refereras från: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh finns i dokumentationen på [hur du använder anpassade skriptåtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Det finns två python installationer i klustret. Spark använder Anaconda python-installationen finns på `/usr/bin/anaconda/bin`. Referera till den installationen i dina anpassade åtgärder via `/usr/bin/anaconda/bin/pip` och `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Öppna en PySpark Jupyter-anteckningsbok

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

5. Du får nu `import tensorflow` och köra en hello world-exemplet. 

    Kopiera följande kod:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Resultatet ser ut så här:
    
    ![TensorFlow kodkörning](./media/apache-spark-python-package-installation/execution.png "köra TensorFlow-kod")

## <a name="seealso"></a>Se även
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
* [Använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
