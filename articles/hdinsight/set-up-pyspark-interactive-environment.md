---
title: PySpark interaktiv miljö med Azure HDInsight Tools
description: Lär dig hur du använder Azure HDInsight Tools for Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393660"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurera den interaktiva miljön i PySpark för Visual Studio Code

Följande steg visar hur du konfigurerar den interaktiva miljön i PySpark i VS-kod.

Vi använder **python/pip-kommandot** för att skapa virtuell miljö i din hemsökväg. Om du vill använda en annan version måste du ändra standardversionen av **python/pip-kommandot** manuellt. Mer information se [uppdateringsalternativ](https://linux.die.net/man/8/update-alternatives).

1. Installera [Python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/).

   + Installera Python [https://www.python.org/downloads/](https://www.python.org/downloads/)från .
   + Installera pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) från (om den inte är installerad från Python-installationen).
   + Verifiera att Python och pip har installerats med hjälp av följande kommandon. (Valfritt)

        ![Kontrollera kommandot Python pip version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Vi rekommenderar att du installerar Python manuellt i stället för att använda standardversionen av macOS.

2. Installera **virtualenv** genom att köra kommandot nedan.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andra paket

Om du stöter på ett felmeddelande installerar du de nödvändiga paketen genom att köra följande kommandon:

   ![Installera libkrb5-paket för python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Starta om VS-koden och gå sedan tillbaka till skriptredigeraren som kör **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo

* HDInsight för VS-kod: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda Azure HDInsight Tool för Visual Studio-kod](hdinsight-for-vscode.md)
* [Använd Azure Toolkit för IntelliJ för att skapa och skicka Apache Spark Scala-program](spark/apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit för IntelliJ för att fjärrsöka Apache Spark-program via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i ett Apache Spark-kluster för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
