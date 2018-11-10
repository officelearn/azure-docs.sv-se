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
ms.openlocfilehash: dff9420eb0f91652cc134a37d1b248e2e5b2b681
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004543"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller den klassiska Azure-CLI

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du tar bort ett kluster med Azure-portalen, Azure PowerShell och den klassiska Azure-CLI i det här dokumentet.

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

## <a name="azure-classic-cli"></a>Klassisk Azure CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Använd följande från en kommandotolk för att ta bort klustret:

    azure hdinsight cluster delete CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.
