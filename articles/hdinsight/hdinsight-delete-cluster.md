---
title: Hur du tar bort ett HDInsight-kluster – Azure
description: Information om de olika sätten att du tar bort ett HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097208"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. I det här dokumentet har du lära dig hur du tar bort ett kluster som använder den [Azure-portalen](https://portal.azure.com), [Az för Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/overview), och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Tar bort ett HDInsight-kluster tas inte bort Azure Storage-konton eller Data Lake Storage som är associerade med klustret. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra menyn navigerar du till **alla tjänster** > **Analytics** > **HDInsight-kluster** och välj ditt kluster.

3. Standardvyn, väljer du den **ta bort** ikon. Följ anvisningarna för att ta bort ditt kluster.
   
    ![ikonen Ta bort](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az-modulen

Ersätt `CLUSTERNAME` med namnet på ditt HDInsight-kluster i koden nedan. Ange följande kommando för att ta bort klustret från en PowerShell-kommandotolk:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Ersätt `CLUSTERNAME` med namnet på ditt HDInsight-kluster och `RESOURCEGROUP` med namnet på resursgruppen i koden nedan.  Ange följande för att ta bort klustret från en kommandotolk:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
