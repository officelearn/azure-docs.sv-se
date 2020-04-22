---
title: Transportlagersäkerhet i Azure HDInsight
description: Transportlagersäkerhet (TLS) och SSL (Secure Sockets Layer) är kryptografiska protokoll som ger kommunikationssäkerhet över ett datornätverk.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771968"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Transportlagersäkerhet i Azure HDInsight

Anslutningar till HDInsight-klustret via `https://CLUSTERNAME.azurehdinsight.net` slutpunkten för det offentliga klustret proxyseras via klustergatewaynoder. Dessa anslutningar är säkrade med hjälp av ett protokoll som kallas TLS. Genom att tillämpa högre versioner av TLS på gateways förbättras säkerheten för dessa anslutningar. Mer information om varför du bör använda nyare versioner av TLS finns i [Lösa TLS 1.0-problemet](https://docs.microsoft.com/security/solving-tls1-problem).

Som standard accepterar Azure HDInsight-kluster TLS 1.2-anslutningar på offentliga HTTPS-slutpunkter och äldre versioner för bakåtkompatibilitet. Du kan styra den minsta TLS-versionen som stöds på gatewaynoderna när kluster skapas med hjälp av antingen Azure-portalen eller en Resource Manager-mall. För portalen väljer du TLS-versionen på fliken **Säkerhet + nätverk** när klustret skapas. Använd egenskapen **minSupportedTlsVersion** för en Resource Manager-mall vid distributionen. En exempelmall finns i [hdinsight minimum TLS 1.2 Quickstart template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Den här egenskapen stöder tre värden: "1,0", "1,1" och "1,2", vilket motsvarar TLS 1,0+, TLS 1.1+ respektive TLS 1.2+ .

> [!IMPORTANT]
> Från och med den 30 juni 2020 kommer Azure HDInsight att framtvinga TLS 1.2 eller senare versioner för alla HTTPS-anslutningar. Vi rekommenderar att du ser till att alla dina klienter är redo att hantera TLS 1.2 eller senare versioner. Mer information finns i [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Nästa steg

* [Planera ett virtuellt nätverk för Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md).
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
