---
title: Hantera Azure HDInsight-kluster med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera Azure HDInsight-kluster. Klustertyper som omfattar Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query och ML-tjänster.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 5ae97b17d06fa0a9934a58ac662ef12116cce4f6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137405"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Hantera Azure HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du använder [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) att hantera Azure HDInsight-kluster. Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure CLI. Om du inte har installerat Azure CLI, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) anvisningar.

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Anslut till Azure

Logga in på Azure-prenumerationen. Om du planerar att använda Azure Cloud Shell och sedan markerar du **prova** i det övre högra hörnet i kodblocket. Annars anger du kommandot nedan:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>En lista över kluster

Använd [az hdinsight lista](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) till en lista över kluster. Redigera kommandona nedan genom att ersätta `RESOURCE_GROUP_NAME` med namnet på resursgruppen, sedan ange kommandon:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Visa kluster

Använd [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) för att visa information för ett kluster som anges. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME`, och `CLUSTER_NAME` med relevant information, ange sedan kommando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd [az hdinsight bort](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) att ta bort ett kluster som anges. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME`, och `CLUSTER_NAME` med relevant information, ange sedan kommando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Observera att detta tar bort alla resurser i gruppen, inklusive standardkontot för lagring.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skala kluster

Använd [az hdinsight ändra storlek på](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) att ändra storlek på det angivna HDInsight-klustret till den angivna storleken. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME`, och `CLUSTER_NAME` med relevant information. Ersätt `TARGET_INSTANCE_COUNT` med antalet arbetsnoder för klustret. Läs mer om att skala kluster [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md). Ange kommandot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utför olika HDInsight-kluster administrativa uppgifter. Mer information finns i följande artiklar:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
