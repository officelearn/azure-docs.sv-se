---
title: Exempel på Azure PowerShell-skript – Skapa ett Service Fabric-kluster | Microsoft Docs
description: Exempel på Azure PowerShell-skript – Skapa ett Service Fabric-testkluster med tre noder.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a03362ebd4b8502f12b7c7bb9aadc558f6a073d2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Skapa ett Service Fabric-testkluster med tre noder

Det här exempelskriptet skapar ett Service Fabric-testkluster med tre noder som skyddas med ett X.509-certifikat. Klusterkonfigurationen med tre noder stöds för utveckling/testning eftersom du på ett säkert sätt kan utföra uppgraderingar och rädda enskilda nodfel (så länge de inte inträffar samtidigt). Produktionskluster kräver fem eller fler noder för att vara motståndskraftiga mot samtidiga fel.  

Kommandot skapar ett självsignerat certifikat och överför det till ett nytt nyckelvalv, som skapas i samma resursgrupp som klustret. Certifikatet kopieras även till en lokal katalog.  Ange parametern *-OS* för att välja den version av Windows eller Linux som körs på klusternoderna.  Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Login-AzureRmAccount` för att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, klustret och alla relaterade resurser.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Skapar ett nytt Service Fabric-kluster. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
