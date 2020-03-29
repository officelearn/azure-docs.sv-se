---
title: Fristående Azure Service Fabric-paket för Windows Server
description: Beskrivning och innehåll i Azure Service Fabric-paketet för Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451853"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Fristående paket för Service Fabric för Windows Server
I det [nedladdade](https://go.microsoft.com/fwlink/?LinkId=730690) Fristående servicepaketet hittar du följande filer:

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| SkapaServiceFabricCluster.ps1 |Ett PowerShell-skript som skapar klustret med hjälp av inställningarna i ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Ett PowerShell-skript som tar bort ett kluster med inställningarna i ClusterConfig.json. |
| AddNode.ps1 |Ett PowerShell-skript för att lägga till en nod i ett befintligt distribuerat kluster på den aktuella datorn. |
| RemoveNode.ps1 |Ett PowerShell-skript för att ta bort en nod från ett befintligt distribuerat kluster från den aktuella datorn. |
| CleanFabric.ps1 |Ett PowerShell-skript för rengöring av en fristående Service Fabric-installation av den aktuella datorn. Tidigare MSI-installationer bör tas bort med egna associerade uninstallers. |
| TestConfiguration.ps1 |Ett PowerShell-skript för analys av infrastrukturen enligt vad som anges i Cluster.json. |
| HämtaServiceFabricRuntimePackage.ps1 |Ett PowerShell-skript som används för att hämta det senaste runtime-paketet utanför bandet, för scenarier där distributionsdatorn inte är ansluten till internet. |
| DeploymentComponentsAutoextractor.exe |Självextraherande arkiv som innehåller distributionskomponenter som används av fristående paketskript. |
| EULA_ENU.txt |Licensvillkoren för användning av Microsoft Azure Service Fabric fristående Windows Server-paket. Du kan [ladda ner en kopia av licensavtalet](https://go.microsoft.com/fwlink/?LinkID=733084) nu. |
| Readme.txt |En länk till viktig information och grundläggande installationsanvisningar. Det är en delmängd av instruktionerna i det här dokumentet. |
| Tredje PartNotice.rtf |Meddelande om programvara från tredje part som finns i paketet. |
| Verktyg\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |FriståendeLogCollector.exe som körs på begäran för att samla in och ladda upp spårningsloggar till Microsoft för supportändamål. |
| Verktyg\ServiceFabricUpdateService.zip |Ett verktyg som används för att aktivera automatisk koduppgradering för kluster som inte har tillgång till internet. Mer information hittar du [här](service-fabric-cluster-upgrade-windows-server.md)|

**Mallar** 

| **Filnamn** | **Kort beskrivning** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |En exempelfil för klusterkonfiguration som innehåller inställningarna för ett oskyddat utvecklingskluster för en nod, trenod, en dator (eller virtuell dator), inklusive informationen för varje nod i klustret. |
| ClusterConfig.Unsecure.MultiMachine.json |En exempelfil för klusterkonfiguration som innehåller inställningarna för ett oskyddat kluster med flera datorer (eller virtuell dator), inklusive informationen för varje dator i klustret. |
| ClusterConfig.Windows.DevCluster.json |En exempelfil för klusterkonfiguration som innehåller alla inställningar för ett säkert utvecklingskluster för en nod, en dator (eller virtuell dator), inklusive informationen för varje nod som finns i klustret. Klustret skyddas med hjälp av [Windows-identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |En exempelfil för klusterkonfiguration som innehåller alla inställningar för ett säkert kluster med flera datorer (eller virtuella datorer) med Windows-säkerhet, inklusive informationen för varje dator som finns i det säkra klustret. Klustret skyddas med hjälp av [Windows-identiteter](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |En exempelfil för klusterkonfiguration som innehåller alla inställningar för ett säkert utvecklingskluster för en nod, en dator (eller virtuell dator), inklusive informationen för varje nod i klustret. Klustret är skyddat med x509-certifikat. |
| ClusterConfig.x509.MultiMachine.json |En exempelfil för klusterkonfiguration som innehåller alla inställningar för det säkra kluster med flera datorer (eller virtuella datorer), inklusive informationen för varje nod i det säkra klustret. Klustret är skyddat med x509-certifikat. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |En exempelfil för klusterkonfiguration som innehåller alla inställningar för det säkra kluster med flera datorer (eller virtuella datorer), inklusive informationen för varje nod i det säkra klustret. Klustret är skyddat med [grupphanterade tjänstkonton](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Exempel på klusterkonfiguration
Senaste versionerna av klusterkonfigurationsmallar finns på GitHub-sidan: [Fristående klusterkonfigurationsexempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Oberoende körningspaket
Det senaste körningspaketet hämtas automatiskt under klusterdistribution från [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relaterat
* [Skapa ett fristående Azure Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
* [Säkerhetsscenarier för service fabric-kluster](service-fabric-windows-cluster-windows-security.md)
