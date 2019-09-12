---
title: Skript åtgärd – installera python-paket med Jupyter på Azure HDInsight
description: Stegvisa instruktioner för hur du använder skript åtgärd för att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda externa python-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: ce5dc7e17020e1e4564ebe1f531645f7329718dc
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900699"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Skript åtgärd för att installera externa python-paket för Jupyter-anteckningsböcker i Apache Spark på HDInsight

> [!div class="op_single_selector"]
> * [Använda cell Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Använda skript åtgärd](apache-spark-python-package-installation.md)

Lär dig hur du använder skript åtgärder för att konfigurera ett [Apache Spark](https://spark.apache.org/) kluster i HDInsight till att använda externa, community-bidroga **python** -paket som inte ingår i klustret.

> [!NOTE]  
> Du kan också konfigurera en Jupyter Notebook genom att `%%configure` använda Magic för att använda externa paket. Instruktioner finns i [använda externa paket med Jupyter-anteckningsböcker i Apache Spark kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Du kan söka i [paket indexet](https://pypi.python.org/pypi) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som gjorts tillgängliga via [Conda-falska](https://conda-forge.org/feedstocks/).

I den här artikeln får du lära dig hur du installerar [TensorFlow](https://www.tensorflow.org/) -paketet med skript åtgärd i klustret och använder det via Jupyter Notebook som ett exempel.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Om du inte redan har ett Spark-kluster i HDInsight Linux kan du köra skript åtgärder när klustret skapas. Gå till dokumentationen om [hur du använder anpassade skript åtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för program med öppen källkod som används i HDInsight-kluster

Microsoft Azure HDInsights tjänsten använder ett eko system med tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod. Mer information finns i avsnittet **support omfattning** på [webbplatsen för vanliga frågor om support för Azure](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare en nivå av stöd för inbyggda komponenter.

Det finns två typer av komponenter med öppen källkod som är tillgängliga i HDInsight-tjänsten:

* **Inbyggda komponenter** – dessa komponenter är förinstallerade i HDInsight-kluster och tillhandahåller kärn funktioner i klustret. Till exempel, Apache Hadoop garn-ResourceManager, Apache Hive frågespråket (HiveQL) och Mahout-biblioteket tillhör den här kategorin. En fullständig lista över kluster komponenter finns i [Nyheter i Apache Hadoop kluster versioner från HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Anpassade komponenter** – du, som en användare av klustret, kan installera eller använda i din arbets belastning, vilken komponent som helst som är tillgänglig i communityn eller som du har skapat.

> [!IMPORTANT]   
> Komponenter som ingår i HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet, eller så kan de be dig att tillhandahålla tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex.: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser [https://apache.org](https://apache.org)på, till exempel: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. Från [Azure Portal](https://portal.azure.com/)navigerar du till klustret.  

2. Med klustret valt väljer du **skript åtgärder**i den vänstra rutan under **Inställningar**.

3. Välj **+ Skicka ny**.

4. Ange följande värden för fönstret **Skicka skript åtgärd** :  


    |Parameter | Value |
    |---|---|
    |Skripttyp | Välj **anpassad** i list rutan.|
    |Name |Ange `tensorflow` i text rutan.|
    |Bash-skript-URI |Ange `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` i text rutan. |
    |Node-typ (er) | Markera kryss rutorna för **huvud**och **arbetare** . |

    `tensorflowinstall.sh`innehåller följande kommandon:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Välj **Skapa**.  Gå till dokumentationen om [hur du använder anpassade skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

6. Vänta tills skriptet har slutförts.  Fönstret **skript åtgärder** kommer att ange att **nya skript åtgärder kan skickas när den aktuella kluster åtgärden är slutförd** medan skriptet körs.  En förlopps indikator kan visas från fönstret Ambari-GRÄNSSNITTets **bakgrunds åtgärder** .

7. Öppna en PySpark Jupyter Notebook.  Anvisningar finns i [skapa en Jupyter-anteckningsbok för Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

8. Nu `import tensorflow` ska du köra ett Hello World-exempel. Ange följande kod:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Resultatet ser ut så här:
    
    ![Körning av TensorFlow-kod](./media/apache-spark-python-package-installation/tensorflow-execution.png "Kör TensorFlow-kod")

> [!NOTE]  
> Det finns två python-installationer i klustret. Spark kommer att använda den Anaconda python-installation `/usr/bin/anaconda/bin` som finns på och kommer att användas som standard i python 2,7-miljön. Om du vill använda python 3. x och installera paket i PySpark3-kärnan, använder du sökvägen `conda` till den körbara filen för miljön `-n` och använder parametern för att ange miljön. Exempelvis `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` `conda-forge` installerar kommandot paketet i python 3,5-miljön med hjälp av kanalen. `ggplot`

## <a name="seealso"></a>Se även
* [: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda externa paket med Jupyter-anteckningsböcker i Apache Spark kluster i HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
