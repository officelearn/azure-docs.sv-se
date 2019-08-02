---
title: Apache Spark strömnings jobb tar längre tid än vanligt att bearbeta i Azure HDInsight
description: Apache Spark strömnings jobb tar längre tid än vanligt att bearbeta i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679437"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Scenario: Apache Spark strömnings jobb tar längre tid än vanligt att bearbeta i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du ser att några av de Apache Spark strömmande jobben är långsamma eller tar längre tid än vanligt att bearbeta. För Spark-strömmande program motsvarar varje batch med meddelanden ett jobb som skickats till Spark. Om ett jobb normalt tar X sekunder att bearbeta, kan det ibland ta 2-3 minuter mer än vanligt.

## <a name="cause"></a>Orsak

En möjlig orsak är att Apache Kafkas producenten tar mer än två minuter att slutföra skrivningen till Kafka-klustret. Om du vill felsöka problemet med Kafka kan du lägga till viss loggning i koden som använder en Kafka-tillverkare för att skicka meddelanden och korrelera dem med loggarna från Kafka-klustret.

En annan möjlig orsak är att frekvent läsningar och skrivningar till WASB kan orsaka att efterföljande Micro-batchar är i vänte läge. WASB-implementeringen `Filesystem.listStatus` av är mycket långsam på grund `O(n!)` av en algoritm för att ta bort dubbletter. Den använder för mycket minne på grund av den extra konverteringen `FileMetadata` från `FileStatus` `BlobListItem` till. Algoritmen tar till exempel över 30 minuter för att lista 700 000-filer. Så om `ListBlobs` anropas aggressivt av SparkSQL varje Micro batch kommer det att orsaka efterföljande Micro-batchar för att få en högre tids fördröjning. [Den här korrigeringen](https://issues.apache.org/jira/browse/HADOOP-15547) åtgärdar problemet, men om det saknas i din miljö `ListBlobs` kommer det att uppleva hög latens. Även om du tar bort filer varje timme, måste listan i Server delen iterera över alla rader (inklusive borttagna) eftersom skräp insamlings processen inte har slutförts än. Även om korrigeringen kan lösa vissa problem kan skräp insamlings problemet fortfarande orsaka fördröjning i Stream-bearbetningen av batchar.

## <a name="resolution"></a>Lösning

Använd korrigeringen [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) . Om detta inte är möjligt kan du använda HDFS som kontroll punkts plats. Ange `checkpointDirectory` till något som: `hdfs://mycluster/checkpoint`.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
