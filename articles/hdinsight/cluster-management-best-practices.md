---
title: Metodtips för klusterhantering – Azure HDInsight
description: Lär dig metodtips för hantering av HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782026"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Metodtips för HDInsight-klusterhantering

Lär dig metodtips för hantering av HDInsight-kluster.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hur skapar jag HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Azure Data Factory | [Skapa Apache Hadoop-kluster på begäran i HDInsight med hjälp av Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Mall för Anpassad Resurshanteraren | [Skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Snabbstartsmallar | [Snabbstartsmallar för HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-exempel | [HdInsight Azure-exempel](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Skapa Linux-baserade kluster i HDInsight med hjälp av Azure-portalen](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Skapa HDInsight-kluster med Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Skapa Linux-baserade kluster i HDInsight med Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Skapa Apache Hadoop-kluster med Azure REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Om du skapar ett kluster och återanvänder klusternamnet från ett tidigare skapat kluster väntar du tills den tidigare klusterborttagningen är klar innan du skapar klustret.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hur anpassar jag HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Skriptåtgärder | [Anpassa Azure HDInsight-kluster med hjälp av skriptåtgärder](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Anpassa HDInsight-kluster med Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externa metabutiker | [Använda extern metadatalagring i Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Anpassad Ambari-databas | [Konfigurera HDInsight-kluster med en anpassad Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Vilka är några fel som jag kan ställas inför när jag skapar kluster?

| Fel | Mer information |
|---|---|
| Ingen kvot | Det finns kvoter för antalet kvoter som du kan skapa på din prenumeration i varje region. Mer information finns i [Kapacitetsplanering: kvoter](./hdinsight-capacity-planning.md). |
| Inga fler IP-adresser tillgängliga | Varje virtuella nätverk har ett begränsat antal IP-adresser. När du skapar ett HDInsight-kluster använder varje nod (inklusive zookeeper- och gatewaynoder) några av dessa tilldelade IP-adresser. När alla IP-adresser används uppstår det här felet.  |
| NSG-regler (Network Security Group) tillåter inte kommunikation med HDInsight-resursleverantörer | Om du använder NSGs eller användardefinierade vägar (UDRs) för att styra inkommande trafik till ditt HDInsight-kluster, måste du se till att klustret kan kommunicera med kritiska Azure-hälso- och hanteringstjänster. Mer information finns i [NSG-tjänsttaggar (Network Security Group) för Azure HDInsight](./hdinsight-service-tags.md) |
| Återanvändning av klusternamn | När du använder ett klusternamn som du har använt tidigare måste du vänta X antal minuter innan du återskapar klustret. Annars visas ett meddelande om att resursen redan finns. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hur hanterar jag att köra HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Automatisk skalning | [Skala Azure HDInsight-kluster automatiskt](./hdinsight-autoscale-clusters.md) |
| Manuell skalning | [Skala Azure HDInsight-kluster](./hdinsight-scaling-best-practices.md) |
| Övervakning med Ambari| [Övervaka klusterprestanda i Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Övervakning med Azure Monitor-loggar | [Använda Azure Monitor-loggar för att övervaka HDInsight-kluster](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hur kontrollerar jag borttagna HDInsight-kluster?

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Du kan använda följande fråga med Azure Monitor-loggar för att övervaka borttagna kluster.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Nästa steg

* [Kapacitetsplanering för HDInsight-kluster](./hdinsight-capacity-planning.md)
* [Vilka är standard- och rekommenderade nodkonfigurationer för Azure HDInsight?](./hdinsight-supported-node-configuration.md)