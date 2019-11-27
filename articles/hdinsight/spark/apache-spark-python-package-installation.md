---
title: Skript åtgärd för python-paket med Jupyter på Azure HDInsight
description: Stegvisa instruktioner för hur du använder skript åtgärd för att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda externa python-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215576"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Hantera Python-miljön i Azure HDInsight på ett säkert sätt med skriptåtgärd

> [!div class="op_single_selector"]
> * [Använda cell Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Använda skript åtgärd](apache-spark-python-package-installation.md)

HDInsight har två inbyggda python-installationer i Spark-klustret, Anaconda python 2,7 och python 3,5. I vissa fall behöver kunderna anpassa python-miljön, t. ex. installera externa python-paket eller en annan python-version. I den här artikeln visar vi bästa praxis för säker hantering av python-miljöer för ett [Apache Spark](https://spark.apache.org/) kluster i HDInsight.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Om du inte redan har ett Spark-kluster i HDInsight Linux kan du köra skript åtgärder när klustret skapas. Gå till dokumentationen om [hur du använder anpassade skript åtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för program med öppen källkod som används i HDInsight-kluster

Microsoft Azure HDInsights tjänsten använder ett eko system med tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod. Mer information finns på [webbplatsen för vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare en nivå av stöd för inbyggda komponenter.

Det finns två typer av komponenter med öppen källkod som är tillgängliga i HDInsight-tjänsten:

* **Inbyggda komponenter** – dessa komponenter är förinstallerade i HDInsight-kluster och tillhandahåller kärn funktioner i klustret. Till exempel, Apache Hadoop garn Resource Manager, Apache Hive frågespråket (HiveQL) och Mahout-biblioteket tillhör den här kategorin. En fullständig lista över kluster komponenter finns i [Nyheter i Apache Hadoop kluster versioner från HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Anpassade komponenter** – du, som en användare av klustret, kan installera eller använda i din arbets belastning, vilken komponent som helst som är tillgänglig i communityn eller som du har skapat.

> [!IMPORTANT]
> Komponenter som ingår i HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet, eller så kan de be dig att tillhandahålla tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex. [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser på [https://apache.org](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Förstå den standardinställda python-installationen

HDInsight Spark-kluster skapas med Anaconda-installation. Det finns två python-installationer i klustret, Anaconda python 2,7 och python 3,5. I tabellen nedan visas standard inställningarna för python för Spark, livy och Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Sökväg|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Standard är inställt på 2,7|Saknas|
|Livy|Standard är inställt på 2,7|Saknas|
|Jupyter|PySpark-kernel|PySpark3-kernel|

## <a name="safely-install-external-python-packages"></a>Installera externa python-paket på ett säkert sätt

HDInsight-kluster är beroende av den inbyggda python-miljön, både python 2,7 och python 3,5. Direkt installation av anpassade paket i de inbyggda standard miljöerna kan leda till oväntade ändringar i biblioteks versionen och även dela upp klustret. Följ stegen nedan för att på ett säkert sätt installera anpassade externa python-paket för dina Spark-program.

1. Skapa en virtuell python-miljö med Conda. En virtuell miljö ger ett isolerat utrymme för dina projekt utan att behöva dela andra. När du skapar den virtuella python-miljön kan du ange python-version som du vill använda. Observera att du fortfarande behöver skapa en virtuell miljö även om du vill använda python 2,7 och 3,5. Detta görs för att se till att klustrets standard miljö inte får plats. Kör skript åtgärder på klustret för alla noder med skriptet nedan för att skapa en virtuell python-miljö. 

    -   `--prefix` anger en sökväg där en virtuell Conda-miljö bor. Det finns flera konfigurationer som behöver ändras ytterligare utifrån den sökväg som anges här. I det här exemplet använder vi py35new, eftersom klustret redan har en befintlig virtuell miljö som heter py35.
    -   `python=` anger python-versionen för den virtuella miljön. I det här exemplet använder vi version 3,5, samma version som klustret som skapats i en. Du kan också använda andra python-versioner för att skapa den virtuella miljön.
    -   `anaconda` anger package_spec som Anaconda för att installera Anaconda-paket i den virtuella miljön.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Installera externa python-paket i den virtuella miljön som skapats om det behövs. Kör skript åtgärder på klustret för alla noder med skriptet nedan för att installera externa python-paket. Du måste ha sudo-behörighet här för att kunna skriva filer till den virtuella miljömappen.

    Du kan söka i [paket indexet](https://pypi.python.org/pypi) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som gjorts tillgängliga via [Conda-falska](https://conda-forge.org/feedstocks/).

    -   `seaborn` är det paket namn som du vill installera.
    -   `-n py35new` ange namnet på den virtuella miljön som du nyss skapade. Se till att ändra namnet på motsvarande sätt baserat på hur du skapar den virtuella miljön.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Om du inte känner till namnet på den virtuella miljön kan du använda SSH till noden huvuden i klustret och köra `/usr/bin/anaconda/bin/conda info -e` för att visa alla virtuella miljöer.

3. Ändra Spark-och livy-konfiguration och peka på den virtuella miljön som skapats.

    1. Öppna Ambari UI, gå till sidan Spark2, fliken konfigurationer.
    
        ![Ändra Spark-och livy-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Expandera Advanced livy2-kuvert, Lägg till under uttryck i nederkant. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ändra livy-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expandera Advanced spark2-kuvert, Ersätt den befintliga exporten PYSPARK_PYTHON-instruktionen längst ned. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ändra Spark-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Spara ändringarna och starta om berörda tjänster. Dessa ändringar kräver en omstart av Spark2-tjänsten. Ambari UI upprättar en nödvändig omstart, klicka på Starta om för att starta om alla berörda tjänster.

        ![Ändra Spark-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Om du vill använda den nya virtuella miljön på Jupyter. Du måste ändra Jupyter-konfiguration och starta om Jupyter. Kör skript åtgärder på alla huvudnoder med nedanstående instruktion för att peka Jupyter mot den nya virtuella miljön som skapats. Se till att ändra sökvägen till det prefix du angav för den virtuella miljön. När du har kört den här skript åtgärden startar du om Jupyter-tjänsten via Ambari-ANVÄNDARGRÄNSSNITTET för att göra den här ändringen tillgänglig.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Du kan dubbelt kontrol lera python-miljön i Jupyter Notebook genom att köra nedanstående kod:

    ![Kontrol lera python-versionen i Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Kända problem

Det finns ett känt fel för Anaconda-versionen 4.7.11 och 4.7.12. Om du ser att skript åtgärderna låser sig vid `"Collecting package metadata (repodata.json): ...working..."` och att det inte går att `"Python script has been killed due to timeout after waiting 3600 secs"`. Du kan ladda ned [skriptet](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) och köra det som skript åtgärder på alla noder för att åtgärda problemet.

Du kan kontrol lera din Anaconda-version genom att använda SSH till noden kluster huvud och köra `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
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
