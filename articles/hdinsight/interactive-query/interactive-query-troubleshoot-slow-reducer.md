---
title: Reducer är långsam i Azure HDInsight
description: Reducer är långsam i Azure HDInsight från eventuell data skeva
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895171"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: Reducer är långsam i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör `insert into table1 partition(a,b) select a,b,c from table2` en fråga som frågeplanen startar en massa reducerare men data från varje partition går till en enda reducerare. Detta medför att frågan blir lika långsam som den tid det tar för den största partitionens reducer.

## <a name="cause"></a>Orsak

Öppna [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) och kontrollera `hive.optimize.sort.dynamic.partition`värdet för uppsättningen .

Värdet för den här variabeln är avsett att ställas in på sant/falskt baserat på datans art.

Om partitionerna i indatatabellen är mindre (säg mindre än 10), och så är `true`antalet utdatapartitioner, och variabeln är inställd på , medför detta att data sorteras och skrivs globalt med hjälp av en enda reducer per partition. Även om antalet tillgängliga reducerare är större, kan några reducerare släpa efter på grund av data skeva och max parallellism kan inte uppnås. När det `false`ändras till kan mer än en reducer hantera en enda partition och flera mindre filer skrivs ut, vilket resulterar i snabbare infogning. Detta kan påverka ytterligare frågor men på grund av förekomsten av mindre filer.

Värdet är `true` vettigt när antalet partitioner är större och data inte är skeva. I sådana fall kommer resultatet av kartfasen att skrivas ut så att varje partition hanteras av en enda reducer vilket resulterar i bättre efterföljande frågeprestanda.

## <a name="resolution"></a>Lösning

1. Försök att partitionera om data för att normalisera till flera partitioner.

1. Om #1 inte är möjligt anger du värdet för konfigurationen till false i beeline-session och försöker igen. `set hive.optimize.sort.dynamic.partition=false`. Det rekommenderas inte att ange värdet på false på klusternivå. Värdet av `true` är optimalt och ange parametern efter behov baserat på datas och frågas karaktär.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
