---
title: Apache HBase REST svarar inte på begäranden i Azure HDInsight
description: Lös problemet med Apache HBase REST som inte svarar på begäranden i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887180"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST svarar inte på begäranden i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache HBase REST-tjänsten svarar inte på begäranden i Azure HDInsight.

## <a name="cause"></a>Orsak

Den möjliga orsaken här kan vara Apache HBase REST-tjänsten är läckande uttag, vilket är särskilt vanligt när tjänsten har varit igång under en lång tid (till exempel månader). Från klienten SDK kan du se ett felmeddelande som liknar:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Lösning

Starta om HBase REST med kommandot nedan efter SSHing till värden. Du kan också använda skriptåtgärder för att starta om den här tjänsten på alla arbetsnoder:

```bash
sudo service hdinsight-hbrest restart
```

Det här kommandot stoppar HBase Region Server på samma värd. Du kan antingen manuellt starta HBase Region Server via Ambari eller låta Ambari automatiskt starta om funktionen återställa HBase Region Server automatiskt.

Om problemet kvarstår kan du installera följande begränsningsskript som ett CRON-jobb som körs var femte minut på varje arbetsnod. Det här begränsningsskriptet pingar REST-tjänsten och startar om den om REST-tjänsten inte svarar.

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

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
