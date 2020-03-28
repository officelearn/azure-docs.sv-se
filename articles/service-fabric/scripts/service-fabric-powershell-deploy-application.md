---
title: Distribuera program till ett kluster i Powershell
description: Exempel på Azure PowerShell-skript – distribuera ett program till ett Service Fabric-kluster.
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
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610309"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuera ett program till ett Service Fabric-kluster

Det här exempelskriptet kopierar ett programpaket till ett klusteravbildningsarkiv, registrerar programtypen i klustret, tar bort det onödiga programpaketet och skapar en programinstans från programtypen.  Om några standardtjänster har definierats i programmanifestet för målprogramtypen skapas dessa tjänster just nu. Anpassa parametrarna efter behov. 

Installera, om så behövs, Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptexemplet har körts kan skriptet i [Ta bort ett program](service-fabric-powershell-remove-application.md) användas för att ta bort programinstansen, avregistrera programtypen och ta bort programpaketet från bildarkivet.

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Skapar en anslutning till ett Service Fabric-kluster. |
|[Kopia-serviceFabricApplicationPaket](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Kopierar ett programpaket till klusteravbildningsarkivet.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registrerar en programtyp och version i klustret. |
|[Nya-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Skapar ett program från en registrerad programtyp. |
| [Ta bort ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Tar bort ett Programpaket för Service Fabric från bildarkivet.|

## <a name="next-steps"></a>Nästa steg

Mer information om PowerShell-modulen Service Fabric finns i [Azure PowerShell-dokumentation](/powershell/azure/service-fabric/?view=azureservicefabricps).

Ytterligare Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
