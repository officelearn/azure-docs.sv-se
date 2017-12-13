---
title: "Azure PowerShell-skript exempel – skapa ett Service Fabric-kluster | Microsoft Docs"
description: Azure PowerShell-skript Sample - skapa en tre nodtest Service Fabric-klustret.
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/12/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 03348efa4ebdaed987df73756c6b57da0cc76fb5
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Skapa ett tre nodtest Service Fabric-kluster

Det här exempelskriptet skapar en tre nodtest Service Fabric-kluster som skyddas med ett X.509-certifikat. Tre noder klusterkonfigurationen stöds för utveckling och testning eftersom du kan utföra uppgraderingar och överleva fel på enskilda noder (förutsatt att de inte råkar samtidigt) på ett säkert sätt. Produktionskluster kräver fem eller fler noder för att bli motståndskraftiga mot samtidiga fel.  

Kommandot skapar ett självsignerat certifikat och överför det till ett nytt nyckelvalv som skapas i samma resursgrupp som klustret. Certifikatet kopieras även till en lokal katalog.  Ange parametern *-OS* för att välja den version av Windows eller Linux som körs på klusternoderna.  Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview) och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Följande kommando kan användas för att ta bort resursgruppen, kluster och alla relaterade resurser efter skriptexempel har körts.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Skapar ett nytt Service Fabric-kluster. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
