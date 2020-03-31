---
title: RpcTimeoutException för Apache Spark sparsamhet - Azure HDInsight
description: Du ser 502 fel vid bearbetning av stora datauppsättningar med Apache Spark sparsamhetsserver
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894273"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException för Apache Spark sparsamhet server i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Spark-programmet misslyckas med `org.apache.spark.rpc.RpcTimeoutException` ett `Futures timed out`undantag och ett meddelande: , som i följande exempel:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`och `overhead limit exceeded` fel kan också `sparkthriftdriver.log` visas i samma exempel som i följande exempel:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Orsak

Dessa fel orsakas av brist på minnesresurser under databearbetning. Om Java-skräpinsamlingsprocessen startar kan det leda till att Spark-programmet hängs upp. Frågor börjar time out och stoppa bearbetningen. Felet `Futures timed out` indikerar ett kluster under allvarlig stress.

## <a name="resolution"></a>Lösning

Öka klusterstorleken genom att lägga till fler arbetsnoder eller öka minneskapaciteten för de befintliga klusternoderna. Du kan också justera datapipelinen för att minska mängden data som bearbetas samtidigt.

Tidsgränsen `spark.network.timeout` styr tidsgränsen för alla nätverksanslutningar. Om du ökar tidsgränsen för nätverket kan det ge mer tid för vissa kritiska åtgärder att slutföras, men det löser inte problemet helt.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
