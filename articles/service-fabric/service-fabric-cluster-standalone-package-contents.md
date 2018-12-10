---
title: Azure Service Fabric fristående paketet för Windows Server | Microsoft Docs
description: Beskrivning och innehållet i det Azure Service Fabric fristående paketet för Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1a34ad340463faf0cdccdb7fa8b87283de60b4cc
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141619"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Innehåll i Service Fabric fristående paketet för Windows Server
I den [ned](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric fristående paketet, hittar du följande filer:

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Ett PowerShell.skript som skapar klustret med inställningarna i ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Ett PowerShell-skript som tar bort ett kluster med inställningarna i ClusterConfig.json. |
| AddNode.ps1 |Ett PowerShell-skript för att lägga till en nod till ett befintligt distribuerat kluster på den aktuella datorn. |
| RemoveNode.ps1 |Ett PowerShell-skript för att ta bort en nod från ett befintligt distribuerat kluster från den aktuella datorn. |
| CleanFabric.ps1 |Ett PowerShell-skript för att rensa en fristående Service Fabric-installationen av den aktuella datorn. Tidigare MSI-installationer bör tas bort med hjälp av sina egna associerade uninstallers. |
| TestConfiguration.ps1 |Ett PowerShell-skript för att analysera infrastrukturen som anges i Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Ett PowerShell-skript som används för att ladda ned det senaste runtime-paketet out of band för scenarier där den distribuera datorn inte är ansluten till internet. |
| DeploymentComponentsAutoextractor.exe |Självextraherande arkiv som innehåller distributionskomponenter som används av fristående paketet skript. |
| EULA_ENU.txt |Licensvillkor för användning av Microsoft Azure Service Fabric fristående Windows Server-paketet. Du kan [hämta en kopia av LICENSVILLKOREN](https://go.microsoft.com/fwlink/?LinkID=733084) nu. |
| Readme.txt |En länk till viktig information och instruktioner för grundläggande installation. Det är en delmängd av instruktionerna i det här dokumentet. |
| ThirdPartyNotice.rtf |Meddelande om programvara från tredje part som finns i paketet. |
| Tools\Microsoft.Azure.ServiceFabric.windowsserver.SupportPackage.zip |StandaloneLogCollector.exe som körs på begäran för att samla in och överföra spårningsloggar till Microsoft för support ändamål. |
| Tools\ServiceFabricUpdateService.zip |Ett verktyg som används för att aktivera automatisk koduppgradering för kluster som inte har Internetåtkomst. Mer information finns [här](service-fabric-cluster-upgrade-windows-server.md)|

**Mallar** 

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |En kluster-exemplet konfigurationsfil som innehåller inställningar för ett oskyddat, tre noder, enkel dator (eller virtuell dator) utvecklingskluster, inklusive information för varje nod i klustret. |
| ClusterConfig.Unsecure.MultiMachine.json |En kluster-exemplet konfigurationsfil som innehåller inställningar för ett oskyddat, flera (eller andra) kluster, inklusive information för varje dator i klustret. |
| ClusterConfig.Windows.DevCluster.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för ett säkert, tre noder, enkel dator (eller virtuell dator) kluster för utveckling, inklusive information för varje nod i klustret. Klustret skyddas med hjälp av [Windows identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för ett säkert, kluster för flera datorer (eller virtuell dator) med hjälp av Windows-säkerhet, inklusive information för varje dator som tillhör det säkra klustret. Klustret skyddas med hjälp av [Windows identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för en säker, tre noder, enkel dator (eller virtuell dator) utvecklingsklustret, inklusive information för varje nod i klustret. Klustret skyddas med x509 certifikat. |
| ClusterConfig.x509.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för klustret säker, flera datorer (eller virtuell dator), inklusive information för varje nod i det säkra klustret. Klustret skyddas med x509 certifikat. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Ett kluster exempel konfigurationsfil som innehåller alla inställningar för klustret säker, flera datorer (eller virtuell dator), inklusive information för varje nod i det säkra klustret. Klustret skyddas med [Grupphanterade tjänstkonton](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Konfigurationsexempel för kluster
Senaste versionerna av klustret Konfigurationsmallar finns på GitHub-sida: [Konfigurationsexempel för fristående kluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Oberoende Runtime-paketet
Senaste körningspaketet laddas ned automatiskt under distributionen av klustret från [Hämta länk - Service Fabric-körningen – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relaterat
* [Skapa ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
* [Säkerhetsscenarier för Service Fabric-kluster](service-fabric-windows-cluster-windows-security.md)
