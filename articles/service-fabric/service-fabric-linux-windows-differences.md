---
title: Skillnader mellan Azure Service Fabric i Linux och Windows | Microsoft Docs
description: Skillnader mellan Azure Service Fabric Preview i Linux och Azure Service Fabric i Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5faf7dc0b544f6fe2f83565cc368e218c6df35af
ms.lasthandoff: 03/28/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Skillnader mellan Service Fabric i Linux (förhandsversion) och Windows (allmänt tillgänglig)

Eftersom Service Fabric i Linux är en förhandsversion är det vissa funktioner som bara finns i Windows, inte i Linux. Dessa funktioner kommer att finnas även i Linux när Service Fabric i Linux blir allmänt tillgänglig.

* Tillförlitliga samlingar (och tillförlitliga tillståndskänsliga tjänster) stöds inte i Linux.
* ReverseProxy är inte tillgängligt i Linux.
* Fristående installationsprogram är inte tillgängligt i Linux.
* XML-schemavalideringen för manifestfiler utförs inte i Linux. 
* Omdirigering av konsol stöds inte i Linux. 
* FAS (Fault Analysis Service) är inte tillgängligt i Linux.
* Azure Active Directory-stöd är inte tillgängligt i Linux.
* Vissa CLI-kommandon som motsvarar Powershell-kommandon är inte tillgängliga.
* Endast vissa av Powershell-kommandona kan köras mot ett Linux-kluster (se nästa avsnitt).

>[!NOTE]
>Omdirigering av konsol stöds inte i produktionskluster (gäller även Windows).

Utvecklingsverktygen skiljer sig för VisualStudio, Powershell, VSTS och ETW som används i Windows och Yeoman, Eclipse, Jenkins och LTTng som används i Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>PowerShell-cmdletar som inte fungerar mot ett Linux Service Fabric-kluster

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Nästa steg
* [Förbereda utvecklingsmiljön i Linux](service-fabric-get-started-linux.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Använd Azure CLI för att hantera dina Service Fabric-program](service-fabric-azure-cli.md)

