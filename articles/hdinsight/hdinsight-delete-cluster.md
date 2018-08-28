---
title: Hur du tar bort ett HDInsight-kluster – Azure
description: Information om de olika sätten att du tar bort ett HDInsight-kluster.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 737cc120877a9d0f06a1f6d209bcf9a233aa7d19
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091317"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du tar bort ett kluster med Azure-portalen, Azure PowerShell och Azure CLI 1.0 i det här dokumentet.

> [!IMPORTANT]
> Tar bort ett HDInsight-kluster tas inte bort Azure Storage-konton eller Data Lake Store som är associerade med klustret. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj ditt HDInsight-kluster. Om inte ditt HDInsight-kluster är fäst på instrumentpanelen, kan du söka efter den efter namn med sökfältet.
   
    ![Portal-sökning](./media/hdinsight-delete-cluster/navbar.png)

2. Inställningar för klustret, väljer du den **ta bort** ikon. När du uppmanas, väljer **Ja** ta bort klustret.
   
    ![ikonen Ta bort](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Använder du följande kommando från PowerShell-prompten, ta bort klustret:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

## <a name="azure-cli-10"></a>Azure CLI 1.0

Använd följande från en kommandotolk för att ta bort klustret:

    azure hdinsight cluster delete CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

> [!NOTE]
> Azure CLI 2.0 stöder inte ta bort HDInsight-kluster just nu (23 oktober 2017).
