---
title: PySpark interaktiv miljö med Azure HDInsight-verktyg
description: Lär dig hur du använder Azure HDInsight-verktyg för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VScode, Azure HDInsight tools, Hive, python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 47f774f7b848e34aa7dc219ee4b08fb083cbd813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188216"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurera den interaktiva PySpark-miljön för Visual Studio Code

Följande steg visar hur du konfigurerar den interaktiva PySpark-miljön i VS Code.

Vi använder kommandot **python/pip** för att bygga en virtuell miljö i din Start Sök väg. Om du vill använda en annan version måste du ändra standard versionen av **python/pip** kommando manuellt. Mer information finns i [Update-alternativ](https://linux.die.net/man/8/update-alternatives).

1. Installera [python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/).

   * Installera python från [https://www.python.org/downloads/](https://www.python.org/downloads/).
   * Installera pip från [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (om det inte är installerat från python-installationen).
   * Kontrol lera att python och pip har installerats med hjälp av följande kommandon. (Valfritt)

        ![Kontrol lera kommandot python pip version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Vi rekommenderar att du installerar python manuellt i stället för att använda macOS-standardversionen.

2. Installera **virtuell miljö** genom att köra kommandot nedan.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andra paket

Om du kommer över ett fel meddelande installerar du de nödvändiga paketen genom att köra följande kommandon:

   ![Installera libkrb5-paket för python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Starta om VS Code och gå sedan tillbaka till skript redigeraren som kör **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo

* HDInsight för VS Code: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda Azure HDInsight-verktyget för Visual Studio Code](hdinsight-for-vscode.md)
* [Använd Azure Toolkit for IntelliJ för att skapa och skicka Apache Spark Scala-program](spark/apache-spark-intellij-tool-plugin.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
