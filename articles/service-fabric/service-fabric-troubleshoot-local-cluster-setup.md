---
title: Felsöka din lokala konfiguration av Azure Service Fabric | Microsoft Docs
description: Den här artikeln innehåller en uppsättning förslag för felsökning av det lokala utvecklingsklustret
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662813"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Felsöka din konfiguration av lokal utveckling
Om du stöter på ett problem vid interaktion med din lokala Azure Service Fabric-kluster för utveckling, kan du läsa följande rekommendationer för möjliga lösningar.

## <a name="cluster-setup-failures"></a>Klusterinstallationsfel
### <a name="cannot-clean-up-service-fabric-logs"></a>Det går inte att rensa upp Service Fabric-loggar
#### <a name="problem"></a>Problem
När du kör skriptet DevClusterSetup, kan du se följande fel:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Du kan nu köra skriptet.

## <a name="cluster-connection-failures"></a>Kluster-anslutningsfel

### <a name="type-initialization-exception"></a>Initieringen av typundantag
#### <a name="problem"></a>Problem
När du ansluter till klustret i PowerShell kan se du felet TypeInitializationException för System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Lösning
Path-variabeln har inte angetts på rätt sätt under installationen. Logga ut från Windows och logga in igen. Detta uppdaterar din sökväg.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Klusteranslutning misslyckas med ”-objekt stängs”
#### <a name="problem"></a>Problem
Ett anrop till Connect-ServiceFabricCluster misslyckas med felmeddelandet Så här:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör.

### <a name="fabric-connection-denied-exception"></a>Fabric anslutning nekas undantag
#### <a name="problem"></a>Problem
När du felsöker från Visual Studio, får du felet FabricConnectionDeniedException.

#### <a name="solution"></a>Lösning
Det här felet uppstår vanligen när du försöker starta en värdprocess för tjänsten manuellt.

Kontrollera att du inte har några serviceprojekt ange som Startprojekt i din lösning. Endast Service Fabric-programprojekt ska anges som Startprojekt.

> [!TIP]
> Om efter installationen, ditt lokala kluster börjar beter sig onormalt, kan du återställa den med hjälp av programmet local cluster manager fack. Detta tar bort befintligt kluster och konfigurera en ny. Observera att alla distribuerade program och associerade data tas bort.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Förstå och felsöka ditt kluster med systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

