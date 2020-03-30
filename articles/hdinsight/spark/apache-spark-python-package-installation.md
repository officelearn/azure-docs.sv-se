---
title: Skriptåtgärd för Python-paket med Jupyter på Azure HDInsight
description: Steg-för-steg-instruktioner om hur du använder skriptåtgärd för att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda externa python-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129613"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Hantera Python-miljön i Azure HDInsight på ett säkert sätt med skriptåtgärd

> [!div class="op_single_selector"]
> * [Använda cellmagi](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Använda skriptåtgärd](apache-spark-python-package-installation.md)

HDInsight har två inbyggda Python-installationer i Spark-klustret, Anaconda Python 2.7 och Python 3.5. I vissa fall måste kunderna anpassa Python-miljön, till exempel installera externa Python-paket eller en annan Python-version. I den här artikeln visar vi bästa praxis för att hantera Python-miljöer på ett säkert sätt för ett [Apache Spark-kluster](./apache-spark-overview.md) på HDInsight.

## <a name="prerequisites"></a>Krav

Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md). Om du inte redan har ett Spark-kluster på HDInsight kan du köra skriptåtgärder när klustret skapas. Besök dokumentationen om [hur du använder anpassade skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för programvara med öppen källkod som används i HDInsight-kluster

Microsoft Azure HDInsight-tjänsten använder ett ekosystem av tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän supportnivå för tekniker med öppen källkod. Mer information finns på [webbplatsen för Vanliga frågor och svar om Azure Support](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare stöd för inbyggda komponenter.

Det finns två typer av komponenter med öppen källkod som är tillgängliga i HDInsight-tjänsten:

|Komponent |Beskrivning |
|---|---|
|Inbyggd|Dessa komponenter är förinstallerade på HDInsight-kluster och tillhandahåller kärnfunktioner i klustret. Apache Hadoop YARN Resource Manager, frågespråket Apache Hive (HiveQL) och Mahout-biblioteket tillhör till exempel den här kategorin. En fullständig lista över klusterkomponenter finns i [Nyheter i Apache Hadoop-klusterversionerna från HDInsight](../hdinsight-component-versioning.md).|
|Anpassat|Du som användare av klustret kan installera eller använda alla komponenter som är tillgängliga i communityn eller som du har skapat i arbetsbelastningen.|

> [!IMPORTANT]
> Komponenter som medföljer HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som är relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimligt stöd som hjälper dig att felsöka problemet ytterligare. Microsoft-support kan lösa problemet eller så kan de be dig att engagera tillgängliga kanaler för tekniker med öppen källkod där djup expertis för den tekniken hittas. Till exempel finns det många community webbplatser som kan användas, som: [https://stackoverflow.com](https://stackoverflow.com) [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Även Apache projekt har [https://apache.org](https://apache.org)projektwebbplatser på , till exempel: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Förstå standardinstallation av Python

HDInsight Spark-klustret skapas med Anaconda-installation. Det finns två Python-installationer i klustret, Anaconda Python 2.7 och Python 3.5. Tabellen nedan visar standardinställningarna för Python för Spark, Livy och Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Sökväg|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Standard inställd på 2,7|Ej tillämpligt|
|Livy|Standard inställd på 2,7|Ej tillämpligt|
|Jupyter|PySpark-kärna|PySpark3-kärna|

## <a name="safely-install-external-python-packages"></a>Installera externa Python-paket på ett säkert sätt

HDInsight-klustret är beroende av den inbyggda Python-miljön, både Python 2.7 och Python 3.5. Om du installerar anpassade paket direkt i de vanliga inbyggda miljöerna kan det orsaka oväntade ändringar av biblioteksversionen och bryta klustret ytterligare. Följ nedanstående steg för att säkert kunna installera anpassade externa Python-paket för dina Spark-program.

1. Skapa virtuell Python-miljö med conda. En virtuell miljö ger ett isolerat utrymme för dina projekt utan att bryta andra. När du skapar den virtuella Python-miljön kan du ange pythonversion som du vill använda. Observera att du fortfarande behöver skapa virtuell miljö även om du vill använda Python 2.7 och 3.5. Detta för att se till att klustrets standardmiljö inte blir trasig. Kör skriptåtgärder i klustret för alla noder med nedanstående skript för att skapa en virtuell Python-miljö.

    -   `--prefix`anger en sökväg där en conda virtuell miljö finns. Det finns flera configs som måste ändras ytterligare baserat på den väg som anges här. I det här exemplet använder vi py35new, eftersom klustret redan har en befintlig virtuell miljö som kallas py35.
    -   `python=`anger Python-versionen för den virtuella miljön. I det här exemplet använder vi version 3.5, samma version som klustret som är inbyggt i ett. Du kan också använda andra Python-versioner för att skapa den virtuella miljön.
    -   `anaconda`anger package_spec som anaconda för att installera Anaconda-paket i den virtuella miljön.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installera externa Python-paket i den skapade virtuella miljön om det behövs. Kör skriptåtgärder i klustret för alla noder med nedanstående skript för att installera externa Python-paket. Du måste ha sudo privilegium här för att skriva filer till den virtuella miljön mappen.

    Du kan söka i [paketindexet](https://pypi.python.org/pypi) efter en komplett lista över tillgängliga paket. Du kan också få en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som gjorts tillgängliga via [conda-smedja](https://conda-forge.org/feedstocks/).

    Använd nedan kommando om du vill installera ett bibliotek med sin senaste version:

    - Använd conda kanal:

        -   `seaborn`är det paketnamn som du vill installera.
        -   `-n py35new`ange det virtuella miljönamn som just skapas. Se till att ändra namnet på motsvarande sätt baserat på din virtuella miljö skapas.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Eller använd PyPi repo, ändra `seaborn` och `py35new` motsvarande:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Använd nedan kommando om du vill installera ett bibliotek med en viss version:

    - Använd conda kanal:

        -   `numpy=1.16.1`är paketets namn och version som du vill installera.
        -   `-n py35new`ange det virtuella miljönamn som just skapas. Se till att ändra namnet på motsvarande sätt baserat på din virtuella miljö skapas.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Eller använd PyPi repo, ändra `numpy==1.16.1` och `py35new` motsvarande:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Om du inte känner till det virtuella miljönamnet kan du SSH `/usr/bin/anaconda/bin/conda info -e` till huvudnoden i klustret och köra för att visa alla virtuella miljöer.

3. Ändra Spark och Livy configs och peka på den skapade virtuella miljön.

    1. Öppna Ambari UI, gå till Spark2 sida, Configs fliken.

        ![Ändra Spark och Livy config genom Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Expandera Avancerad livy2-env, lägg till nedanstående satser längst ner. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ändra Livy config genom Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expandera Avancerad spark2-env, ersätt den befintliga PYSPARK_PYTHON-satsen längst ned. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ändra Spark config genom Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Spara ändringarna och starta om berörda tjänster. Dessa ändringar kräver en omstart av Spark2-tjänsten. Ambari UI kommer att fråga en obligatorisk omstart påminnelse, klicka på Starta om för att starta om alla berörda tjänster.

        ![Ändra Spark config genom Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Om du vill använda den nya skapade virtuella miljön på Jupyter. Du måste ändra Jupyter configs och starta Om Jupyter. Kör skriptåtgärder på alla rubriknoder med nedanstående sats för att peka Jupyter på den nya skapade virtuella miljön. Se till att ändra sökvägen till det prefix som du angav för den virtuella miljön. När du har kört den här skriptåtgärden startar du om Jupyter-tjänsten via Ambari UI för att göra den här ändringen tillgänglig.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Du kan dubbla bekräfta Python-miljön i Jupyter Notebook genom att köra under kod:

    ![Kontrollera Python-versionen i Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Kända problem

Det finns en känd bugg för Anaconda version 4.7.11, 4.7.12 och 4.8.0. Om du ser dina `"Collecting package metadata (repodata.json): ...working..."` skriptåtgärder `"Python script has been killed due to timeout after waiting 3600 secs"`hänga på och misslyckas med . Du kan hämta [skriptet](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) och köra det som skriptåtgärder på alla noder för att åtgärda problemet.

Om du vill kontrollera Anaconda-versionen kan du SSH `/usr/bin/anaconda/bin/conda --v`till klusterhuvudnoden och köra .

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
