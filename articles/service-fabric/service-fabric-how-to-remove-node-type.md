---
title: Ta bort en nodtyp i Azure Service Fabric
description: Lär dig hur du tar bort en nodtyp från ett Service Fabric kluster som körs i Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f3dc3210fdb436038174bb8d9347424f14d3faa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464503"
---
# <a name="remove-a-service-fabric-node-type"></a>Ta bort en Service Fabric nodtyp
Den här artikeln beskriver hur du skalar ett Azure Service Fabric-kluster genom att ta bort en befintlig nodtyp från ett kluster. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att ta bort en nodtyp (skalnings uppsättning för virtuell dator) och alla dess noder.  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) om du vill ta bort en nod av typen Service Fabric.

De tre åtgärderna som inträffar när Remove-AzServiceFabricNodeType anropas är:
1.  Skalnings uppsättningen för den virtuella datorn bakom nodtypen tas bort.
2.  Nodtypen tas bort från klustret.
3.  För varje nod inom den nodtypen tas hela statusen för noden bort från systemet. Om det finns tjänster på den noden flyttas tjänsterna först ut till en annan nod. Om kluster hanteraren inte kan hitta en nod för repliken/tjänsten är åtgärden försenad/blockerad.

> [!WARNING]
> Att använda Remove-AzServiceFabricNodeType för att ta bort en nodtyp från ett produktions kluster bör inte användas regelbundet. Det är ett farligt kommando eftersom det tar bort den virtuella datorns skalnings uppsättnings resurs bakom nodtypen. 

## <a name="durability-characteristics"></a>Hållbarhets egenskaper
Säkerhet prioriteras över hastighet när du använder Remove-AzServiceFabricNodeType. Nodtypen måste vara silver eller Gold- [hållbarhets nivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), eftersom:
- Brons ger inga garantier för att spara statusinformation.
- Silver-och guld tåligheten sväller alla ändringar i skalnings uppsättningen.
- Guld ger dig också kontroll över Azure updates under skalnings uppsättning.

Service Fabric "dirigerar" underliggande ändringar och uppdateringar så att inga data förloras. Men när du tar bort en nod med brons hållbarhet kan du förlora statusinformation. Om du tar bort en primär nodtyp och ditt program är tillstånds löst, är brons acceptabelt. När du kör tillstånds känsliga arbets belastningar i produktionen bör den lägsta konfigurationen vara silver. På samma sätt bör den primära nodtypen alltid vara silver eller guld för produktions scenarier.

### <a name="more-about-bronze-durability"></a>Mer om brons-hållbarhet

När du tar bort en nodtyp som är brons går alla noder i nodtypen omedelbart ned. Service Fabric sväller inte alla de virtuella datorerna för skalnings uppsättningar i brons-noder, och därför går alla virtuella datorer omedelbart ned Om du hade något tillstånd för dessa noder försvinner data. Även om du har tillstånds lösa kan alla noder i Service Fabric delta i ringen, vilket innebär att hela nätverket kan gå förlorat, vilket kan göra själva klustret.

## <a name="recommended-node-type-removal-process"></a>Rekommenderad borttagnings process för nodtyp

Om du vill ta bort nodtypen kör du cmdleten [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Det tar lite tid att slutföra cmdleten.  När alla virtuella datorer är borta (representeras som "nere") visas ett fel tillstånd i Fabric:/system/InfrastructureService/[NodeType Name].

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
```

Sedan kan du uppdatera kluster resursen för att ta bort nodtypen. Du kan antingen använda distribution av ARM-mallen eller redigera kluster resursen via [Azure Resource Manager](https://resources.azure.com). Då startas en kluster uppgradering som tar bort tjänsten Fabric:/system/InfrastructureService/[NodeType Name] som är i fel tillstånd.

Noderna visas fortfarande i Service Fabric Explorer. Kör [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) på varje nod som du vill ta bort.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om klustrets [hållbarhets egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Läs mer om [nodtyper och Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Läs mer om hur du [Service Fabric kluster skalning](service-fabric-cluster-scaling.md).
