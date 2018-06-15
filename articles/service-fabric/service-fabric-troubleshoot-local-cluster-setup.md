---
title: Felsöka din lokala konfiguration av Azure Service Fabric | Microsoft Docs
description: Den här artikeln innehåller en uppsättning förslag för att felsöka lokal utveckling-kluster
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a7f58914fd6e498e717e19bfea11c9e3fcfc0399
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212025"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Felsöka din konfiguration av lokal utveckling
Om du stöter på ett problem vid interaktion med din lokala Azure Service Fabric-klustret för utveckling, granska följande rekommendationer för möjliga lösningar.

## <a name="cluster-setup-failures"></a>Kluster-installationsfel
### <a name="cannot-clean-up-service-fabric-logs"></a>Det går inte att rensa Service Fabric-loggar
#### <a name="problem"></a>Problem
När du kör skriptet DevClusterSetup, se följande fel:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Lösning
Stäng det nuvarande PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Du kan nu köra skriptet.

## <a name="cluster-connection-failures"></a>Kluster-anslutningsfel

### <a name="type-initialization-exception"></a>Undantag för initiering av typen
#### <a name="problem"></a>Problem
När du ansluter till klustret i PowerShell kan se du felet TypeInitializationException för System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Lösning
Path-variabeln har inte angetts på rätt sätt under installationen. Logga ut från Windows och logga in igen. Detta uppdaterar din sökväg.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Klustret anslutningen misslyckas med ”-objekt stängs”
#### <a name="problem"></a>Problem
Ett anrop till Connect-ServiceFabricCluster misslyckas med felmeddelandet Så här:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Lösning
Stäng det nuvarande PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör.

### <a name="fabric-connection-denied-exception"></a>Fabric-anslutningen nekades undantag
#### <a name="problem"></a>Problem
När du felsöker från Visual Studio kan du få ett FabricConnectionDeniedException fel.

#### <a name="solution"></a>Lösning
Det här felet uppstår vanligen när du försöker starta en värdprocess för tjänsten manuellt.

Se till att du inte har några serviceprojekt som Startprojekt i din lösning. Endast Service Fabric-programprojekt ska anges som Startprojekt.

> [!TIP]
> Om efter installationen, lokala klustret börjar fungera onormalt, kan du återställa den med hjälp av det lokala klustret manager fack systemprogrammet. Detta tar bort befintliga klustret och skapa en ny. Observera att alla distribuerade program och associerade data tas bort.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Förstå och felsöka klustret med systemet hälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

