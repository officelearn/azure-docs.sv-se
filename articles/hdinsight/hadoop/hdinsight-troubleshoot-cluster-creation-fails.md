---
title: Felsöka klusterskapande fel med Azure HDInsight
description: Lär dig hur du felsöker problem med att skapa Apache-kluster för Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 3af7515995a305f41fb9b9f85deb9107de51c622
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453497"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Felsöka klusterskapande fel med Azure HDInsight

Följande problem är de vanligaste grundorsakerna till klusterskapande fel:

- Problem med behörighet
- Begränsningar av resursprinciper
- Brandväggar
- Resurslås
- Komponentversioner som inte stöds
- Begränsningar för lagringskontonamn
- Avbrott i tjänsten

## <a name="permissions-issues"></a>Problem med behörigheter

Om du använder Azure Data Lake Storage Gen2 `AmbariClusterCreationFailedErrorCode`:::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support.":::och får felmeddelandet " " ", öppna Azure-portalen, gå till ditt lagringskonto och under Åtkomstkontroll (IAM) kontrollerar du att **storage blob Data Contributor** eller rollen Storage **Blob Data Owner** har tilldelat åtkomst till den **användartilldelade hanterade identiteten** för prenumerationen. Detaljerade anvisningar finns i [Konfigurera behörigheter för den hanterade identiteten i Data Lake Storage Gen2-kontot](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Om du använder Azure Data Lake Storage Gen1 läser du installations- och konfigurationsinstruktioner [här](../hdinsight-hadoop-use-data-lake-store.md). DataSjölagringsgen1 stöds inte för HBase-kluster och stöds inte i HDInsight version 4.0.

Om du använder Azure Storage kontrollerar du att lagringskontonamnet är giltigt när klustret skapas.

## <a name="resource-policy-restrictions"></a>Begränsningar av resursprinciper

Prenumerationsbaserade Azure-principer kan neka skapandet av offentliga IP-adresser. Skapande av HDInsight-klustret kräver två offentliga IP-adresser.  

I allmänhet kan följande principer påverka skapandet av kluster:

* Principer som förhindrar att IP-adress skapas & belastningsutjämnare i prenumerationen.
* Princip som förhindrar att lagringskonto skapas.
* Princip som förhindrar borttagning av nätverksresurser (IP-adress /belastningsutjämning).

## <a name="firewalls"></a>Brandväggar

Brandväggar på ditt virtuella nätverks- eller lagringskonto kan neka kommunikation med HDInsight-hanterings-IP-adresser.

Tillåt trafik från IP-adresserna i tabellen nedan.

| Källans IP-adress | Mål | Riktning |
|---|---|---|
| 168.61.49.99 | *:443 | Inkommande |
| 23.99.5.239 | *:443 | Inkommande |
| 168.61.48.131 | *:443 | Inkommande |
| 138.91.141.162 | *:443 | Inkommande |

Lägg också till IP-adresser som är specifika för den region där klustret skapas. Se [IP-adresser för HDInsight-hantering](../hdinsight-management-ip-addresses.md) för en lista över adresserna för varje Azure-region.

Om du använder en expressväg eller en egen anpassad DNS-server läser du [Planera ett virtuellt nätverk för Azure HDInsight – ansluta flera nätverk](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Resurser lås  

Kontrollera att det inte finns några [lås i det virtuella nätverket och resursgruppen](../../azure-resource-manager/management/lock-resources.md). Kluster kan inte skapas eller tas bort om resursgruppen är låst. 

## <a name="unsupported-component-versions"></a>Komponentversioner som inte stöds

Kontrollera att du använder en [version av Azure HDInsight](../hdinsight-component-versioning.md) som stöds och eventuella [Apache Hadoop-komponenter](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) i din lösning.  

## <a name="storage-account-name-restrictions"></a>Begränsningar för lagringskontonamn

Lagringskontonamn får inte vara fler än 24 tecken och får inte innehålla ett specialtecken. Dessa begränsningar gäller även för standardnamnet för containrar i lagringskontot.

Andra namngivningsbegränsningar gäller även för att skapa kluster. Se [Begränsningar för klusternamn](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name), för mer information.

## <a name="service-outages"></a>Avbrott i tjänsten

Kontrollera [Azure-status](https://status.azure.com) för eventuella avbrott eller serviceproblem.

## <a name="next-steps"></a>Nästa steg

* [Utöka Azure HDInsight med hjälp av ett virtuellt Azure-nätverk](../hdinsight-plan-virtual-network-deployment.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Använda Azure-lagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md)
* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](../hdinsight-hadoop-provision-linux-clusters.md)
