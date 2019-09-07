---
title: Azure HDInsight-verktyg – PySpark interaktiv miljö för Visual Studio Code
description: Lär dig hur du använder Azure HDInsight-verktyg för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode, Azure HDInsight tools, Hive, python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 54adb2ec61d77ea429f7da1b88aa9347c1addfe0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736414"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurera den interaktiva PySpark-miljön för Visual Studio Code

Följande steg visar hur du konfigurerar den interaktiva PySpark-miljön i VS Code.

Vi använder kommandot **python/pip** för att bygga en virtuell miljö i din Start Sök väg. Om du vill använda en annan version måste du ändra standard versionen av **python/pip** kommando manuellt. Mer information finns i [Update-alternativ](https://linux.die.net/man/8/update-alternatives).

1. Installera [python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Installera python från [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installera pip från [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/). (Om det inte har installerats från python-installationen)
   + Verifiera att python och pip har installerats med följande kommandon. Valfritt
 
        ![Python pip-version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Vi rekommenderar att du installerar python manuellt i stället för att använda MacOS-standardversionen.


2. Installera **virtuell miljö** genom att köra kommandot nedan.
   
   ```
   pip install virtualenv
   ```

3. För Linux installerar du de nödvändiga paketen genom att köra kommandona nedan om du stöter på fel meddelandet.
   
    ![Python pip-version](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Starta om vs Code och gå sedan tillbaka till den skript redigerare som kör **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight för VS-kod: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda Azure HDInsight-verktyget för Visual Studio Code](hdinsight-for-vscode.md)
* [Använd Azure Toolkit for IntelliJ för att skapa och skicka Apache Spark Scala-program](spark/apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Skapa Apache Spark-program med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i ett Apache Spark-kluster för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
