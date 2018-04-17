---
title: Skala Service Fabric-klustret in eller ut | Microsoft Docs
description: Skala Service Fabric-klustret in eller ut för att matcha begäran genom att ange regler för automatisk skalning för varje nod typ/virtuella datorns skaluppsättning. Lägg till eller ta bort noder till ett Service Fabric-kluster
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: aljo
ms.openlocfilehash: 506877e12d12ff3b1372cc0360a8df1a1d52744a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala Service Fabric-klustret in eller ut använda regler för automatisk skalning eller manuellt
Skaluppsättningar för den virtuella datorn är en Azure compute-resurs som du kan använda för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp som definieras i Service Fabric-klustret har konfigurerats som en separat virtuella datorns skaluppsättning. Varje nodtyp kan sedan skalas i ut oberoende av varandra, har olika uppsättningar av öppna portar och kan ha olika kapacitetsdata. Läs mer om den i den [nodetypes får Service Fabric](service-fabric-cluster-nodetypes.md) dokumentet. Eftersom typer för Service Fabric-nod i klustret görs i skalningsuppsättningar i virtuella datorer på serverdelen måste du ställa in automatisk skalning regler för varje nod typ/virtuella datorns skaluppsättning.

> [!NOTE]
> Din prenumeration måste ha tillräckligt med kärnor att lägga till de nya virtuella datorer som utgör det här klustret. Det finns ingen modellverifiering, så att du får tid distributionsfel, om någon av kvotgränserna nådde.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj noden typ/Virtual Machine skaluppsättningen att skala
Du är för närvarande inte att ange regler för automatisk skalning för skalningsuppsättningar i virtuella datorer med hjälp av portalen, så Låt oss använda Azure PowerShell (1.0 +) lista över typer noden och sedan lägga till Autoskala regler till dem.

Om du vill hämta listan över virtuella datorns skaluppsättning som utgör ditt kluster kör du följande cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för noden typ/virtuella datorns skaluppsättning
Om klustret har flera nodtyper, upprepar du detta för varje nod typer/virtuella datorn anger att du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [tillförlitlighetsnivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skala ned den primära noden typ till mindre än det minsta antalet se klustret instabil eller sätta. Detta kan resultera i förlust av data för dina program och systemtjänster.
> 
> 

Funktionen Autoskala är för närvarande inte styrs av belastning som dina program kan rapporterar till Service Fabric. Så just nu den Autoskala du rent styrs av de prestandaräknare som sänds av var och en av den virtuella datorn skaluppsättning instanser.  

Följ dessa instruktioner [att ställa in automatisk skalning för varje skaluppsättning för virtuell dator för](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I en skala ned scenario, om inte din nodtyp har en hållbarhet guld eller Silver måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med lämplig nod-namn.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Manuellt lägga till virtuella datorer i en nod typ/virtuella datorns skaluppsättning
Följ exemplet/instruktionerna i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra antalet virtuella datorer i varje Nodetype. 

> [!NOTE]
> Lägga till virtuella datorer tar tid, så förväntar sig inte vara omedelbar tilläggen. Planera därför att lägga till kapacitet i tid, så att mer än 10 minuter innan VM-kapaciteten är tillgänglig för replikerna / service instanser som ska placeras.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från det primära noden typ/virtuellt datorns skaluppsättning
> [!NOTE]
> Service fabric systemtjänsterna körs i den primära nodtypen i klustret. Mindre än vad tillförlitlighetsnivån garanterar så aldrig ska stänga av eller minska antalet instanser i de nodtyperna. Referera till [information på tillförlitlighet nivåerna här](service-fabric-cluster-capacity.md). 
> 
> 

Du måste köra den följande steg för en VM-instansen i taget. Detta ger systemtjänsterna (och tillståndskänsliga tjänster) stängs avslutas på VM-instans som du tar bort och nya repliker som skapas på andra noder.

1. Kör [inaktivera ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) med syftet 'RemoveNode' att inaktivera noden ska du ta bort (högsta instans i den nodtypen).
2. Kör [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) och kontrollera att noden verkligen har övergått till inaktiverad. Om inte, vänta tills noden är inaktiverad. Du kan skynda dig det här steget.
3. Följ exemplet/instruktionerna i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra antal virtuella datorer med en i den här nodtypen. Instansen tas bort är den högsta VM-instansen. 
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära noden typerna mindre än tillförlitlighetsnivån garanterar. Referera till [information på tillförlitlighet nivåerna här](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ta manuellt bort virtuella datorer från icke-primär nod typ/virtuella datorns skaluppsättning
> [!NOTE]
> För en tillståndskänslig tjänst behöver du ett visst antal noder alltid upp till att upprätthålla tillgänglighet och bevara tillståndet för din tjänst. Vid mycket minimum behöver du antalet noder lika med mål replik set antalet partitionen/service. 
> 
> 

Du måste köra de följande stegen en VM-instansen i taget. Detta ger systemtjänsterna (och tillståndskänsliga tjänster) stängs avslutas på VM-instans som du tar bort och nya repliker skapas annan var.

1. Kör [inaktivera ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) med syftet 'RemoveNode' att inaktivera noden ska du ta bort (högsta instans i den nodtypen).
2. Kör [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) och kontrollera att noden verkligen har övergått till inaktiverad. Om inte, vänta tills noden är inaktiverad. Du kan skynda dig det här steget.
3. Följ exemplet/instruktionerna i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra antal virtuella datorer med en i den här nodtypen. Den högsta VM-instansen tas nu bort. 
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära noden typerna mindre än tillförlitlighetsnivån garanterar. Referera till [information på tillförlitlighet nivåerna här](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Beteenden som du kan se i Service Fabric Explorer
När du skalar upp ett kluster visar Service Fabric Explorer antalet noder (virtuella skaluppsättning instanser) som ingår i klustret.  Men när du skalar ett kluster av du ser den borttagna nod och VM-instans som visas i ett feltillstånd om du anropar [ta bort ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) med lämplig nod-namn.   

Här är förklaring till problemet.

Noderna som anges i Service Fabric Explorer är en avbildning av vilka Service Fabric systemtjänsterna (FM specifikt) medveten om antalet noder i klustret hade/har. När du skalar virtuella datorns skaluppsättning anges den virtuella datorn har tagits bort men FM systemtjänst fortfarande tror att noden (som har mappats till den virtuella datorn som har tagits bort) kommer tillbaka. Service Fabric Explorer fortsätter så att visa noden (även om hälsotillståndet kan vara fel eller okänd).

För att se till att en nod ska tas bort när en virtuell dator tas bort, har du två alternativ:

1) Välja en hållbarhet guld eller Silver för nodtyper i klustret, som ger dig infrastruktur-integration. Som sedan bort noder från våra tjänster (FM) systemtillstånd när du skala.
Referera till [information om hållbarhet nivåer här](service-fabric-cluster-capacity.md)

2) När VM-instans har skalats, måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Service Fabric-kluster kräver ett visst antal noder som ska vara igång hela tiden för att underhålla tillgänglighet och bevara tillstånd - kallas ”för att underhålla kvorum”. Därför är det oftast säkert att stänga av alla datorer i klustret om du först har utfört en [fullständig säkerhetskopiering av din tillstånd](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs följande också mer information om planering av kapacitet för klustret, uppgradera ett kluster och partitionering tjänster:

* [Planera din kapacitet för kluster](service-fabric-cluster-capacity.md)
* [Klusteruppgradering](service-fabric-cluster-upgrade.md)
* [Partitionen tillståndskänsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
