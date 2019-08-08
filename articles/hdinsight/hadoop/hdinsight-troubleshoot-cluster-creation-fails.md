---
title: Felsöka kluster skapande fel med Azure HDInsight
description: Lär dig hur du felsöker problem med att skapa kluster för Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857253"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Felsöka kluster skapande fel med Azure HDInsight

Följande problem är de vanligaste orsakerna till kluster skapande fel:

- Behörighets problem
- Begränsningar för resurs principer
- Brandväggar
- Resurslås
- Komponent versioner som inte stöds
- Begränsningar för lagrings konto namn
- Drift avbrott för tjänsten

## <a name="permissions-issues"></a>Problem med behörigheter

Om du använder Data Lake Storage gen 2, se till att den tilldelade hanterade identiteten som tilldelats ditt HDInsight-kluster finns i rollen **Storage BLOB data Contributor** eller i **rollen Storage BLOB data-ägare**. Läs mer i [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) för fullständiga installations anvisningar.

Om du använder Data Lake Storage gen 1, se installations-och konfigurations anvisningar [här](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage gen 1 stöds inte för HBase-kluster och stöds inte i HDInsight version 4,0.

Om du använder Azure Storage, se till att lagrings konto namnet är giltigt när klustret skapas.

## <a name="resource-policy-restrictions"></a>Begränsningar för resurs principer

Med prenumerations Azure-principer kan du inte skapa offentliga IP-adresser. Skapande av HDInsight-klustret kräver två offentliga IP-adresser.  

I allmänhet kan följande principer påverka skapandet av kluster:

* Principer förhindrar skapande av IP-adress & belastningsutjämnare i prenumerationen.
* Princip förhindrar skapande av lagrings konto.
* Princip som förhindrar borttagning av nätverks resurser (IP-/load-saldon).

## <a name="firewalls"></a>Brandväggar

Brand väggar i ditt virtuella nätverk eller lagrings konto kan neka kommunikation med hanterings-IP-adresser för HDInsight.

Tillåt trafik från IP-adresserna i tabellen nedan.

| IP-källadress | Mål | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Inkommande |
| 23.99.5.239 | *: 443 | Inkommande |
| 168.61.48.131 | *: 443 | Inkommande |
| 138.91.141.162 | *: 443 | Inkommande |

Lägg också till de IP-adresser som är speciella för den region där klustret skapas. Se [hanterings-IP-adresser för HDInsight](../hdinsight-management-ip-addresses.md) för en lista över adresser för varje Azure-region.

Om du använder en Express-väg eller din egen anpassade DNS-server kan du läsa [planera ett virtuellt nätverk för Azure HDInsight – ansluta flera nätverk](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Resurs lås  

Se till att det inte finns några [Lås på det virtuella nätverket och resurs gruppen](../../azure-resource-manager/resource-group-lock-resources.md).  

## <a name="unsupported-component-versions"></a>Komponent versioner som inte stöds

Se till att du använder en [version av Azure HDInsight](../hdinsight-component-versioning.md) och Apache Hadoop- [komponenter](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) som stöds i din lösning.  

## <a name="storage-account-name-restrictions"></a>Begränsningar för lagrings konto namn

Lagrings konto namn får inte vara längre än 24 tecken och får inte innehålla specialtecken. Dessa begränsningar gäller även för standardnamnet för containrar i lagringskontot.

## <a name="service-outages"></a>Drift avbrott för tjänsten

Kontrol lera [Azure-status](https://status.azure.com/status) för eventuella problem med avbrott och tjänster.

## <a name="next-steps"></a>Nästa steg

* [Utöka Azure HDInsight med hjälp av ett virtuellt Azure-nätverk](../hdinsight-plan-virtual-network-deployment.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Använda Azure-lagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md)
* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](../hdinsight-hadoop-provision-linux-clusters.md)
