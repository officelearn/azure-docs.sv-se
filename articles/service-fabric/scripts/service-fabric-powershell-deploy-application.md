---
title: "Azure PowerShell-skript exempel – distribuera program till ett kluster | Microsoft Docs"
description: "Azure PowerShell-skript exempel – distribuerar ett program till ett Service Fabric-kluster."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 159b856606885c4ee206ba42ec72a8bd1ec5b0f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuera ett program till ett Service Fabric-kluster

Det här exempelskriptet kopierar ett programpaket till ett kluster avbildningsarkivet, registrerar programtypen i klustret, tar bort onödiga programpaketet och skapar en instans av programmet från programtypen.  Om alla standardtjänster definierades i programmanifestet av programmet måltypen skapas tjänsterna just nu. Anpassa parametrarna efter behov. 

Om det behövs installerar du Service Fabric PowerShell-modulen med den [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Efter skriptexempel har körts, skriptet [tar bort ett program](service-fabric-powershell-remove-application.md) kan användas för att ta bort programinstansen, avregistrera programtypen och ta bort programmet paketet från avbildningsarkivet.

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Skapar en anslutning till ett Service Fabric-kluster. |
|[Kopiera ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopierar arkivet för ett programpaket till kluster-avbildningen.  |
|[Registrera ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registrerar ett programtypen och versionen på klustret. |
|[Ny ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Skapar ett program från en typ som registrerade programmet. |
| [Ta bort ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Tar bort ett Service Fabric-programpaket från avbildningsarkivet.|

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric kan hittas i den [Azure PowerShell-exempel](../service-fabric-powershell-samples.md).
