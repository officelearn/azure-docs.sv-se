---
title: Azure PowerShell skript exempel – ta bort program från ett kluster | Microsoft Docs
description: Azure PowerShell skript exempel – ta bort ett program från ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: f572996b4e460480335dbe32c4599bde206251d4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035506"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Ta bort ett program från ett Service Fabric-kluster

Det här exempel skriptet tar bort en Service Fabric program instans som körs och avregistrerar en program typ och version från klustret.  Om du tar bort en programinstans raderas även de tjänstinstanser som körs som associeras med det programmet. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Tar bort en Service Fabric program instans som körs från klustret.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Avregistrerar en Service Fabric program typ och version från klustret. |

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric PowerShell-modulen finns i [Azure PowerShell dokumentation](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
