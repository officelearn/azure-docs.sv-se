---
title: Azure Service Fabric-fristående paket för Windows Server | Microsoft Docs
description: Beskrivning och innehållet i Azure Service Fabric fristående paketet för Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik;aljo
ms.openlocfilehash: 948b1bb2ce5a19007906262a4efb0ecca8e40482
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Innehållet i Service Fabric fristående paketet för Windows Server
I den [hämtas](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric fristående paketet, hittar du följande filer:

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Ett PowerShell-skript som skapar klustret med inställningarna i ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Ett PowerShell-skript som tar bort ett kluster med inställningarna i ClusterConfig.json. |
| AddNode.ps1 |Ett PowerShell-skript för att lägga till en nod i ett befintligt distribuerat kluster på den aktuella datorn. |
| RemoveNode.ps1 |Ett PowerShell-skript för att ta bort en nod från ett befintligt distribuerat kluster från den aktuella datorn. |
| CleanFabric.ps1 |Ett PowerShell-skript för att rensa en fristående Service Fabric-installation av den aktuella datorn. Tidigare MSI-installationer bör tas bort med hjälp av sina egna associerade uninstallers. |
| TestConfiguration.ps1 |Ett PowerShell-skript för att analysera infrastrukturen som anges i Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Ett PowerShell-skript som används för att ladda ned det senaste runtime-paketet out-of-band för scenarier där den distribuera datorn inte är ansluten till internet. |
| DeploymentComponentsAutoextractor.exe |Självextraherande arkiv som innehåller distributionskomponenter som används av fristående paketet skript. |
| EULA_ENU.txt |Licensvillkor för användning av Microsoft Azure Service Fabric fristående installationspaketet för Windows Server. Du kan [hämta en kopia av LICENSVILLKOREN](http://go.microsoft.com/fwlink/?LinkID=733084) nu. |
| Viktigt.txt |En länk till viktig information och instruktioner för grundläggande installation. Det är en delmängd av instruktionerna i det här dokumentet. |
| ThirdPartyNotice.rtf |Meddelande om tredjepartsprogram som finns i paketet. |
| Tools\Microsoft.Azure.ServiceFabric.windowsserver.SupportPackage.zip |StandaloneLogCollector.exe som körs på begäran för att samla in och skicka loggar till Microsoft support syfte. |
| Tools\ServiceFabricUpdateService.zip |Ett verktyg som används för att aktivera automatisk koduppgradering för kluster som inte har tillgång till internet. Mer information hittar du [här](service-fabric-cluster-upgrade-windows-server.md)|

**Mallar** 
| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Ett kluster exempel konfigurationsfil som innehåller inställningarna för ett oskyddat, tre-nod, enskild dator (eller virtuella) development kluster, inklusive information för varje nod i klustret. |
| ClusterConfig.Unsecure.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller inställningarna för ett oskyddat, flera datorer (eller virtuella) kluster, inklusive information för varje dator i klustret. |
| ClusterConfig.Windows.DevCluster.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för en säker, tre noder enskild dator (eller virtuella datorn) utveckling kluster, inklusive information för varje nod i klustret. Klustret skyddas med hjälp av [Windows identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för ett säkert, flera datorer (eller virtuella datorn)-kluster med Windows-säkerhet, inklusive information för varje dator som är i säker klustret. Klustret skyddas med hjälp av [Windows identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för en säker, tre-nod, enskild dator (eller virtuella) development klustret, inklusive information för varje nod i klustret. Klustret skyddas med x509 certifikat. |
| ClusterConfig.x509.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för säker, flera datorer (eller virtuella) klustret, inklusive information för varje nod i klustret säker. Klustret skyddas med x509 certifikat. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för säker, flera datorer (eller virtuella) klustret, inklusive information för varje nod i klustret säker. Klustret skyddas med [Grupphanterade tjänstkonton](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Klustret Configuration-exempel
Senaste versionerna av klustrets Konfigurationsmallar finns på GitHub-sidan: [fristående kluster Configuration exempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Oberoende Runtime-paketet
Senaste körning paketet hämtas automatiskt under Klusterdistribution från [Hämta länk - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relaterat
* [Skapa ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
* [Säkerhetsscenarier för Service Fabric-kluster](service-fabric-windows-cluster-windows-security.md)
