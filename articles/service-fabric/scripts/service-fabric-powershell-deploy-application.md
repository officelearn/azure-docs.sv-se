---
title: Skriptexempel för Azure PowerShell – distribuera program till ett kluster | Microsoft Docs
description: Skriptexempel för Azure PowerShell – distribuera ett program till Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: e205315530b0dc89037c1253c571c72c55f00a67
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661436"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuera ett program till ett Service Fabric-kluster

Det här exempelskriptet kopierar ett programpaket till ett kluster avbildningsarkivet, registrerar vilken typ av program i klustret, tar du bort onödiga programpaketet och skapar en programinstans från vilken typ av program.  Om alla standardtjänster har definierats i applikationsmanifestet av mål-programtyp, skapas de tjänsterna som just nu. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När exempelskriptet har körts, skriptet [ta bort ett program](service-fabric-powershell-remove-application.md) kan användas för att ta bort programinstansen, avregistrera programtypen och ta bort programpaketet från avbildningsarkivet.

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Skapar en anslutning till ett Service Fabric-kluster. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopior lagra för ett programpaket till kluster-avbildning.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registrerar en programtypen och versionen på klustret. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Skapar ett program från en registrerad programtypen. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Tar bort ett Service Fabric-programpaket från avbildningsarkivet.|

## <a name="next-steps"></a>Nästa steg

Läs mer om Service Fabric PowerShell-modulen [Azure PowerShell-dokumentationen](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
