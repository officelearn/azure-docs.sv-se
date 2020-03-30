---
title: Apache Spark långsamt när Azure HDInsight-lagring har många filer
description: Apache Spark-jobbet går långsamt när Azure-lagringsbehållaren innehåller många filer i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894330"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark-jobbet går långsamt när Azure Storage-containern innehåller många filer i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör ett HDInsight-kluster blir Apache Spark-jobbet som skriver till Azure-lagringsbehållare långsamt när det finns många filer/undermappar. Det tar till exempel 20 sekunder när du skriver till en ny behållare, men ungefär 2 minuter när du skriver till en behållare som har 200 000 filer.

## <a name="cause"></a>Orsak

Detta är ett känt Spark-problem. Långsamheten kommer från `ListBlob` `GetBlobProperties` och operationer under Spark-jobbkörningen.

Om du vill spåra partitioner `FileStatusCache` måste Spark underhålla en som innehåller information om katalogstruktur. Med hjälp av den här cachen kan Spark tolka sökvägarna och vara medveten om tillgängliga partitioner. Fördelen med att spåra partitioner är att Spark bara vidrör nödvändiga filer när du läser data. För att hålla den här informationen uppdaterad måste Spark, när du skriver nya data, lista alla filer under katalogen och uppdatera cacheminnet.

I Spark 2.1, medan vi inte behöver uppdatera cachen efter varje skrivning, kommer Spark att kontrollera om en befintlig partitionskolumn matchar med den föreslagna i den aktuella skrivbegäran, så det kommer också att leda till listningsåtgärder i början av varje skrivning.

I Spark 2.2 bör det här prestandaproblemet åtgärdas när du skriver data med tilläggsläge.

## <a name="resolution"></a>Lösning

När du skapar en partitionerad datauppsättning är det viktigt att använda ett partitioneringsschema som `FileStatusCache`begränsar antalet filer som Spark har att lista för att uppdatera .

För varje Nth mikro parti där N % 100 == 0 (100 är bara ett exempel), flytta befintliga data till en annan katalog, som kan läsas in av Spark.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
