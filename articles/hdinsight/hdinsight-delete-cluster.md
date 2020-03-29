---
title: Ta bort ett HDInsight-kluster – Azure
description: Information om olika sätt att ta bort ett Azure HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807144"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen är proportionellt per minut, så du bör alltid ta bort klustret när det inte längre används. I det här dokumentet får du lära dig hur du tar bort ett kluster med [Azure-portalen,](https://portal.azure.com) [Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/overview)och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Om du tar bort ett HDInsight-kluster tas inte Azure Storage-konton eller Data Lake Storage som är associerade med klustret bort. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På den vänstra menyn navigerar du till **Alla tjänster** > **Analytics** > **HDInsight-kluster** och väljer ditt kluster.

3. Välj ikonen **Ta bort** i standardvyn. Följ uppmaningen om att ta bort klustret.

    ![Knappen Ta bort kluster i HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Ersätt `CLUSTERNAME` med namnet på ditt HDInsight-kluster i koden nedan. Från en PowerShell-prompt anger du följande kommando för att ta bort klustret:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Ersätt `CLUSTERNAME` med namnet på DITT HDInsight-kluster och `RESOURCEGROUP` med namnet på resursgruppen i koden nedan.  Från en kommandotolk anger du följande för att ta bort klustret:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
