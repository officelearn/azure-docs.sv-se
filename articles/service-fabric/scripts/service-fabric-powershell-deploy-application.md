---
title: Distribuera program till ett kluster i PowerShell
description: Azure PowerShell skript exempel – distribuera ett program till ett Service Fabric-kluster.
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
ms.openlocfilehash: 145372fa872c481ec1a7c3de016c35fdc0f9d960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083811"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuera ett program till ett Service Fabric-kluster

Det här exempel skriptet kopierar ett programpaket till ett kluster avbildnings lager, registrerar program typen i klustret, tar bort det onödiga programpaketet och skapar en program instans från program typen.  Om några standard tjänster definierades i program manifestet för mål program typen, skapas dessa tjänster just nu. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skript exemplet har körts kan skriptet i [ta bort ett program](service-fabric-powershell-remove-application.md) användas för att ta bort program instansen, avregistrera program typen och ta bort programpaketet från avbildnings arkivet.

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
|[Anslut – ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Skapar en anslutning till ett Service Fabric-kluster. |
|[Kopiera – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopierar ett programpaket till klustrets avbildnings arkiv.  |
|[Registrera – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registrerar en program typ och en version av klustret. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Skapar ett program från en registrerad program typ. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Tar bort ett Service Fabric-programpaket från avbildnings arkivet.|

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric PowerShell-modulen finns i [Azure PowerShell dokumentation](/powershell/azure/service-fabric/overview?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
