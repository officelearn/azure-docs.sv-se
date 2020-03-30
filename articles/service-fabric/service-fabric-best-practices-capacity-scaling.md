---
title: Kapacitetsplanering och skalning för Azure Service Fabric
description: Metodtips för planering och skalning av kluster och program för serviceinfrastruktur.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258998"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitetsplanering och skalning för Azure Service Fabric

Innan du skapar ett Azure Service Fabric-kluster eller skalar beräkningsresurser som är värdar för klustret är det viktigt att planera för kapacitet. Mer information om planering av kapacitet finns i [Planera klusterkapaciteten Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Mer information om bästa praxis för klusterskalbarhet finns i [Skalbarhetsöverväganden](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)för service fabric.

Förutom att ta hänsyn till nodtyp och klusteregenskaper bör du förvänta dig att skalningsåtgärder tar längre tid än en timme att slutföra för en produktionsmiljö. Det här övervägandet gäller oavsett hur många virtuella datorer du lägger till.

## <a name="autoscaling"></a>Automatisk skalning
Du bör utföra skalningsåtgärder via Azure Resource Manager-mallar, eftersom det är den bästa metoden för att behandla [resurskonfigurationer som kod]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Om du använder automatisk skalning via skalningsuppsättningar för virtuella datorer får din versionsutnyttjade Resource Manager-mall att definiera dina instansantal för skalningsuppsättningar för virtuella datorer. Felaktig definition ökar risken för att framtida distributioner orsakar oavsiktliga skalningsåtgärder. I allmänhet bör du använda automatisk skalning om:

* Distribuera dina Resource Manager-mallar med rätt deklarerad kapacitet stöder inte ditt användningsfall.
     
   Förutom manuell skalning kan du konfigurera en [pipeline för kontinuerlig integrering och leverans i Azure DevOps Services med hjälp av Azure-resursgruppsdistributionsprojekt](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Den här pipelinen utlöses ofta av en logikapp som använder prestandamått för virtuella datorer som efterfrågas från [AZURE Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Pipelinen skalar effektivt automatiskt baserat på vilka mått du vill ha, samtidigt som du optimerar för Resource Manager-mallar.
* Du behöver vågrätt bara skala en nod för skalning av en virtuell dator i taget.
   
   Om du vill skala ut med tre eller fler noder åt gången bör du [skala ut ett Service Fabric-kluster genom att lägga till en skalningsuppsättning för den virtuella datorn](virtual-machine-scale-set-scale-node-type-scale-out.md). Det är säkrast att skala in och skala ut skala av virtuella datorer horisontellt, en nod i taget.
* Du har Silver-tillförlitlighet eller högre för ditt Service Fabric-kluster och Silver hållbarhet eller högre på alla skalor där du konfigurerar regler för automatisk skalning.
  
   Minimikapaciteten för regler för automatisk skalning måste vara lika med eller större än fem instanser för virtuella datorer. Den måste också vara lika med eller större än minimum på tillförlitlighetsnivå för din primära nodtyp.

> [!NOTE]
> Service Fabric-tillståndskänslig serviceinfrastruktur:/System/InfastructureService/<NODE_TYPE_NAME> körs på alla nodtyper som har Silver eller högre hållbarhet. Det är den enda systemtjänsten som stöds för att köras i Azure på någon av dina klusternodtyper.

## <a name="vertical-scaling-considerations"></a>Hänsyn till lodrät skalning

[Vertikal skalning av](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) en nodtyp i Azure Service Fabric kräver ett antal steg och överväganden. Ett exempel:

* Klustret måste vara felfritt innan skalning skalas. Annars destabiliserar du klustret ytterligare.
* Silver hållbarhetsnivå eller högre krävs för alla Service Fabric klusternodtyper som är värd för tillståndskänsliga tjänster.

> [!NOTE]
> Din primära nodtyp som är värd för tillståndskänsliga Service Fabric-systemtjänster måste vara Silver hållbarhetsnivå eller större. När du har aktiverat Silver hållbarhet, klusteråtgärder som uppgraderingar, lägga till eller ta bort noder, och så vidare kommer att vara långsammare eftersom systemet optimerar för datasäkerhet över hastighet av åtgärder.

Vertikal skalning av en skalningsuppsättning för virtuella datorer är en destruktiv åtgärd. Skala i stället klustret vågrätt genom att lägga till en ny skaluppsättning med önskad SKU. Migrera sedan dina tjänster till önskad SKU för att slutföra en säker vertikal skalningsåtgärd. Att ändra en virtuell datorskalauppsättningsresurs SKU är en destruktiv åtgärd eftersom den återskapar värdarna, vilket tar bort alla lokalt beständiga tillstånd.

Klustret använder egenskaper för service [fabric-nod och placeringsbegränsningar](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) för att bestämma var programmets tjänster ska vara värd. När du skalar din primära nodtyp lodlinje `"nodeTypeRef"`deklarerar du identiska egenskapsvärden för . Du hittar dessa värden i tillägget Service Fabric för skaluppsättningar för virtuella datorer. 

Följande kodavsnitt i en Resource Manager-mall visar de egenskaper som du ska deklarera. Den har samma värde för de nyligen etablerade skalningsuppsättningar som du skalar till, och det stöds endast som en tillfällig tillståndskänslig tjänst för klustret.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Lämna inte klustret igång med flera skalningsuppsättningar som använder samma `nodeTypeRef` egenskapsvärde längre än vad som krävs för att slutföra en lyckad vertikal skalningsåtgärd.
>
> Verifiera alltid åtgärder i testmiljöer innan du försöker ändra produktionsmiljön. Som standard har Service Fabric-klustersystemtjänster en placeringsbegränsning till endast måltypen primär nod.

När nodegenskaperna och placeringsbegränsningarna har deklarerats gör du följande steg en VM-instans i taget. Detta gör att systemtjänsterna (och dina tillståndskänsliga tjänster) kan stängas av på ett smidigt sätt på den VM-instans som du tar bort när nya repliker skapas någon annanstans.

1. Från PowerShell `Disable-ServiceFabricNode` kör `RemoveNode` du med avsikt att inaktivera noden som du ska ta bort. Ta bort nodtypen som har det högsta talet. Om du till exempel har ett kluster med sex noder tar du bort förekomsten "MyNodeType_5"-virtuell dator.
2. Kör `Get-ServiceFabricNode` för att kontrollera att noden har övergått till inaktiverad. Om inte, vänta tills noden är inaktiverad. Detta kan ta ett par timmar för varje nod. Fortsätt inte förrän noden har övergått till inaktiverad.
3. Minska antalet virtuella datorer med en i nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till och med 3 efter behov, men skala aldrig ned antalet instanser i de primära nodtyperna mindre än vad tillförlitlighetsnivån motiverar. Se [Planera klusterkapaciteten för service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.
5. När alla virtuella datorer är borta (representeras som "Ned") visas ett feltillstånd för infrastrukturen:/System/InfrastructureService/[nodnamn). Sedan kan du uppdatera klusterresursen för att ta bort nodtypen. Du kan antingen använda ARM-malldistributionen eller redigera klusterresursen via [Azure Resource Manager](https://resources.azure.com). Detta startar en klusteruppgradering som tar bort den infrastruktur/System/InfrastructureService/[nodtyp] som är i feltillstånd.
 6. Efter att du kan eventuellt ta bort VMScaleSet, kommer du fortfarande se noderna som "Down" från Service Fabric Explorer-vyn ändå. Det sista steget skulle vara `Remove-ServiceFabricNodeState` att rensa upp dem med kommandot.

## <a name="horizontal-scaling"></a>Horisontell skalning

Du kan göra vågrät skalning antingen [manuellt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) eller [programmässigt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Om du skalar en nodtyp med hållbarhet i Silver eller Guld blir skalningen långsam.

### <a name="scaling-out"></a>Skala ut

Skala ut ett Service Fabric-kluster genom att öka instansantalet för en viss skalauppsättning för virtuella datorer. Du kan skala ut programmässigt med hjälp `AzureClient` av och ID för önskad skala inställd för att öka kapaciteten.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Om du vill skala ut manuellt uppdaterar du kapaciteten i egenskapen SKU för önskad resurs [för skala för virtuell dator.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skala in

Att skala in kräver mer hänsyn än skalning ut. Till exempel:

* Service Fabric-systemtjänster körs i den primära nodtypen i klustret. Stäng aldrig av eller skala ned antalet instanser för den nodtypen så att du har färre instanser än vad tillförlitlighetsnivån motiverar. 
* För en tillståndskänslig tjänst behöver du ett visst antal noder som alltid är upp för att upprätthålla tillgänglighet och bevara tillståndet för din tjänst. Du behöver åtminstone ett antal noder som är lika med antalet målreplikuppsättningar för partitionen eller tjänsten.

Så här skalar du in manuellt:

1. Från PowerShell `Disable-ServiceFabricNode` kör `RemoveNode` du med avsikt att inaktivera noden som du ska ta bort. Ta bort nodtypen som har det högsta talet. Om du till exempel har ett kluster med sex noder tar du bort förekomsten "MyNodeType_5"-virtuell dator.
2. Kör `Get-ServiceFabricNode` för att kontrollera att noden har övergått till inaktiverad. Om inte, vänta tills noden är inaktiverad. Detta kan ta ett par timmar för varje nod. Fortsätt inte förrän noden har övergått till inaktiverad.
3. Minska antalet virtuella datorer med en i nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till och med 3 efter behov tills du etablerar den kapacitet du vill ha. Skala inte ned antalet instanser i de primära nodtyperna till mindre än vad tillförlitlighetsnivån motiverar. Se [Planera klusterkapaciteten för service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.

Om du vill skala in manuellt uppdaterar du kapaciteten i egenskapen SKU för önskad resurs [för skala för virtuell dator.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Du måste förbereda noden för avstängning för att skalas in programmässigt. Leta reda på den nod som ska tas bort (noden med högst instans). Ett exempel:

```csharp
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

Inaktivera och ta bort noden med `FabricClient` samma`client` instans (i det här`instanceIdString` fallet) och nodinstansen (i det här fallet) som du använde i föregående kod:

```csharp
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
> När du skalar ned ett kluster visas den borttagna noden/VM-instansen som visas i feltillstånd i Service Fabric Explorer. En förklaring av det här beteendet finns [i Beteenden som du kan observera i Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Du kan:
> * Anropa [kommandot Ta bort ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) med lämpligt nodnamn.
> * Distribuera [hjälpprogrammet för automatisk skalning av Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) i klustret. Det här programmet säkerställer att de nedskalade noderna rensas från Service Fabric Explorer.

## <a name="reliability-levels"></a>Tillförlitlighetsnivåer

[Tillförlitlighetsnivån](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) är en egenskap för din Service Fabric-klusterresurs. Det kan inte konfigureras på olika sätt för enskilda nodtyper. Den styr replikeringsfaktorn för systemtjänsterna för klustret och är en inställning på klusterresursnivå. 

Tillförlitlighetsnivån avgör det minsta antalet noder som den primära nodtypen måste ha. Tillförlitlighetsnivån kan ta följande värden:

* Platina: Kör systemtjänsterna med ett antal målreplikuppsättningar på sju och nio frönoder.
* Guld: Kör systemtjänsterna med ett antal målreplikuppsättningar på sju och sju frönoder.
* Silver: Kör systemtjänsterna med antalet målreplikuppsättningar på fem och fem frönoder.
* Brons: Kör systemtjänsterna med ett antal målreplikuppsättningar på tre och tre frönoder.

Den lägsta rekommenderade tillförlitlighetsnivån är Silver.

Tillförlitlighetsnivån anges i egenskapsavsnittet i [Microsoft.ServiceFabric/clusters-resursen,](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)så här:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Hållbarhetsnivåer

> [!WARNING]
> Nodtyper som körs med bronshållbarhet får _inga privilegier_. Infrastrukturjobb som påverkar dina tillståndslösa arbetsbelastningar stoppas eller försenas inte, vilket kan påverka dina arbetsbelastningar. 
>
> Använd Brons hållbarhet endast för nodtyper som kör tillståndslösa arbetsbelastningar. För produktionsarbetsbelastningar kör du Silver eller högre för att säkerställa tillståndskonsekvens. Välj rätt tillförlitlighet baserat på vägledningen i [dokumentationen för kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Hållbarhetsnivån måste anges i två resurser. En är tilläggsprofilen för [den virtuella datorn skala uppsättning resurs:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

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

Den andra resursen finns under `nodeTypes` i [Microsoft.ServiceFabric/clusters-resursen:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric-klusterverktyget för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
