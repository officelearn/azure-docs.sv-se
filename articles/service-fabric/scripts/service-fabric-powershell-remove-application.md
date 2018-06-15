---
title: Azure PowerShell skriptexempel - ta bort programmet från ett kluster | Microsoft Docs
description: Azure PowerShell skriptexempel - ta bort ett program från ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ea7037fc3655298fc4c03b8d9f988a55e42c9fe9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927870"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Ta bort ett program från ett Service Fabric-kluster

Det här exempelskriptet tar bort en instans som körs Service Fabric-program och Avregistrerar en programtypen och versionen från klustret.  Tar bort programinstansen också att tas bort alla körs tjänsten instanser som är associerade med programmet. Anpassa parametrarna efter behov. 

Om det behövs installerar du Service Fabric PowerShell-modulen med den [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Tar bort en instans som körs Service Fabric-program från klustret.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Avregistrerar en Service Fabric-programtypen och versionen från klustret. |

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
