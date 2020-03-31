---
title: Det går inte att skapa Jupyter-anteckningsbok i Azure HDInsight
description: I artikeln beskrivs felsökningssteg och möjliga lösningar för problem vid interaktion med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186791"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Det går inte att skapa Jupyter-anteckningsbok i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du startar en Jupyter-anteckningsbok visas ett felmeddelande som innehåller:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Orsak

En version matchar inte.

## <a name="resolution"></a>Lösning

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öppna `_version.py` genom att köra följande kommando:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Ändra **5** till **4** så att den ändrade raden visas på följande sätt:

    ```python
    version_info = (4, 0, 3)
    ```

    Spara ändringar genom att ange **Ctrl + X**, **Y**, **Retur**.

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, var `CLUSTERNAME` är namnet på klustret.

1. Starta om Tjänsten Jupyter.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
