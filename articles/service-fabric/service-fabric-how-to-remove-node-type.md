---
title: Ange hur till fjärr en nod i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du tar bort en nodtyp i Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980860"
---
# <a name="remove-a-service-fabric-node-type"></a>Ta bort en nodtyp för Service Fabric

Använd [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) att ta bort en nodtyp för Service Fabric.

De två åtgärderna som uppstår när Remove-AzureRmServiceFabricNodeType anropas är:
1.  Den virtuella datorn skala ange (VMSS) bakom nodtyp tas bort.
2.  För alla noder i den nodtypen tas hela tillståndet för noden bort från systemet. Om det finns tjänster på noden, sedan flyttas tjänsterna först till en annan nod. Om cluster manager i inte hittar en nod för repliken/tjänst, är åtgärden för fördröjd/blockerad.

> [!NOTE]
> Du bör inte använda Remove-AzureRmServiceFabricNodeType ta bort en nodtyp från ett produktionskluster som ska användas regelbundet. I det här fallet är det en mycket farliga kommandot eftersom det i praktiken tar bort virtuella datorns Skalningsuppsättning resurs bakom nodtyp. När du anropar Remove-AzureRmServiceFabricNodeType har systemet inget sätt att veta om du bryr dig om säkra borttagningen. 

## <a name="durability-characteristics"></a>Egenskaper för tillförlitlighet
Säkerhet är prioriterad över hastighet när du använder Remove-AzureRmServiceFabricNodeType. Silver eller Gold [hållbarhet egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) rekommenderas eftersom:
- Brons medför inga garantier om hur du sparar statusinformation.
- Silver och Gold hållbarhet trap ändringar i VMSS.
- Guld ger dig kontroll över Azure uppdaterar under VMSS.

Service Fabric ”samordnar” underliggande ändras och uppdateras så att data inte går förlorade. När du tar bort en nod med Brons tillförlitlighet, kan du förlora statusinformation. Om du ska ta bort en primära nodtypen och ditt program är tillståndslösa, är Brons acceptabelt. När du kör tillståndskänsliga arbetsbelastningar i produktion, ska den lägsta konfigurationen Silver. På samma sätt för produktionsscenarier ska den primära nodtypen alltid vara Silver eller Gold.

### <a name="more-about-bronze-durability"></a>Mer om Brons hållbarhet

När du tar bort en nodtyp som är Brons kraschar alla noder i nodtyp omedelbart. Service Fabric fånga inte Brons noder VMSS uppdateringar, alltså alla virtuella datorer tappar anslutningen omedelbart. Om du har något tillståndskänsliga på dessa noder, förloras data. Nu, även om du skulle tillståndslösa, som alla noder i Service Fabric delta upp i ringen så att en hela nätverket kan gå förlorade, som kan påverka själva klustret.

Till skillnad från tar bort en nod, eftersom du kan ta bort en nod i taget i teorin, vänta tills repliker och tjänster så att flytta över, vänta tills systemet stabilisera innan du tar bort en annan nod, och så vidare.  Om du tar bort flera noder samtidigt på samma gång, kan ditt kluster gå (eftersom Service Fabric inte trap VMSS uppdateringar med Brons hållbarhet).

## <a name="recommended-node-type-removal-process"></a>Rekommenderade borttagningsprocessen för nod-typ

Ta bort nodtyp på de mest snabb och säker sätt:
1.  Om du använder Brons hållbarhet eller inte vill att systemet för att flytta program som innehåller tillståndsinformationen som går förlorad efter nod typ borttagningen första tom tillståndskänsliga data från de noder som kommer att påverkas av noden typ borttagningen.
2.  Kör [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) på varje nod som du vill ta bort.
3.  Kör [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) för virtuella datorer som påverkas av noden typ borttagningen.
4. Kör [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) att ta bort nodtyp.

## <a name="next-steps"></a>Nästa steg
- Mer information om klustret [hållbarhet egenskaper](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Läs mer om [nodtyper och VM-Skalningsuppsättningar](service-fabric-cluster-nodetypes.md).
- Läs mer om [skalning av Service Fabric-klustret](service-fabric-cluster-scaling.md).