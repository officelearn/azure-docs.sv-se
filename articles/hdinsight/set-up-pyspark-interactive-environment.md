---
title: Azure HDInsight Tools - konfigurera interaktiv PySpark-miljö för Visual Studio Code
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode, Azure HDInsight-verktyg, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: f7048665ea5bfbf6d4c1110a5823cf9acbfd0272
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058165"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Ställ in interaktiv PySpark-miljö för Visual Studio Code

Följande steg visar hur du installerar Python-paket genom att köra **HDInsight: interaktiv PySpark**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Ställ in interaktiv PySpark-miljö på macOS och Linux
Om du använder **python 3.x**, måste du använda kommandot **pip3** för följande steg:

1. Se till att **Python** och **pip** är installerade.
 
    ![Python-pip-version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installera Jupyter.
    ```
    sudo pip install jupyter
    ```
   Du kan se ett felmeddelande på Linux och macOS:

   ![Fel 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Installera **libkrb5 dev** (för Linux). Du kan se följande felmeddelande visas:

   ![Fel 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Installera **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Se till att **ipywidgets** har installerats korrekt genom att köra följande:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installera wrapper-kärnor](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installera wrapper-kärnor. Kör **pip visa sparkmagic**. Utdata visar sökvägen för den **sparkmagic** installation. 

    ![sparkmagic plats](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Gå till platsen och kör:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec installation](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Kontrollera installationsstatus för.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec lista](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    För kernlar som är tillgängliga: 
    - **python2** och **pysparkkernel** motsvarar **python 2.x**. 
    - **python3** och **pyspark3kernel** motsvarar **python 3.x**. 

8. Starta om VS Code och gå sedan tillbaka till Skriptredigeraren med **HDInsight: interaktiv PySpark**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight för VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure HDInsight-verktyg för Visual Studio Code](hdinsight-for-vscode.md)
* [Använd Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-appar](spark/apache-spark-intellij-tool-plugin.md)
* [Felsöka Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Felsöka Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
