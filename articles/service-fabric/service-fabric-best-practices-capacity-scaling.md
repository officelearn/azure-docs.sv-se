---
title: Azure Service Fabric-kapacitetsplanering och skalning Metodtips | Microsoft Docs
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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471544"
---
# <a name="capacity-planning-and-scaling"></a>Kapacitetsplanering och skalning

Innan du skapar ett Azure Service Fabric-kluster eller skala beräkningsresurser som är värd för klustret, är det viktigt att planera kapaciteten. Läs mer om hur du planerar kapaciteten [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Planera för skalningsåtgärder tar längre tid än en timme att slutföra för en produktionsmiljö, oavsett hur många virtuella datorer som du lägger till förutom överväger egenskaper Nodetype och kluster.

## <a name="auto-scaling"></a>Automatisk skalning
Skalning ska utföras via Azure Resource malldistributionen, eftersom det är en bra idé att behandla [resurskonfigurationer som kod]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code), och använder Virtual Machine Scale Sets autoskalning resulterar i din Ange antalet instanser; för version Resource Manager-mall som felaktigt definiera VM-skalningsuppsättning öka risken för framtida distributioner som orsakar oönskade skalningsåtgärder och i allmänhet bör du använda automatisk skalning om:

* Distribuera dina Resource Manager-mallar med rätt kapacitet som deklarerats stöder inte ditt användningsområde.
  * Manuell skalning kan du konfigurera en [kontinuerlig integrering och leverans Pipeline i Azure DevOps-tjänster med projekt för distribution av Azure-resursgrupp]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), som utlöses av en Logikapp som utnyttjar ofta prestandamått för virtuella datorer som efterfrågas från [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); effektivt automatisk skalning baserat på måtten du vill, under optimering för Azure Resource Manager lägger till värdet.
* Du behöver bara att vågrätt skala 1 VM scale set-nod i taget.
  * Att skala ut genom 3 eller fler noder i taget, bör du [skala ut ett Service Fabric-kluster genom att lägga till en Virtual Machine Scale Sets](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), och den är säkrast att skala in och ut virtuella datorer anger du vågrätt 1 nod i taget.
* Du har på Silver tillförlitlighet eller högre för Service Fabric-kluster och Silver hållbarhet eller senare på valfri skala Set du konfigurera regler för automatisk skalning.
  * Automatisk skalning regler kapacitet [minsta] måste vara lika med eller större än 5 instanser av virtuella datorer och måste vara lika med eller större än din tillförlitlighetsnivån minimum för den primära nodtypen.

> [!NOTE]
> Azure Service Fabric tillståndskänslig service fabric: / System/InfastructureService/< NODE_TYPE_NAME > körs på varje nodtyp som har Silver eller högre tillförlitlighet, vilket är den enda systemtjänst som går för att köra i Azure på någon av dina kluster nodtyper . 

## <a name="vertical-scaling-considerations"></a>Lodrät skalning överväganden

[Vertikal skalning](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) en nodtyp i Azure Service Fabric kräver ett antal åtgärder och överväganden. Exempel:
* Klustret måste vara felfria innan du skalar. Annars du kommer endast att göra instabil ytterligare kluster.
* **Silver hållbarhet nivå eller högre** krävs för alla Service Fabric-kluster NodeTypes som är värdar för tillståndskänsliga tjänster.

> [!NOTE]
> Din primära NodeType som är värd för tillståndskänslig Service Fabric-systemtjänster måste vara Silver hållbarhet nivå eller högre. När du har aktiverat silver hållbarhet klusteråtgärder, till exempel uppgraderingar, blir att lägga till eller ta bort noder och så vidare långsammare eftersom systemet optimerar för datasäkerheten över hastigheten på åtgärder.

Lodrät skalning en Virtual Machine Scale Sets är en destruktiv åtgärd. I stället vågrätt skala ditt kluster genom att lägga till en ny Skalningsuppsättning med önskad SKU och migrera dina tjänster till din önskade SKU för att slutföra en säker lodrät skalning åtgärd. Ändra en Virtual Machine Scale Sets-resurs SKU är en destruktiv åtgärd eftersom den skapar ny avbildning av värdarna som tar bort alla lokalt sparade tillstånd.

Service Fabric [nodegenskaper och placeringsbegränsningar](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) används av klustret för att bestämma var ska vara värd för dina program-tjänster. Lodrätt skalning av den primära nodtypen när deklarera identiska egenskapsvärden för `"nodeTypeRef"`, som finns i Virtual Machine Scale ställa in Service Fabric-tillägget. Resource Manager-mall följande kodavsnitt visar egenskaperna du deklarerar, med samma värde för din nya etablerade skalningsuppsättningar som du skalar till och stöds endast som en tillfällig tillståndskänslig för klustret:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Lämna inte ditt kluster som körs med flera skalningsuppsättningar som använder samma `nodeTypeRef` egenskapsvärde som är längre än krävs för att slutföra en lyckad åtgärd för lodrät skalning.
> Verifiera alltid åtgärder i testmiljöer innan du försöker miljöändringar för produktion. Som standard har Service Fabric-kluster-systemtjänster villkoret placering för vilka primära nodetype ska tillämpas.

Med den nodegenskaper och placeringsbegränsningar som deklarerats, gör du följande steg för en VM-instansen i taget. Detta gör systemtjänster (och dina tillståndskänsliga tjänster) kan stängas av smidigt på VM-instans som du tar bort då nya repliker skapas någon annanstans.
1. Från PowerShell, kör `Disable-ServiceFabricNode` med avsikt 'RemoveNode' att inaktivera noden ska du ta bort. Ta bort nodtyp som har det högsta antalet. Till exempel om du har ett kluster med sex noder tar du bort ”MyNodeType_5” VM-instans.
2. Kör `Get-ServiceFabricNode` att se till att noden har gått över till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Det kan ta ett par timmar för varje nod. Inte fortsätta förrän noden har gått över till inaktiverad.
3. Minska antalet virtuella datorer med en i den nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära nodtyperna mindre än tillförlitlighetsnivån kräver. Se [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.

## <a name="horizontal-scaling"></a>Horisontell skalning

Horisontell skalning i Service Fabric kan göras antingen [manuellt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) eller [programmässigt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Om du arbetar för att bygga en nodtyp som har silver eller gold hållbarhet, vara skalning långsam.

### <a name="scaling-out"></a>Skala ut

Skala ut ett Service Fabric-kluster genom att öka instansantalet för en viss Virtual Machine Scale Sets. Du kan skala ut via programmering med hjälp av AzureClient och ID för önskad skalningsuppsättningen att öka kapaciteten.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Om du vill skala ut manuellt uppdatera kapaciteten i SKU-egenskapen för den önskade [Virtual Machine Scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) resurs.
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
* För en tillståndskänslig tjänst behöver du ett visst antal noder som alltid är igång för att bibehålla tillgänglighet och bevara tillståndet för din tjänst. Minst behöver du ett antal noder lika target set replikantalet för partitionen/tjänsten.

Följ dessa steg om du vill skala i manuellt:

1. Från PowerShell, kör `Disable-ServiceFabricNode` med avsikt 'RemoveNode' att inaktivera noden ska du ta bort. Ta bort nodtyp som har det högsta antalet. Till exempel om du har ett kluster med sex noder tar du bort ”MyNodeType_5” VM-instans.
2. Kör `Get-ServiceFabricNode` att se till att noden har gått över till inaktiverad. Annars kan du vänta tills noden är inaktiverad. Det kan ta ett par timmar för varje nod. Inte fortsätta förrän noden har gått över till inaktiverad.
3. Minska antalet virtuella datorer med en i den nodtypen. Den högsta VM-instansen tas nu bort.
4. Upprepa steg 1 till 3 efter behov, men aldrig skala ned antalet instanser i de primära nodtyperna mindre än tillförlitlighetsnivån kräver. Se [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) för en lista över rekommenderade instanser.

Om du vill skala i manuellt uppdatera kapaciteten i SKU-egenskapen för den önskade [Virtual Machine Scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) resurs.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Upprepa steg 1 till 3 tills du etablerar den kapacitet som du vill. Inte skala ned antalet instanser i de primära nodtyperna till färre än tillförlitlighetsnivån kräver. Mer information om nivåerna för tillförlitlighet och antalet instanser som användaren måste referera till den [planera kapacitet för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Du måste förbereda noden för nedstängning för att skala i programmässigt. Detta innebär att hitta noden ska tas bort som är den högsta instans-noden och inaktivera den. Exempel:

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

När du har identifierat noden för att ta bort, inaktivera och ta bort den med samma `FabricClient` instans (`client` i det här fallet) och nod-Instansnamn (`instanceIdString` i det här fallet) du använde i koden ovan:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> När du skalar ser ett kluster problem kan du den borttagna nod och VM-instans som visas i ett feltillstånd i Service Fabric Explorer. En förklaring av det här problemet finns i [beteenden som du kan se i Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer).
> 
> Du kan:
> * Anropa [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) med namnet på lämplig noden.
> * Distribuera [service fabric Autoskala hjälpprogram](https://github.com/Azure/service-fabric-autoscale-helper/) i ditt kluster, vilket garanterar den skalats ned noder tas bort från Service Fabric Explorer.

## <a name="reliability-levels"></a>Tillförlitlighetsnivåer

Den [tillförlitlighetsnivån](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) är en egenskap för din resurs för Service Fabric-kluster och kan inte konfigureras på olika sätt för enskilda nodeTypes. Den styr replikeringsfaktorn systemtjänster för klustret och är en inställning på klusternivå för resursen. Tillförlitlighetsnivån avgör det minsta antalet noder som den primära nodtypen måste innehålla. Tillförlitlighetsnivån kan ha följande värden:
* Platina - körs systemtjänster med en target replica set uppräkning av sju och nio startvärdesnoder.
* Guld - körs systemtjänster med en target replica set uppräkning av sju och sju startvärdesnoder.
* Silver - körs systemtjänster med en target replica set uppräkning av fem och fem startvärdesnoder.
* Brons - körs systemtjänster med en target replica set uppräkning av tre och tre startvärdesnoder.

Den minsta rekommenderade tillförlitlighetsnivån är Silver.

Tillförlitlighetsnivån som anges i egenskapsavsnittet i den [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), så här:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Hållbarhet nivåer

> [!WARNING]
> Nodtyper som körs med Brons hållbarhet hämta _saknad behörighet_. Det innebär att infrastruktur för jobb som påverkar din tillståndslösa arbetsbelastningar inte kommer stoppas eller fördröjd, vilket kan påverka dina arbetsbelastningar. Använd Brons hållbarhet enbart för nodtyper som kör tillståndslösa arbetsbelastningar. För arbetsbelastningar för produktion, kör du Silver eller senare för att garantera konsekvens tillstånd. Välj rätt tillförlitligheten baserat på vägledningen i det [dokumentation för kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Hållbarhetsnivå måste ställas in i två resurser. Tilläggsprofilen för den [Virtual Machine Scale Sets resource](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

Och under `nodeTypes` i den [Microsoft.ServiceFabric/clusters resurs](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
