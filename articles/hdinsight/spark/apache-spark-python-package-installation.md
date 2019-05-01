---
title: Skriptåtgärd – installera Python-paket med Jupyter på Azure HDInsight
description: Stegvisa instruktioner om hur du använder skriptåtgärd till att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster att använda externa python-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: c07326cc3a4334f1873eef2dc23da05156a93577
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574656"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använd skriptåtgärd till att installera externa Python-paket för Jupyter notebook i Apache Spark-kluster i HDInsight
> [!div class="op_single_selector"]
> * [Med cellfunktioner](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Med skriptåtgärder](apache-spark-python-package-installation.md)

Lär dig hur du använder skriptåtgärder för att konfigurera en [Apache Spark](https://spark.apache.org/) -kluster i HDInsight för att använda externa, communityn har bidragit med **python** paket som inte är inkluderat out-of the box i klustret.

> [!NOTE]  
> Du kan också konfigurera en Jupyter-anteckningsbok med hjälp av `%%configure` magic att använda externa paket. Anvisningar finns i [använda externa paket med Jupyter notebooks i Apache Spark-kluster på HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Du kan söka i [paketindexet](https://pypi.python.org/pypi) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som har gjorts tillgängliga via [conda-bedömningar](https://conda-forge.org/feedstocks/).

I den här artikeln får du lära dig hur du installerar den [TensorFlow](https://www.tensorflow.org/) paketera med skriptåtgärd i ditt kluster och använda den via Jupyter-anteckningsboken som ett exempel.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Om du inte redan har ett Spark-kluster i HDInsight Linux kan du köra skriptåtgärder när klustret skapas. Gå till dokumentationen på [hur du använder anpassade skriptåtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för öppen källkod-programvara som används i HDInsight-kluster

Microsoft Azure HDInsight-tjänsten använder ett ekosystem med öppen källkod-tekniker som skapats på rätt sätt runt Apache Hadoop. Microsoft Azure tillhandahåller en allmän supportnivå för tekniker med öppen källkod. Mer information finns i den **supportens omfattning** delen av den [stöd för vanliga frågor och svar för Azure-webbplats](https://azure.microsoft.com/support/faq/). Tjänsten HDInsight ger en extra nivå av stöd för inbyggda komponenterna.

Det finns två typer av öppen källkod-komponenter som är tillgängliga i HDInsight-tjänsten:

* **Inbyggda komponenterna** -komponenterna är förinstallerade på HDInsight-kluster och tillhandahåller huvudfunktionerna i klustret. Till exempel Apache Hadoop YARN ResourceManager, Apache Hive-frågespråket (HiveQL) och Mahout-biblioteket som hör till den här kategorin. En fullständig lista över komponenter i serverkluster finns i [vad är nytt i Apache Hadoop-klusterversionerna från HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Anpassade komponenter** -du, som en användare i klustret, kan installera eller använda i din arbetsbelastning någon komponent som är tillgänglig i diskussionsgruppen eller skapats av dig.

> [!IMPORTANT]   
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske kan lösa problemet eller de kan be dig att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ https://apache.org ](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. Från den [Azure-portalen](https://portal.azure.com/), navigera till ditt kluster.  

2. Med ditt kluster som valts i den vänstra rutan under **inställningar**väljer **skriptåtgärder**.

3. Välj **+ Skicka ny**.

4. Ange följande värden för den **skicka skriptåtgärd** fönster:  


    |Parameter | Värde |
    |---|---|
    |Skripttyp | Välj **– anpassade** från den nedrullningsbara listan.|
    |Namn |Ange `tensorflow` i textrutan.|
    |Bash-skript-URI |Ange `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` i textrutan. |
    |Nodtyper | Välj den **Head**, och **Worker** kryssrutorna. |

    `tensorflowinstall.sh` innehåller följande kommandon:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Välj **Skapa**.  Gå till dokumentationen på [hur du använder anpassade skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

6. Vänta på att skriptet ska slutföras.  Den **skriptåtgärder** fönstret kommer tillstånd **kan skicka nya skriptåtgärder efter den aktuella klusteråtgärden har slutförts** medan skriptet körs.  En förloppsindikator visas från Ambari UI **bakgrundsåtgärder** fönster.

7. Öppna en PySpark Jupyter-anteckningsbok.  Se [skapa en Jupyter-anteckningsbok på Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) anvisningar.

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

8. Du får nu `import tensorflow` och köra en hello world-exemplet. Ange följande kod:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Resultatet ser ut så här:
    
    ![TensorFlow kodkörning](./media/apache-spark-python-package-installation/execution.png "köra TensorFlow-kod")

> [!NOTE]  
> Det finns två python installationer i klustret. Spark använder Anaconda python-installationen finns på `/usr/bin/anaconda/bin` och får som standard i Python 2.7-miljön. Om du vill använda Python 3.x och installera paket i kerneln PySpark3 använder sökvägen till den `conda` körbara för den miljön och Använd den `-n` parametern för att ange miljön. Till exempel kommandot `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, installerar den `ggplot` paketet i Python 3.5 miljö med hjälp av den `conda-forge` kanal.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-Plugin för IntelliJ IDEA till att felsöka Apache Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
