---
title: Så här tar du bort ett HDInsight-kluster – Azure
description: Information om de olika sätt som du kan ta bort ett Azure HDInsight-kluster på
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 3903500e87dd162779d1732a4cfe405ba34e702c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074740"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen beräknas proportionellt per minut, så du bör alltid ta bort ditt kluster när det inte längre används. I det här dokumentet får du lära dig hur du tar bort ett kluster med hjälp av [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ-modulen](https://docs.microsoft.com/powershell/azure/)och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Om du tar bort ett HDInsight-kluster tas inte Azure Storage-konton eller Data Lake Storage som är associerade med klustret bort. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Gå till den vänstra menyn och navigera till **alla tjänster**  >  **Analytics**  >  **HDInsight-kluster** och välj ditt kluster.

3. Välj ikonen **ta bort** i standardvyn. Följ anvisningarna för att ta bort klustret.

    ![Knappen Ta bort kluster för HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Ersätt `CLUSTERNAME` med namnet på ditt HDInsight-kluster i koden nedan. Från en PowerShell-kommandotolk anger du följande kommando för att ta bort klustret:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Ersätt `CLUSTERNAME` med namnet på ditt HDInsight-kluster och `RESOURCEGROUP` med namnet på din resurs grupp i koden nedan.  I en kommando tolk anger du följande för att ta bort klustret:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
