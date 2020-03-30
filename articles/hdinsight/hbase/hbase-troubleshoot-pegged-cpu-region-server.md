---
title: Knuten CPU i Apache HBase-kluster - Azure HDInsight
description: Felsöka knuten processor på regionserver i Apache HBase-kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887316"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: Knuten CPU på regionserver i Apache HBase-kluster i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache HBase region serverprocessen börjar uppta nära 200% CPU, vilket gör att varningar att skjuta på HBase Master process och kluster för att inte fungera med full kapacitet.

## <a name="cause"></a>Orsak

Om du kör HBase-kluster v3.4 kan du ha drabbats av en potentiell bugg som orsakas av uppgradering av jdk till version 1.7.0_151. Symptomet vi ser är region serverprocessen börjar upptar nära 200% CPU (för att verifiera detta köra `top` kommandot, om det finns en process som `ps -aux | grep` upptar nära 200% CPU få sin pid och bekräfta att det är region server process genom att köra).

## <a name="resolution"></a>Lösning

1. Installera jdk 1.8 på alla noder i klustret enligt nedan:

    * Kör skriptåtgärden `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`. Var noga med att välja alternativet att köras på alla noder.

    * Du kan också logga in på varje enskild `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`nod och köra kommandot .

1. Gå till Ambari `https://<clusterdnsname>.azurehdinsight.net`UI - .

1. Navigera till **HBase->Configs->Advanced->Advanced** `hbase-env configs` och ändra variabeln `JAVA_HOME` `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`till . Spara konfigurationsändringen.

1. [Valfritt men rekommenderat] [Rensa alla tabeller i klustret](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Från Ambari UI igen, starta om alla HBase-tjänster som behöver startas om.

1. Beroende på data i klustret kan det ta några minuter till upp till en timme för klustret att nå stabilt tillstånd. Det sätt du bekräftar att klustret når stabilt tillstånd är genom att antingen kontrollera HMaster UI (alla regionservrar ska vara aktiva) från Ambari (uppdatera) eller från headnode kör HBase-skalet och sedan köra statuskommandot.

Kontrollera att uppgraderingen lyckades genom att kontrollera att de relevanta HBase-processerna har startats med lämplig java-version – till exempel för regionserverkontroll som:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
