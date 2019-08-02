---
title: RpcTimeoutException för Apache Spark Thrift-server i Azure HDInsight
description: Du ser 502 fel vid bearbetning av stora data mängder med hjälp av Apache Spark Thrift-Server
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 0d4f2f9c8392e5b81f5abb8c67548baefae611c9
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620836"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException för Apache Spark Thrift-server i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Spark-programmet Miss lyckas `org.apache.spark.rpc.RpcTimeoutException` med ett undantag och ett `Futures timed out`meddelande:, som i följande exempel:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`och `overhead limit exceeded` fel kan också visas `sparkthriftdriver.log` i som i följande exempel:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Orsak

Dessa fel orsakas av brist på minnes resurser under data bearbetning. Om Java skräp insamlings processen startar kan det leda till att Spark-programmet låser sig. Frågorna börjar ta slut och stoppa bearbetningen. `Futures timed out` Felet indikerar ett kluster under allvarlig stress.

## <a name="resolution"></a>Lösning

Öka kluster storleken genom att lägga till fler arbetsnoder eller öka minnes kapaciteten för de befintliga klusternoderna. Du kan också justera data pipelinen för att minska mängden data som bearbetas samtidigt.

`spark.network.timeout` Kontrollerar tids gränsen för alla nätverks anslutningar. Om du ökar nätverks tids gränsen kan det ta längre tid för vissa kritiska åtgärder att slutföras, men problemet löses inte helt och hållet.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
