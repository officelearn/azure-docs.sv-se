---
title: "Felsöka din lokala konfiguration av Service Fabric | Microsoft Docs"
description: "Den här artikeln innehåller en uppsättning förslag för att felsöka lokal utveckling-kluster"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Felsöka din konfiguration av lokal utveckling
Om du stöter på ett problem vid interaktion med din lokala Azure Service Fabric-klustret för utveckling, granska följande rekommendationer för möjliga lösningar.

## <a name="cluster-setup-failures"></a>Kluster-installationsfel
### <a name="cannot-clean-up-service-fabric-logs"></a>Det går inte att rensa Service Fabric-loggar
#### <a name="problem"></a>Problem
När du kör skriptet DevClusterSetup, visas ett fel så här:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Lösning
Stäng det nuvarande PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Du bör nu kunna köra skriptet.

## <a name="cluster-connection-failures"></a>Kluster-anslutningsfel
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Service Fabric PowerShell cmdlets känns inte igen av Azure PowerShell
#### <a name="problem"></a>Problem
Om du försöker köra Service Fabric PowerShell-cmdlets som `Connect-ServiceFabricCluster` i ett Azure PowerShell-fönster misslyckas, säger att cmdleten inte känns igen. Anledningen är att Azure PowerShell använder 32-bitars version av Windows PowerShell (även på 64-bitars operativsystemversioner), medan Service Fabric-cmdlets fungerar endast i 64-bitars miljöer.

#### <a name="solution"></a>Lösning
Kör alltid Service Fabric-cmdlet: ar direkt från Windows PowerShell.

> [!NOTE]
> Den senaste versionen av Azure PowerShell kan inte skapa en särskild genväg så att det inte längre ska ske.
> 
> 

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
Stäng det nuvarande PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Du ska nu kunna ansluta.

### <a name="fabric-connection-denied-exception"></a>Fabric-anslutningen nekades undantag
#### <a name="problem"></a>Problem
När du felsöker från Visual Studio kan du få ett FabricConnectionDeniedException fel.

#### <a name="solution"></a>Lösning
Det här felet uppstår vanligen när du försöker starta en serverprocess manuellt i stället för att tillåta Service Fabric runtime om du.

Se till att du inte har några serviceprojekt som Startprojekt i din lösning. Endast Service Fabric-programprojekt ska anges som Startprojekt.

> [!TIP]
> Om efter installationen, lokala klustret börjar fungera onormalt, kan du återställa den med hjälp av det lokala klustret manager fack systemprogrammet. Detta tar bort befintliga klustret och skapa en ny. Observera att alla distribuerade program och associerade data tas bort.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Förstå och felsöka klustret med systemet hälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

