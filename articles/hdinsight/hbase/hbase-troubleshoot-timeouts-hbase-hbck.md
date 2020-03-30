---
title: Överskridna tidsgränser med kommandot ”hbase hbck” i Azure HDInsight
description: Time out problem med "hbase hbck" kommando vid fastställande av region tilldelningar
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887197"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: Timeouts med kommandot "hbase hbck" i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Stöt på timeout med `hbase hbck` kommando när du åtgärdar regiontilldelningar.

## <a name="cause"></a>Orsak

En potentiell orsak till timeout-problem när du använder `hbck` kommandot kan vara att flera regioner är i tillståndet "övergång" under lång tid. Du kan se dessa regioner som offline i HBase Master UI. Eftersom ett stort antal regioner försöker gå över kan HBase Master ta time out och inte ansluta dessa regioner igen.

## <a name="resolution"></a>Lösning

1. Logga in på HDInsight HBase-klustret med SSH.

1. Kör `hbase zkcli` kommando för att ansluta med Apache ZooKeeper skal.

1. Kör `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` eller kommandot.

1. Avsluta `hbase zkcli` från skalet med kommandot. `exit`

1. Starta om tjänsten Active HBase Master från Apache Ambari.From the Apache Ambari UI, restart the Active HBase Master service.

1. Kör `hbase hbck -fixAssignments`-kommandot.

1. Övervaka HBase Master UI "region i övergång" det avsnittet för att se till att ingen region fastnar.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
