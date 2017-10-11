---
title: Hur du tar bort ett HDInsight-kluster - Azure | Microsoft Docs
description: "Information om de olika sätt att du tar bort ett HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 65dac529df15d2dd43eec17673d82a2832f7692e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Ta bort ett HDInsight-kluster med hjälp av din webbläsare, PowerShell eller Azure CLI

HDInsight-kluster faktureringen påbörjas när ett kluster skapas och slutar när klustret har tagits bort. Fakturering är proportionerligt per minut, så du bör alltid ta bort klustret när den inte längre används. Lär dig hur du tar bort ett kluster med Azure-portalen, Azure PowerShell och Azure CLI 1.0 i det här dokumentet.

> [!IMPORTANT]
> Tar bort ett HDInsight-kluster tas inte bort Azure Storage-konton eller Datasjölager som är associerade med klustret. Du kan återanvända data som lagras i dessa tjänster i framtiden.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj ditt HDInsight-kluster. Om ditt HDInsight-kluster inte är fäst på instrumentpanelen, kan du söka efter den av namn med hjälp av sökfältet.
   
    ![Portal-sökning](./media/hdinsight-delete-cluster/navbar.png)

2. När det öppnas bladet för klustret, väljer du den **ta bort** ikon. När du uppmanas, Välj **Ja** att ta bort klustret.
   
    ![Ikonen Ta bort](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Använder du följande kommando för att ta bort klustret från en PowerShell-kommandotolk:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

## <a name="azure-cli-10"></a>Azure CLI 1.0

Använd följande från en kommandotolk för att ta bort klustret:

    azure hdinsight cluster delete CLUSTERNAME

Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

> [!NOTE]
> Azure CLI 2.0 stöder inte ta bort HDInsight-kluster just nu (31 juli 2017).