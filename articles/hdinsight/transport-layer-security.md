---
title: Säkerhet på transport nivå i Azure HDInsight
description: TLS (Transport Layer Security) och SSL (Secure Sockets Layer) är krypterings protokoll som ger kommunikations säkerhet i ett dator nätverk.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 6e46cca28c049a794db617797d5f09e9f3b5720d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006899"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Säkerhet på transport nivå i Azure HDInsight

Anslutningar till HDInsight-klustret via den offentliga kluster slut punkten `https://CLUSTERNAME.azurehdinsight.net` är proxy via klusternoder. Dessa anslutningar skyddas med hjälp av ett protokoll som kallas TLS. Att framtvinga högre versioner av TLS på gateways förbättrar säkerheten för dessa anslutningar. Mer information om varför du bör använda nyare versioner av TLS finns i [lösa problemet med tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

Som standard godkänner Azure HDInsight-kluster TLS 1,2-anslutningar på offentliga HTTPS-slutpunkter och äldre versioner för bakåtkompatibilitet. Du kan kontrol lera den lägsta TLS-version som stöds på Gateway-noderna när klustret skapas, antingen med hjälp av Azure Portal eller en Resource Manager-mall. För portalen väljer du TLS-versionen från fliken **säkerhet + nätverk** när klustret skapas. Använd egenskapen **minSupportedTlsVersion** för en Resource Manager-mall vid distributions tiden. En exempel-mall finns i [snabb starts mal len för HDInsight-lägsta TLS 1,2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Den här egenskapen stöder tre värden: "1,0", "1,1" och "1,2", som motsvarar TLS 1.0 +, TLS 1.1 + och TLS 1.2 + respektive.


## <a name="next-steps"></a>Nästa steg

* [Planera ett virtuellt nätverk för Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md).
* [Nätverks säkerhets grupper](../virtual-network/security-overview.md).
