---
title: Azure HDInsight-verktyg, konfigurera PySpark interaktiv miljö för Visual Studio Code | Microsoft Docs
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio-koden för att skapa och skicka frågor och skript.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurera PySpark interaktiv miljö för Visual Studio Code

Följande steg visar hur du installerar Python-paket genom att köra **HDInsight: PySpark interaktiva**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Ställ in den interaktiva miljön PySpark i macOS- och Linux
Om du använder **python 3.x**, måste du använda kommandot **pip3** för följande steg:

1. Kontrollera att **Python** och **pip** är installerade.
 
    ![Python pip-versionen](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installera Jupyter.
    ```
    sudo pip install jupyter
    ```
   Du kan se följande felmeddelande på Linux- och macOS:

   ![Fel 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Installera **libkrb5 dev** (för Linux). Du kan se följande felmeddelande:

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
   ![Installera wrapper kärnor](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installera wrapper-kärnor. Kör **pip visa sparkmagic**. Utdata visar sökvägen för den **sparkmagic** installation. 

    ![sparkmagic plats](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Gå till platsen och kör sedan:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec installera](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Kontrollera installationsstatus.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec lista](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    För kernlar som är tillgängliga: 
    - **python2** och **pysparkkernel** motsvarar **python 2.x**. 
    - **python3** och **pyspark3kernel** motsvarar **python 3.x**. 

8. Starta om VS-kod och gå sedan tillbaka till Skriptredigeraren med **HDInsight: PySpark interaktiva**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight VS-kod: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure HDInsight-verktyg för Visual Studio Code](hdinsight-for-vscode.md)
* [Använda Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-program](spark/apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
