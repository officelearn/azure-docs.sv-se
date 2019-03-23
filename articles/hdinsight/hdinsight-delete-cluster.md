---
title: Hur du tar bort ett HDInsight-kluster – Azure
description: Information om de olika sätten att du tar bort ett HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 4df4fa29722dd3ad33cf1ce123877f04f9f4b4c1
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360396"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller den klassiska Azure-CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. I det här dokumentet har du lära dig hur du tar bort ett kluster som använder den [Azure-portalen](https://portal.azure.com), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/), och den klassiska Azure-CLI.

> [!IMPORTANT]  
> Tar bort ett HDInsight-kluster tas inte bort Azure Storage-konton eller Data Lake Storage som är associerade med klustret. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj ditt HDInsight-kluster. Om inte ditt HDInsight-kluster är fäst på instrumentpanelen, kan du söka efter den efter namn med sökfältet.
   
    ![Portal-sökning](./media/hdinsight-delete-cluster/navbar.png)

2. Inställningar för klustret, väljer du den **ta bort** ikon. När du uppmanas, väljer **Ja** ta bort klustret.
   
    ![ikonen Ta bort](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använder du följande kommando från PowerShell-prompten, ta bort klustret:

    Remove-AzHDInsightCluster -ClusterName CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

## <a name="azure-classic-cli"></a>Klassisk Azure CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Använd följande från en kommandotolk för att ta bort klustret:

    azure hdinsight cluster delete CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.
