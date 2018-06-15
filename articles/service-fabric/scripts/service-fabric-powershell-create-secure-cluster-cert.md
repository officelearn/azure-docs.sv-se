---
title: Exempel på Azure PowerShell-skript – Skapa ett Service Fabric-kluster | Microsoft Docs
description: Exempel på Azure PowerShell-skript – Skapa ett Service Fabric-kluster.
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
ms.date: 01/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ad3c51f0f43d63fd784156eca680218850897e8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596559"
---
# <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Det här exempelskriptet skapar ett Service Fabric-kluster med fem noder som skyddas med ett X.509-certifikat.  Kommandot skapar ett självsignerat certifikat och laddar upp det till ett nytt nyckelvalv. Certifikatet kopieras även till en lokal katalog.  Ange parametern *-OS* för att välja den version av Windows eller Linux som körs på klusternoderna.  Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

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
