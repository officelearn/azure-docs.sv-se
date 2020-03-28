---
title: Ta bort program från ett kluster i Powershell
description: Exempel på Azure PowerShell-skript – Ta bort ett program från ett Service Fabric-kluster.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610292"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Ta bort ett program från ett Service Fabric-kluster med Powershell

Det här exempelskriptet tar bort en programinstans som körs i Tjänst fabric och avregistrerar en programtyp och version från klustret.  Om du tar bort en programinstans raderas även de tjänstinstanser som körs som associeras med det programmet. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ta bort ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Tar bort en programinstans som körs i Service Fabric från klustret.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Avregistrerar en tjänst fabric-programtyp och version från klustret. |

## <a name="next-steps"></a>Nästa steg

Mer information om PowerShell-modulen Service Fabric finns i [Azure PowerShell-dokumentation](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
