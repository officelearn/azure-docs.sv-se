---
title: Lagringsundantag efter anslutningsåterställning i Azure HDInsight
description: Lagringsundantag efter anslutningsåterställning i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887231"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: Lagringsundantag efter anslutningsåterställning i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att skapa en ny Apache HBase-tabell.

## <a name="cause"></a>Orsak

Under en tabell trunkeringsprocess uppstod ett problem med lagringsanslutningen. Tabellposten togs bort i HBase-metadatatabellen. Alla utom en blob-fil togs bort.

Även om det inte `/hbase/data/default/ThatTable` fanns någon mapp blob kallas sitter i lagringen. WASB-drivrutinen hittade förekomsten av blob-filen ovanför och skulle `/hbase/data/default/ThatTable` inte tillåta att skapa någon blob som anropades eftersom den antog att de överordnade mapparna fanns, vilket innebär att det gick inte att skapa tabellen.

## <a name="resolution"></a>Lösning

1. Starta om den aktiva HMaster från Apache Ambari UI. Detta kommer att låta en av de två standby HMaster blir den aktiva och den nya aktiva HMaster kommer att ladda metadata tabell info. Således kommer du `already-deleted` inte att se tabellen i HMaster UI.

1. Du kan hitta den överblivna blob-filen från `hdfs dfs -ls /xxxxxx/yyyyy`gränssnittsverktyg som Cloud Explorer eller kommandot som körs . Kör `hdfs dfs -rmr /xxxxx/yyyy` för att ta bort blobben. Till exempel `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Nu kan du skapa en ny tabell med samma namn i HBase.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
