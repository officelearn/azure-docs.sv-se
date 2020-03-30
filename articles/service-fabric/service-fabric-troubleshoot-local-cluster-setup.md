---
title: Felsöka den lokala azure service fabric-klusterkonfigurationen
description: Den här artikeln innehåller en uppsättning förslag på felsökning av ditt lokala utvecklingskluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465502"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Felsöka konfigurationen av ditt lokala utvecklingskluster
Om du stöter på ett problem när du interagerar med ditt lokala Azure Service Fabric-utvecklingskluster läser du följande förslag på potentiella lösningar.

## <a name="cluster-setup-failures"></a>Fel i klusterkonfigurationen
### <a name="cannot-clean-up-service-fabric-logs"></a>Det går inte att rensa service fabric-loggar
#### <a name="problem"></a>Problem
När du kör DevClusterSetup-skriptet visas följande fel:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Du kan nu köra skriptet.

## <a name="cluster-connection-failures"></a>Klusteranslutningsfel

### <a name="type-initialization-exception"></a>Typ initiering undantag
#### <a name="problem"></a>Problem
När du ansluter till klustret i PowerShell visas felet TypeInitializationException för System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Lösning
Sökvägens variabel har inte angetts korrekt under installationen. Logga ut från Windows och logga in igen. Detta uppdaterar din väg.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Klusteranslutningen misslyckas med "Objektet är stängt"
#### <a name="problem"></a>Problem
Ett anrop till Connect-ServiceFabricCluster misslyckas med ett sådant här fel:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör.

### <a name="fabric-connection-denied-exception"></a>Undantag för nekad infrastrukturanslutning
#### <a name="problem"></a>Problem
När du felsöker från Visual Studio får du ett FabricConnectionDeniedException-fel.

#### <a name="solution"></a>Lösning
Det här felet uppstår vanligtvis när du försöker starta en tjänstvärdprocess manuellt.

Se till att du inte har några serviceprojekt som är inställda som startprojekt i din lösning. Endast Service Fabric-programprojekt bör anges som startprojekt.

> [!TIP]
> Om ditt lokala kluster börjar bete sig onormalt efter installationen kan du återställa det med hjälp av det lokala systemfältet för klusterhanteraren. Detta tar bort det befintliga klustret och konfigurerar ett nytt. Observera att alla distribuerade program och associerade data tas bort.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Förstå och felsöka klustret med systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

