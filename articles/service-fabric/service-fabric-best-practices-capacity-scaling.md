---
title: Kapacitetsplanering och skalning för Azure Service Fabric | Microsoft Docs
description: Bästa praxis för planering och skalning av Service Fabric-kluster och program.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 58c50eac60f1a8a47aac9a88125bc3e0132ec3db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059158"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitetsplanering och skalning för Azure Service Fabric

Innan du skapar ett Azure Service Fabric-kluster eller skala beräkningsresurser som är värdar för klustret, är det viktigt att planera kapaciteten. Läs mer om hur du planerar kapaciteten [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Ytterligare bästa praxis riktlinjer för klustret skalbarhet, se [överväganden angående skalbarhet av Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Utöver att bedöma noden egenskaper typ och -kluster, bör du förväntar dig skalningsåtgärder tar längre tid än en timme att slutföra för en produktionsmiljö. Detta gäller oavsett hur många virtuella datorer du vill lägga till.

## <a name="autoscaling"></a>Automatisk skalning
Du bör utföra skalningsåtgärder via Azure Resource Manager-mallar, eftersom det är bäst att behandla [resurskonfigurationer som kod]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Med automatisk skalning med VM-skalningsuppsättningar gör version Resource Manager-mallen felaktigt definiera antalet instanser för VM-skalningsuppsättningar. Felaktig definition ökar risken att oönskade skalningsåtgärder resulterar i framtida distributioner. I allmänhet bör du använda automatisk skalning om:

* Distribuera dina Resource Manager-mallar med rätt kapacitet som deklarerats stöder inte ditt användningsområde.
     
   Manuell skalning kan du konfigurera en [kontinuerlig integrering och leverans pipeline i Azure DevOps-tjänster med hjälp av Azure-resurs distributionsprojekt](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Denna pipeline utlöses ofta av en logikapp som använder prestandamått för virtuella datorer som efterfrågas från den [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Pipelinen effektivt skalar baserat på måtten du vill, under optimering för Resource Manager-mallar.
* Du behöver skala vågrätt endast en VM scale set nod i taget.
   
   Om du vill skala ut med tre eller fler noder i taget, bör du [skala ut ett Service Fabric-kluster genom att lägga till en VM-skalningsuppsättning](virtual-machine-scale-set-scale-node-type-scale-out.md). Är det säkrast att skala in och skala ut virtuella datorer anger vågrätt, en nod i taget.
* Du har Silver tillförlitlighet eller högre för Service Fabric-kluster och Silver hållbarhet eller senare på valfri skala konfigurerar du reglerna för automatisk skalning.
  
   Den minsta kapaciteten för reglerna för automatisk skalning måste vara lika med eller större än fem instanser av virtuella datorer. Det måste också vara lika med eller större än din tillförlitlighetsnivån minimum för den primära nodtypen.

> [!NOTE]
> Service Fabric tillståndskänslig service fabric: / System/InfastructureService/< NODE_TYPE_NAME > körs på varje nodtyp som har Silver eller högre tillförlitlighet. Det är den enda systemtjänst som går för att köra i Azure på någon av dina kluster nodtyper.

## <a name="vertical-scaling-considerations"></a>Lodrät skalning överväganden

[Vertikal skalning](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) en nodtyp i Azure Service Fabric kräver ett antal åtgärder och överväganden. Exempel:

* Klustret måste vara felfria innan du skalar. I annat fall kommer du bli instabilt klustret ytterligare.
* Silver hållbarhet nivå eller högre krävs för alla Service Fabric-kluster nodtyper som är värdar för tillståndskänsliga tjänster.

> [!NOTE]
> Den primära nodtypen som är värd för tillståndskänslig Service Fabric-systemtjänster måste vara Silver hållbarhet nivå eller högre. När du har aktiverat Silver hållbarhet klusteråtgärder, till exempel uppgraderingar, blir att lägga till eller ta bort noder och så vidare långsammare eftersom systemet optimerar för datasäkerheten över hastigheten på åtgärder.

Vertikal skalning en VM-skalningsuppsättning är en destruktiv åtgärd. I stället vågrätt skala ditt kluster genom att lägga till en ny skalningsuppsättning med önskad SKU: N. Sedan migrera dina tjänster till din önskade SKU för att slutföra en säker lodrät skalning åtgärd. Ändra en VM scale set resurs-SKU: N är en destruktiv åtgärd eftersom den skapar ny avbildning av dina värdar, vilken tar bort alla lokalt sparade tillstånd.

Klustret använder Service Fabric [nodegenskaper och placeringsbegränsningar](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) du bestämma om att tillhandahålla tjänster för ditt program. När du skalar lodrätt den primära nodtypen, deklarera identiska egenskapsvärden för `"nodeTypeRef"`. Du hittar dessa värden i Service Fabric-tillägget för VM-skalningsuppsättningar. 

Resource Manager-mall följande kodavsnitt visar egenskaper som du måste deklarera. Den har samma värde för de nyetablerade skalningsuppsättningar som du skalar till och stöds endast som en tillfällig tillståndskänslig tjänst för klustret.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Lämna inte ditt kluster som körs med flera skalningsuppsättningar som använder samma `nodeTypeRef` egenskapsvärde som är längre än krävs för att slutföra en lyckad åtgärd för lodrät skalning.
>
> Verifiera alltid åtgärder i testmiljöer innan du försöker ändringar till produktionsmiljön. Som standard har Service Fabric-kluster systemtjänster villkoret placering till bara den primära noden måltypen.

Med den nodegenskaper och placeringsbegränsningar som deklarerats, gör du följande steg för en VM-instansen i taget. Detta gör systemtjänster (och dina tillståndskänsliga tjänster) kan stängas av smidigt på VM-instans som du ska ta bort då nya repliker skapas någon annanstans.

1. Från PowerShell, kör `Disable-ServiceFabricNode` med avsikt `RemoveNode` att inaktivera noden ska du ta bort. Ta bort nodtyp som har det högsta antalet. Till exempel om du har ett kluster med sex noder tar du bort ”MyNodeType_5” VM-instans.
2. Kör `Get-ServiceFabricNode` att se till att noden har gått över till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Det kan ta ett par timmar för varje nod. Inte fortsätta förrän noden har gått över till inaktiverad.
3. Minska antalet virtuella datorer med en i den nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära nodtyperna mindre än tillförlitlighetsnivån kräver. Se [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.

### <a name="example-scenario"></a>Exempel på ett scenario
Ett scenario som stöds när du vill utföra en åtgärd med vertikal skalning är: du vill migrera dina Service Fabric-kluster och program från en ohanterad disk till hanterade diskar utan driftavbrott. 

Du kan etablera en ny VM-skalningsuppsättning med hanterade diskar och utföra en uppgradering av programmet med placeringsbegränsningar som är riktade till etablerad kapacitet. Service Fabric-klustret kan sedan schemalägga din arbetsbelastning på etablerade klustret noden kapaciteten som distribueras av uppgraderingsdomän utan driftavbrott. 

Backend-poolen slutpunkter för den [Azure Load Balancers grundläggande SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kan vara virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. Det innebär att du inte kan använda en grundläggande SKU-belastningsutjämnare om du flyttar dina system Service Fabric-program mellan skalningsuppsättningar, utan att orsaka tillfälliga inaccessibility för hanteringsslutpunkten Service Fabric-kluster. Detta gäller även om klustret och dess program körs fortfarande.

Användare etablera ofta en Standard-SKU-belastningsutjämnare när du utför en virtuell IP-adress (VIP)-växling mellan Basic SKU belastningsutjämnare och Standard-SKU resurser för belastningsutjämning. Den här tekniken begränsar alla framtida inaccessibility till 30 sekunder krävs för VIP växlar.

## <a name="horizontal-scaling"></a>Horisontell skalning

Du kan göra horisontell skalning antingen [manuellt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) eller [programmässigt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Om du skalar en nodtyp som har Silver eller Gold hållbarhet skalning kommer att vara långsamma.

### <a name="scaling-out"></a>Skala ut

Skala ut ett Service Fabric-kluster genom att öka instansantalet för en viss VM-skalningsuppsättning. Du kan skala ut med hjälp av programmässigt `AzureClient` och ID för önskad skalningsuppsättningen att öka kapaciteten.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Om du vill skala ut manuellt uppdatera kapaciteten i SKU-egenskapen för den önskade [virtual machine scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) resurs.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skala in

Skala i kräver mer övervägande än skala ut. Exempel:

* Service Fabric-systemtjänster kör i den primära nodtypen i klustret. Aldrig stänga eller skala ned antalet instanser för den nodtypen så att du har färre instanser än tillförlitlighetsnivån kräver. 
* För en tillståndskänslig tjänst behöver du ett visst antal noder som alltid är igång för att bibehålla tillgänglighet och bevara tillståndet för din tjänst. Minst behöver du ett antal noder lika target set replikantalet av partitionen eller tjänsten.

Följ dessa steg om du vill skala i manuellt:

1. Från PowerShell, kör `Disable-ServiceFabricNode` med avsikt `RemoveNode` att inaktivera noden ska du ta bort. Ta bort nodtyp som har det högsta antalet. Till exempel om du har ett kluster med sex noder tar du bort ”MyNodeType_5” VM-instans.
2. Kör `Get-ServiceFabricNode` att se till att noden har gått över till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Det kan ta ett par timmar för varje nod. Inte fortsätta förrän noden har gått över till inaktiverad.
3. Minska antalet virtuella datorer med en i den nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till 3 tills du etablerar den kapacitet som du vill. Inte skala ned antalet instanser i de primära nodtyperna till färre än tillförlitlighetsnivån kräver. Se [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.

Om du vill skala i manuellt uppdatera kapaciteten i SKU-egenskapen för den önskade [virtual machine scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) resurs.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Du måste förbereda noden för nedstängning för att skala i programmässigt. Hitta noden ska tas bort (den högsta instans noden). Exempel:

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Inaktivera och ta bort noden med hjälp av samma `FabricClient` instans (`client` i det här fallet) och -nod (`instanceIdString` i det här fallet) som du använde i den tidigare koden:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> När du skalar ned ett kluster, visas den borttagna nod och VM-instans som visas i ett feltillstånd i Service Fabric Explorer. En förklaring av det här problemet finns i [beteenden som du kan se i Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Du kan:
> * Anropa den [kommandot Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) med namnet på lämplig noden.
> * Distribuera den [hjälpprogram för Service Fabric Autoskala](https://github.com/Azure/service-fabric-autoscale-helper/) i klustret. Det här programmet säkerställer att skalas ned noderna tas bort från Service Fabric Explorer.

## <a name="reliability-levels"></a>Tillförlitlighetsnivåer

Den [tillförlitlighetsnivån](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) är en egenskap i Service Fabric-klusterresursen. Det kan inte konfigureras på olika sätt för enskilda nodtyper. Den styr replikeringsfaktorn systemtjänster för klustret och är en inställning på klusternivå för resursen. 

Tillförlitlighetsnivån avgör det minsta antalet noder som den primära nodtypen måste innehålla. Tillförlitlighetsnivån kan ha följande värden:

* Platina: Kör systemtjänster med en target replica set uppräkning av sju och nio startvärdesnoder.
* Gold: Kör systemtjänster med en target replica set uppräkning av sju och sju startvärdesnoder.
* Silver: Kör systemtjänster med en target replica set uppräkning av fem och fem startvärdesnoder.
* Brons: Kör systemtjänster med en target replica set uppräkning av tre och tre startvärdesnoder.

Den minsta rekommenderade tillförlitlighetsnivån är Silver.

Tillförlitlighetsnivån som anges i egenskapsavsnittet i den [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), så här:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Hållbarhet nivåer

> [!WARNING]
> Nodtyper som körs med Brons hållbarhet hämta _saknad behörighet_. Infrastruktur för jobb som påverkar din tillståndslösa arbetsbelastningar kan inte stoppas eller fördröjd, som kan påverka dina arbetsbelastningar. 
>
> Använd Brons hållbarhet enbart för nodtyper som kör tillståndslösa arbetsbelastningar. Kör Silver eller högre för att säkerställa konsekvens tillstånd för produktionsarbetsbelastningar. Välj rätt tillförlitligheten baserat på vägledningen i det [dokumentation för kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Hållbarhetsnivå måste ställas in i två resurser. En är tilläggsprofilen för den [VM-skalningsuppsättningen resource](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Andra resursen är `nodeTypes` i den [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Lär dig mer om [Service Fabric-supportalternativ](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
