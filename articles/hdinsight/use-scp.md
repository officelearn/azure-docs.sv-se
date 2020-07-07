---
title: Använd SCP med Apache Hadoop i Azure HDInsight
description: Det här dokumentet innehåller information om hur du ansluter till HDInsight med hjälp av SSH-och SCP-kommandona.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82188437"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Använd SCP med Apache Hadoop i Azure HDInsight

Den här artikeln innehåller information om hur du överför filer på ett säkert sätt med ditt HDInsight-kluster.

## <a name="copy-files"></a>Kopiera filer

Verktyget `scp` kan användas för att kopiera filer till och från enskilda noder i ett kluster. Med följande kommando kopieras till exempel katalogen `test.txt` från det lokala systemet till den primära huvudnoden:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Eftersom ingen sökväg har angetts efter `:` placeras filen i hemkatalogen `sshuser`.

I följande exempel kopieras filen `test.txt` från hemkatalogen `sshuser` på den primära huvudnoden till det lokala systemet:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` har bara åtkomst tull filsystemet för enskilda noder i klustret. Det kan inte användas för att komma åt data i HDFS-kompatibla lagrings utrymmen för klustret.

Använd `scp` när du behöver ladda upp en resurs för en SSH-session. Ladda exempelvis upp ett Python-skript och kör det sedan från en SSH-session.

I följande dokument hittar du information om att läsa in data direkt till det HDFS-kompatibla lagringsutrymmet:

* [HDInsight med Azure Storage](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight med Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Nästa steg

* [Använda SSH med HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
