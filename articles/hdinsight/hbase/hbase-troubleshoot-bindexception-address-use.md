---
title: BindException – adress som redan används i Azure HDInsight
description: BindException – adress som redan används i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887350"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException – adress som redan används i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Omstarten på en Apache HBase Region Server kan inte slutföras. Från `region-server.log` katalogen `/var/log/hbase` på arbetsnoderna där regionserverns start misslyckas kan ett felmeddelande som liknar följande visas:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Orsak

Starta om Apache HBase-regionservrar under tung arbetsbelastningsaktivitet. Nedan är vad som händer bakom kulisserna när en användare initierar omstarten på HBase region serverns från Apache Ambari UI:

1. Ambari-agenten skickar en stoppbegäran till regionservern.

1. Ambari-agenten väntar i 30 sekunder på att regionservern ska stängas av graciöst

1. Om programmet fortsätter att ansluta till regionservern stängs servern inte av omedelbart. Tidsgränsen på 30 sekunder går ut innan avstängningen inträffar.

1. Efter 30 sekunder skickar Ambari-agenten`kill -9`ett force-kill () kommando till regionservern.

1. På grund av denna plötsliga avstängning, även om regionen serverprocessen blir dödad, kan `AddressBindException`porten i samband med processen inte släppas, vilket så småningom leder till .

## <a name="resolution"></a>Lösning

Minska belastningen på HBase-regionservrarna innan du startar en omstart. Det är också en bra idé att först spola alla bord. En referens om hur du spolar tabeller finns i [HDInsight HBase: Så här förbättrar du omstartstiden för Apache HBase-klustret genom att tömma tabeller](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Du kan också försöka starta om regionservrarna manuellt på arbetsnoderna med följande kommandon:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
