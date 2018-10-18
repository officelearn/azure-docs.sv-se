---
title: Skala ett Service Fabric-kluster in eller ut | Microsoft Docs
description: Skala ett Service Fabric-klustret in eller ut för att matcha begäran genom att ange regler för automatisk skalning för varje nod typ/Virtual Machine scale Sets. Lägga till eller ta bort noder till ett Service Fabric-kluster
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: aljo
ms.openlocfilehash: 0d809f9a1b3abbb284c3f7e0c27eb9c236692a3f
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386473"
---
# <a name="read-before-you-scale"></a>Läsa innan du skalar
Skala beräkningsresurser till källan arbetsbelastning för ditt program kräver avsiktlig planering, nästan alltid tar längre tid än en timme att slutföra för en produktionsmiljö och kräver att du kan läsa mer arbetsbelastning affärskontexten; i själva verket om du aldrig har gjort den här aktiviteten innan, bör du börja med att läsa och förstå [Service Fabric-kluster kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity), innan du fortsätter med resten av det här dokumentet. Den här rekommendationen är att undvika oväntade LiveSite problem och vi rekommenderar också att du har testa de åtgärder som du vill utföra mot en icke-produktionsmiljö. Du kan när som helst [rapportera produktionsproblem eller begära betald support för Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-production-issues-or-request-paid-support-for-azure). För tekniker som allokerats för att utföra dessa åtgärder som har rätt kontext, i den här artikeln beskriver skalningsåtgärder, men du måste bestämma och förstå vilka åtgärder som är lämpliga för ditt användningsområde; till exempel vilka resurser för att skala (processor, lagring, minne), vilken riktning skala (vågrätt eller lodrätt) och vilka åtgärder att utföra (resursmall distribution, Portal, PowerShell/CLI).

# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala ett Service Fabric-kluster in eller ut med hjälp av regler för automatisk skalning eller manuellt
Virtual machine scale sets är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i Service Fabric-kluster har ställts in som en separat VM-skalningsuppsättning. Varje nodtyp skalas sedan in eller ut oberoende av varandra, ha olika portar öppna och ha olika kapacitet. Läs mer om den i den [Service Fabric nodetypes](service-fabric-cluster-nodetypes.md) dokumentet. Eftersom Service Fabric-nodtyper i klustret består av VM-skalningsuppsättningar i serverdelen, behöver du konfigurera regler för automatisk skalning för varje nod typ/Virtual Machine scale Sets.

> [!NOTE]
> Prenumerationen måste ha tillräckligt många kärnor du lägger till de nya virtuella datorerna som tillsammans bildar det här klustret. Det finns ingen Modellvalidering, så att du får tid distributionsfel, om någon av kvotgränserna uppnår. En enda nodtyp kan också inte bara överstiga 100 noder per VMSS. Du kan behöva lägga till VMSS: er för att få den riktade skalan och automatisk skalning kan inte automagically lägga till VMSS'S. Lägga till VMSSS på plats till ett aktivt kluster är en utmaning och ofta detta leder användare etablera nya kluster med lämpliga nodtyperna etablerats vid tidpunkten för skapandet; [planera klusterkapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) därefter. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj noden typ/virtuell datorskalning som anger att skala
Du är för närvarande inte kan ange regler för automatisk skalning för VM scale sets med hjälp av portalen så kan vi använda Azure PowerShell (1.0 +) lista över typer noden och sedan lägga till regler för automatisk skalning till dem om du vill skapa ett Service Fabric-kluster.

Om du vill hämta listan över virtuella datorns skaluppsättning som utgör ditt kluster, kör du följande cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för noden typ/Virtual Machine scale Sets
Om klustret har flera nodtyper, upprepar du detta för varje nod typer/virtuell datorskalning anger att du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [tillförlitlighetsnivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skala ned den primära noden skriver till mindre än det minsta antalet gör klustret instabil eller påverkar den. Detta kan resultera i dataförlust för dina program och för systemtjänster.
> 
> 

Funktionen för automatisk skalning är för närvarande inte styrs av belastning som dina program kan ska rapportera till Service Fabric. Så just nu Autoskala du får helt och hållet drivs av prestandaräknare som genereras av den virtuella datorn skaluppsättningsinstanser.  

Följ de här instruktionerna [att ställa in automatisk skalning för varje VM-skalningsuppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I en skala ned scenario, om inte din nodtyp har en hållbarhetsnivå guld eller Silver måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med namnet på lämplig noden. För hållbarhet Brons rekommenderar vi inte för att skala ned mer än en nod i taget.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Lägga till virtuella datorer manuellt till en nod typ/Virtual Machine scale Sets
I anvisningarna sample/i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra hur många virtuella datorer i varje Nodetype. 

> [!NOTE]
> Tillägg av virtuella datorer tar tid, så du inte räknar med tillägg till direkt. Planera så att lägga till kapacitet bra i tid, så att mer än 10 minuter innan den VM-kapaciteten är tillgänglig för replikerna / service-instanser till placeras.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från den primära noden typ/Virtual Machine scale Sets
> [!NOTE]
> Service fabric-systemtjänster kör i den primära nodtypen i klustret. Så bör aldrig att Stäng av eller skala ned antalet instanser i de nodtyperna garanterar mindre än vad tillförlitlighetsnivån. Referera till [information på nivåerna för tillförlitlighet här](service-fabric-cluster-capacity.md). 
> 
> 

Du behöver köra den följande steg för en VM-instansen i taget. Det möjliggör systemtjänster (och dina tillståndskänsliga tjänster) för att stängas av smidigt på VM-instans som du tar bort och nya repliker som skapats på andra noder.

1. Kör [inaktivera ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) med avsikt 'RemoveNode' att inaktivera noden ska du ta bort (den högsta instansen i den nodtypen).
2. Kör [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) att se till att noden verkligen har övergått till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Du kan skynda dig det här steget.
3. I anvisningarna sample/i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra hur många virtuella datorer med en i den nodtypen. Instansen tas bort är den högsta VM-instansen. 
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära nodtyperna mindre än tillförlitlighetsnivån kräver. Referera till [information på nivåerna för tillförlitlighet här](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från icke-primära noden typ/Virtual Machine scale Sets
> [!NOTE]
> För en tillståndskänslig tjänst behöver du ett visst antal noder som ska vara alltid upp till att bibehålla tillgänglighet och bevara tillståndet för din tjänst. Vid mycket åtminstone behöver du antalet noder lika target set replikantalet för partitionen/tjänsten. 
> 
> 

Du måste köra de följande stegen en VM-instansen i taget. Detta ger systemtjänster (och dina tillståndskänsliga tjänster) för att stängas av smidigt på VM-instans som du tar bort och nya repliker skapas annan var.

1. Kör [inaktivera ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) med avsikt 'RemoveNode' att inaktivera noden ska du ta bort (den högsta instansen i den nodtypen).
2. Kör [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) att se till att noden verkligen har övergått till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Du kan skynda dig det här steget.
3. I anvisningarna sample/i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra hur många virtuella datorer med en i den nodtypen. Detta tar nu bort högsta VM-instansen. 
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära nodtyperna mindre än tillförlitlighetsnivån kräver. Referera till [information på nivåerna för tillförlitlighet här](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Beteenden som du kan se i Service Fabric Explorer
När du skalar upp ett kluster visas antalet noder (VM-skalningsuppsättningen instanser) som ingår i klustret med Service Fabric Explorer.  Men när du skalar ett kluster problem kan du ser den borttagna nod/VM-instansen som visas i ett feltillstånd, såvida inte du anropar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med namnet på lämplig noden.   

Här är en förklaring för det här beteendet.

Noderna visas i Service Fabric Explorer är en avbildning av vilka de Service Fabric-systemtjänsterna (FM specifikt) känner antalet noder i klustret hade/har. När du skalar skalningsuppsättning för virtuell dator den virtuella datorn togs bort men FM systemtjänsten fortfarande tror att noden (som har kopplats till den virtuella datorn som har tagits bort) kommer tillbaka. Service Fabric Explorer fortsätter så att visa noden (även om hälsotillståndet kan vara fel eller okänd).

För att se till att en nod tas bort när en virtuell dator tas bort, har du två alternativ:

1) Välj hållbarhet nivån guld eller Silver för nodtyperna i klustret, vilket ger dig infrastruktur-integrering. Som sedan bort noderna från våra tjänster (FM) systemtillstånd när du skalar.
Referera till [information om hållbarhet nivåer här](service-fabric-cluster-capacity.md)

2) När VM-instansen har skalats, måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric-kluster kräver ett visst antal noder att vara igång hela tiden för att bibehålla tillgänglighet och bevara tillstånd - kallas ”upprätthålla kvorum”. Därför är det oftast säkert att Stäng alla datorer i klustret, såvida inte du först har utfört en [fullständig säkerhetskopiering av din delstat](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs följande för att också lära dig om planera klusterkapacitet, uppgradera ett kluster och partitionering tjänster:

* [Planera din klusterkapacitet](service-fabric-cluster-capacity.md)
* [Klusteruppgradering](service-fabric-cluster-upgrade.md)
* [Partition tillståndskänsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
