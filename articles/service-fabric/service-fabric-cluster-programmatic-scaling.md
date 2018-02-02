---
title: "Azure Service Fabric programmässiga skalning | Microsoft Docs"
description: "Skala ett Azure Service Fabric-kluster in eller ut programmässigt, enligt anpassade utlösare"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bfa020e29a9bb67f0634d220725bc11279e1565c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Skala Service Fabric-klustret via programmering 

Grunderna i skala ett Service Fabric-kluster i Azure beskrivs i dokumentationen på [skalning av klustret](./service-fabric-cluster-scale-up-down.md). Artikeln beskriver hur Service Fabric-kluster är byggda på virtuella datorer och kan skalas manuellt eller med Autoskala regler. Det här dokumentet tittar på programmering koordinerande Azure skalning åtgärder för mer avancerade scenarier. 

## <a name="reasons-for-programmatic-scaling"></a>Orsaker till programmässiga skalning
I många fall är är skalning manuellt eller via Autoskala regler bra lösningar. I andra scenarier, men kanske de inte rätt storlek. Eventuella nackdelar med dessa metoder är:

- Skalning manuellt måste du logga in och uttryckligen begära skalning åtgärder. Om skalning åtgärder krävs ofta eller vid oväntade tidpunkter, kanske inte en bra lösning i den här metoden.
- När Autoskala regler för att ta bort en instans från en skaluppsättning för virtuell dator, de inte bort automatiskt kunskap om noden från det associera Service Fabric-klustret såvida inte nodtypen har en hållbarhet Silver eller guld. Eftersom Autoskala regler fungerar i skala anger (istället för på Service Fabric-nivå), Autoskala regler kan ta bort Service Fabric-noder utan att dem avslutas. Borttagningen oartigt nod lämnar 'ghost-tillstånd för Service Fabric-noden efter efter skala i operations. En person (eller en tjänst) skulle behöva regelbundet Rensa borttagna noden tillstånd i Service Fabric-klustret.
  - En nodtyp med hållbarhet guld eller Silver rensas automatiskt bort noder, så det behövs ingen ytterligare rensning.
- Även om det finns [många mått](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) stöds av Autoskala regler, är det fortfarande en begränsad uppsättning. Om ditt scenario anrop för att skala baserat på vissa mått som inte omfattas i uppsättningen kan sedan kanske Autoskala regler inte ett bra alternativ.

Baserat på dessa begränsningar, kan du implementera flera anpassade automatisk skalning modeller. 

## <a name="scaling-apis"></a>Skalning API: er
Azure API: er finns som tillåter program programmässigt arbetar med virtuella skala uppsättningar och Service Fabric-kluster. Om den befintliga Autoskala alternativ inte fungerar för ditt scenario, gör dessa API: er det möjligt att implementera anpassade skalning logik. 

En metod för att implementera funktionen ”home tillverkade” automatisk skalning är att lägga till en ny tillståndslösa tjänsten Service Fabric-programmet för att hantera skalning åtgärder. I tjänstens `RunAsync` metod, en uppsättning utlösare kan avgöra om skalning krävs (inklusive kontrollerar parametrar, till exempel klustrets maximala storlek och skala cooldowns).   

API: et för virtuella scale set interaktioner (både att kontrollera det aktuella antalet instanser för virtuella datorer och ändra den) är den [flytande Azure Management Compute biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Flytande beräknings-bibliotek innehåller en lätt att använda API för att interagera med virtuella datorer.

Om du vill interagera med själva Service Fabric-klustret använder [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Naturligtvis behöver skalning koden inte köras som en tjänst i klustret för att skalas. Båda `IAzure` och `FabricClient` kan fjärransluta till deras associerade Azure-resurser, så att tjänsten skalning enkelt kan vara ett konsolprogram eller Windows-tjänsten körs från utanför Service Fabric-programmet. 

## <a name="credential-management"></a>Hantering av autentiseringsuppgifter
En utmaning för att skriva en tjänst att hantera skalning är att tjänsten måste kunna komma åt virtuella skala uppsättning resurser utan interaktiv inloggning. Det är enkelt att åtkomst till Service Fabric-klustret om tjänsten skalning ändrar sin egen Service Fabric-program, men autentiseringsuppgifter krävs för att komma åt skaluppsättning. Du kan använda för att logga in en [tjänstens huvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) skapats med den [Azure CLI 2.0](https://github.com/azure/azure-cli).

Ett huvudnamn för tjänsten kan skapas med följande steg:

1. Logga in på Azure CLI (`az login`) som en användare med åtkomst till virtuella datorns skaluppsättning anger
2. Skapa tjänsten huvudnamn med`az ad sp create-for-rbac`
    1. Anteckna appId (kallas klient-ID någon annanstans), namn, lösenord och klient för senare användning.
    2. Du måste också ditt prenumerations-ID som kan visas med`az account list`

Flytande beräknings-biblioteket kan logga in med autentiseringsuppgifterna enligt följande (Observera att core flytande Azure typer som `IAzure` finns i den [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) paketet):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

När du loggade in scale set-instanser kan efterfrågas `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Skala ut
Med flytande Azure compute SDK, instanser kan läggas till skaluppsättningen för virtuell dator med bara några få anrop-

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Du kan också kan virtuella skala storlek också hanteras med PowerShell-cmdlets. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)Hämta virtuella Skala uppsättningsobjekt. Den aktuella kapaciteten är tillgänglig via den `.sku.capacity` egenskapen. När du har ändrat kapacitet till önskat värde skaluppsättningen för virtuell dator i Azure kan uppdateras med den [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) kommando.

Som när du lägger till en nod manuellt lägga till en skaluppsättning för instansen måste vara innehåller allt som behövs för att starta en ny Service Fabric-nod eftersom mallen skaluppsättning tillägg för att automatiskt ansluta nya instanser till Service Fabric-klustret. 

## <a name="scaling-in"></a>Skalning i

Skalning i liknar skala ut. Den faktiska virtuella skaluppsättning ändringar är praktiskt taget samma. Men som har diskuterats tidigare, Service Fabric endast rensas automatiskt bort noder med en hållbarhet guld eller Silver. Så i Brons hållbarhet skala i fallet är det nödvändigt att interagera med Service Fabric-klustret för att stänga av noden som ska tas bort och sedan ta bort dess tillstånd.

Förbereda noden för avstängning gäller att hitta noden ska tas bort (den nyligen tillagda virtuella scale set instansen) av och inaktivera den. Virtual machine scale set instanser numreras i den ordning som de har lagts till, så nyare noder kan hittas genom att jämföra siffersuffix i de noder namn (som matchar den underliggande virtuella datorns skaluppsättning ange instansnamn). 

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

När noden ska tas bort finns, den kan inaktiveras och tas bort med hjälp av samma `FabricClient` instans och `IAzure` instansen från tidigare.

```csharp
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

Som med skala ut PowerShell-cmdlets för att ändra virtuella datorn kan set kapacitet även användas här om en scripting metod är att föredra. När den virtuella instansen har tagits bort, kan tas bort Service Fabric nodens tillstånd.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Nackdelarna

Som visas i föregående kodfragment ger skapar egna skalning service största möjliga kontroll och anpassningsbarheten över ditt program är skalning beteende. Detta kan vara användbart för scenarier som kräver exakt kontroll över när eller hur ett program skalas in eller ut. Den här kontrollen har dock en kompromiss förenklar koden. Med den här metoden innebär att du behöver egna skalning kod, som är icke-trivial.

Hur ska du bör Service Fabric skalning beror på ditt scenario. Om det är ovanligt att skalning, räcker troligen möjligheten att lägga till eller ta bort noder manuellt. För mer komplicerade scenarier erbjuder Autoskala regler och SDK exponera möjlighet att skala programmässigt kraftfulla alternativ.

## <a name="next-steps"></a>Nästa steg

Kom igång med att implementera din egen automatisk skalning logik genom att bekanta dig med följande begrepp och användbara API: er:

- [Skalning manuellt eller med Autoskala regler](./service-fabric-cluster-scale-up-down.md)
- [Flytande Azure-bibliotek för .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (användbart för att interagera med Service Fabric-klustret underliggande skalningsuppsättningar i virtuella datorer)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (användbart för att interagera med ett Service Fabric-kluster och noder)
