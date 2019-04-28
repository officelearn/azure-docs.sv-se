---
title: Azure HDInsight Tools - konfigurera interaktiv PySpark-miljö för Visual Studio Code
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode, Azure HDInsight-verktyg, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 882a071731837b0f7d03ac374a7c831a20a3927e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766410"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Ställ in interaktiv PySpark-miljö för Visual Studio Code

Följande steg visar hur du ställer in interaktiv PySpark-miljö i VS Code.

Vi använder **python-pip** kommando för att skapa virtuell miljö i din Home-sökväg. Om du vill använda en annan version, måste du ändra standardversionen av **python-pip** kommandot manuellt. Mer information finns i [uppdatering alternativ](https://linux.die.net/man/8/update-alternatives).

1. Installera [Python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Installera Python från [ https://www.python.org/downloads/ ](https://www.python.org/downloads/).
   + Installera pip från [ https://pip.pypa.io/en/stable/installing ](https://pip.pypa.io/en/stable/installing/). (Om det inte installeras från Python-installationen)
   + Kontrollera Python och pip är installerade med följande kommandon. (Valfritt)
 
        ![Python-pip-version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Vi rekommenderar att manuellt installera Python istället för att använda standardversionen MacOS.


2. Installera **virtuell miljö** genom att köra kommandot nedan.
   
   ```
   pip install virtualenv
   ```

3. Installera de nödvändiga paketen för Linux, genom att köra kommandona nedan om du får ett felmeddelande.
   
    ![Python-pip-version](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Starta om VS Code och gå sedan tillbaka till Skriptredigeraren med **HDInsight: Interaktiv PySpark**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight för VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure HDInsight-verktyg för Visual Studio Code](hdinsight-for-vscode.md)
* [Använd Azure Toolkit för IntelliJ för att skapa och skicka Apache Spark Scala-appar](spark/apache-spark-intellij-tool-plugin.md)
* [Felsöka Apache Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Felsöka Apache Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight-verktygen i Azure Toolkit för Eclipse för att skapa Apache Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
