---
title: Apache HBase REST svarar inte på förfrågningar i Azure HDInsight
description: Lös problem med Apache HBase REST svarar inte på begär anden i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887180"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST svarar inte på förfrågningar i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

REST-tjänsten Apache HBase svarar inte på begär anden i Azure HDInsight.

## <a name="cause"></a>Orsak

Den möjliga orsaken kan vara att REST-tjänsten i Apache HBase läcker Sockets, vilket är särskilt vanligt när tjänsten har körts under en längre tid (till exempel månader). Från klientens SDK kan du se ett fel meddelande som liknar:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Upplösning

Starta om HBase REST med kommandot nedan efter ansluta till värden. Du kan också använda skript åtgärder för att starta om tjänsten på alla Worker-noder:

```bash
sudo service hdinsight-hbrest restart
```

Det här kommandot stoppar HBase regions server på samma värd. Du kan antingen starta HBase region server manuellt via Ambari eller låta Ambari automatisk omstart återställa HBase region Server automatiskt.

Om problemet kvarstår kan du installera följande minsknings skript som ett CRON-jobb som körs var 5: e minut på varje arbetsnod. Detta minsknings skript pingar REST-tjänsten och startar om den om REST-tjänsten inte svarar.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
