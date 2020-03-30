---
title: Programmatisk skalning av Azure Service Fabric
description: Skala ett Azure Service Fabric-kluster in eller ut programmässigt, enligt anpassade utlösare
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458280"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Skala ett service fabric-kluster programmässigt 

Service Fabric-kluster som körs i Azure är byggda ovanpå skalningsuppsättningar för virtuella datorer.  [Klusterskalning](./service-fabric-cluster-scale-up-down.md) beskriver hur Service Fabric-kluster kan skalas antingen manuellt eller med regler för automatisk skalning. I den här artikeln beskrivs hur du hanterar autentiseringsuppgifter och skalar ett kluster in eller ut med hjälp av den flytande Azure-beräkningSDK, vilket är ett mer avancerat scenario. En översikt finns i [programmatiska metoder för att samordna Azure-skalningsåtgärder](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Hantera autentiseringsuppgifter
En utmaning med att skriva en tjänst för att hantera skalning är att tjänsten måste kunna komma åt resurser för skala för virtuella datorer utan en interaktiv inloggning. Det är enkelt att komma åt Service Fabric-klustret om skalningstjänsten ändrar sitt eget Service Fabric-program, men autentiseringsuppgifter behövs för att komma åt skalningsuppsättningen. Om du vill logga in kan du använda ett [tjänsthuvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) som skapats med [Azure CLI](https://github.com/azure/azure-cli).

Ett huvudnamn för tjänsten kan skapas med följande steg:

1. Logga in på Azure`az login`CLI ( ) som användare med åtkomst till skalningsuppsättningen för den virtuella datorn
2. Skapa tjänstens huvudnamn med`az ad sp create-for-rbac`
    1. Anteckna appId (kallas "klient-ID" någon annanstans), namn, lösenord och klient för senare användning.
    2. Du behöver också ditt prenumerations-ID, som kan ses med`az account list`

Det flytande beräkningsbiblioteket kan logga in med dessa autentiseringsuppgifter på `IAzure` följande sätt (observera att grundläggande flytande Azure-typer som finns i [Microsoft.Azure.Management.Fluent-paketet):](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)

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

När du har loggat in kan antalet `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`skalerade instanser efterfrågas via .

## <a name="scaling-out"></a>Skala ut
Med hjälp av den flytande Azure-beräkningSDK kan instanser läggas till i skalan för den virtuella datorn med bara några få anrop -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativt kan storleken på skalningsuppsättningen för virtuella datorer också hanteras med PowerShell-cmdlets. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)kan hämta objektet för skalningsuppsättning för virtuell dator. Den aktuella kapaciteten `.sku.capacity` är tillgänglig via boendet. När du har ändrat kapaciteten till önskat värde kan den [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) virtuella datorns skalningsuppsättning i Azure uppdateras med kommandot.

Precis som när du lägger till en nod manuellt bör det vara allt som behövs för att starta en ny Service Fabric-nod, eftersom skalningsuppsättningsmallen innehåller tillägg för att automatiskt ansluta nya instanser till Service Fabric-klustret. 

## <a name="scaling-in"></a>Skala in

Att skala in liknar skalning ut. De faktiska ändringar av skalningsuppsättningen för virtuella datorer är praktiskt taget desamma. Men, som diskuterades tidigare, Städar Service Fabric bara automatiskt upp borttagna noder med en hållbarhet av guld eller silver. Så i brons-hållbarhet skala-in fall, det är nödvändigt att interagera med Service Fabric klustret för att stänga av noden som ska tas bort och sedan ta bort dess tillstånd.

Förbereda noden för avstängning innebär att hitta noden som ska tas bort (den senast tillagda virtuella datorn skala set instans) och inaktivera den. Skalan för virtuell dator numreras i den ordning de läggs till, så nyare noder kan hittas genom att jämföra talsuffixet i nodernas namn (som matchar de underliggande förekomstnamnen för den virtuella datorns skaluppsättning). 

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

När noden som ska tas bort har hittats kan den `FabricClient` inaktiveras `IAzure` och tas bort med samma instans och instansen från tidigare.

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

Precis som med skalning kan PowerShell-cmdlets för ändring av kapacitet för skalningsuppsättning för virtuella datorer också användas här om en skriptmetod är att föredra. När instansen för den virtuella datorn har tagits bort kan nodtillståndet Service Fabric tas bort.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med att implementera din egen logik för automatisk skalning kan du bekanta dig med följande begrepp och användbara API:er:

- [Skala manuellt eller med regler för automatisk skalning](./service-fabric-cluster-scale-up-down.md)
- [Flytande Azure Management-bibliotek för .NET (användbart](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) för att interagera med ett Service Fabric-klusters underliggande skaluppsättningar för virtuella datorer)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (användbart för att interagera med ett Service Fabric-kluster och dess noder)
