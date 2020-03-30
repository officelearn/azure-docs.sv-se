---
title: Hantera Azure HDInsight-kluster med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera Azure HDInsight-kluster. Klustertyper inkluderar Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query och ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272778"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Hantera Azure HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du använder [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att hantera Azure HDInsight-kluster. Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Azure CLI. Om du inte har installerat Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för steg.

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Anslut till Azure

Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lista kluster

Använd [az hdinsight-listan](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) för att lista kluster. Redigera kommandona nedan `RESOURCE_GROUP_NAME` genom att ersätta med namnet på resursgruppen och ange sedan kommandona:

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

Använd [az hdinsight visa](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) för att visa information för ett angivet kluster. Redigera kommandot nedan `RESOURCE_GROUP_NAME`genom `CLUSTER_NAME` att ersätta och med relevant information och ange sedan kommandot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd [az hdinsight-borttagning](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) för att ta bort ett angivet kluster. Redigera kommandot nedan `RESOURCE_GROUP_NAME`genom `CLUSTER_NAME` att ersätta och med relevant information och ange sedan kommandot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Observera att detta tar bort alla resurser i gruppen, inklusive standardlagringskontot.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skala kluster

Använd [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) för att ändra storlek på det angivna HDInsight-klustret till den angivna storleken. Redigera kommandot nedan `RESOURCE_GROUP_NAME`genom `CLUSTER_NAME` att ersätta och med relevant information. Ersätt `WORKERNODE_COUNT` med önskat antal arbetsnoder för klustret. Mer information om skalning av kluster finns i [Skala HDInsight-kluster](./hdinsight-scaling-best-practices.md). Ange kommandot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utför olika administrativa HDInsight-klusteruppgifter. Mer information finns i följande artiklar:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
