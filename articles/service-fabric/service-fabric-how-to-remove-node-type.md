---
title: Ta bort en nodtyp i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du tar bort en nodtyp från ett Service Fabric-kluster som körs i Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787123"
---
# <a name="remove-a-service-fabric-node-type"></a>Ta bort en nodtyp för Service Fabric
Den här artikeln beskriver hur du skalar en Azure Service Fabric-kluster genom att ta bort en befintlig nodtyp från ett kluster. Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Virtual machine scale sets är en Azure-beräkningsresurs som används för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [ställa in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster, kan du skala ett kluster horisontellt genom att ta bort en nodtyp (virtual machine scale Sets) och alla dess noder.  Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.  När klustret skalas skalas programmen automatiskt samt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) att ta bort en nodtyp för Service Fabric.

De tre åtgärder som inträffar när Remove-AzServiceFabricNodeType anropas är:
1.  Skalningsuppsättning för virtuell dator bakom nodtyp tas bort.
2.  Nodtyp tas bort från klustret.
3.  För varje nod i den nodtypen tas hela tillståndet för noden bort från systemet. Om det finns tjänster på noden, sedan flyttas tjänsterna först till en annan nod. Om cluster manager i inte hittar en nod för repliken/tjänst, är åtgärden för fördröjd/blockerad.

> [!WARNING]
> Du bör inte använda Remove-AzServiceFabricNodeType att ta bort en nodtyp från ett produktionskluster som ska användas regelbundet. Det är ett farliga kommando som tar bort VM scale set resursen bakom nodtyp. 

## <a name="durability-characteristics"></a>Egenskaper för tillförlitlighet
Säkerhet är prioriterad över hastighet när du använder Remove-AzServiceFabricNodeType. Nodtypen måste vara Silver eller Gold [hållbarhetsnivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)eftersom:
- Brons medför inga garantier om hur du sparar statusinformation.
- Silver och Gold hållbarhet trap ändringar av skalningsuppsättningen.
- Guld ger dig kontroll över Azure uppdaterar under skalningsuppsättning.

Service Fabric ”samordnar” underliggande ändras och uppdateras så att data inte går förlorade. När du tar bort en nod med Brons tillförlitlighet, kan du förlora statusinformation. Om du ska ta bort en primära nodtypen och ditt program är tillståndslösa, är Brons acceptabelt. När du kör tillståndskänsliga arbetsbelastningar i produktion, ska den lägsta konfigurationen Silver. På samma sätt för produktionsscenarier ska den primära nodtypen alltid vara Silver eller Gold.

### <a name="more-about-bronze-durability"></a>Mer om Brons hållbarhet

När du tar bort en nodtyp som är Brons kraschar alla noder i nodtyp omedelbart. Service Fabric fånga inte Brons noder scale set uppdateringar, alltså alla virtuella datorer tappar anslutningen omedelbart. Om du har något tillståndskänsliga på dessa noder, förloras data. Nu, även om du skulle tillståndslösa, som alla noder i Service Fabric delta upp i ringen så att en hela nätverket kan gå förlorade, som kan bli instabilt själva klustret.

## <a name="recommended-node-type-removal-process"></a>Rekommenderade borttagningsprocessen för nod-typ

Om du vill ta bort nodtyp, kör den [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet.  Cmdlet: en tar lite tid att slutföra.  Kör sedan [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) på varje nod som du vill ta bort.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Nästa steg
- Mer information om klustret [hållbarhet egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Läs mer om [nodtyper och VM-Skalningsuppsättningar](service-fabric-cluster-nodetypes.md).
- Läs mer om [skalning av Service Fabric-klustret](service-fabric-cluster-scaling.md).
