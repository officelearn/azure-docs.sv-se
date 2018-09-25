---
title: Azure Service Fabric programmässiga skalning | Microsoft Docs
description: Skala ett Azure Service Fabric-kluster in eller ut programmässigt, enligt anpassade utlösare
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ff02f79321823e42c25897e9de30dfbb6fac46b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949630"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Skala ett Service Fabric-kluster programmässigt 

Service Fabric-kluster som körs i Azure är byggt på VM-skalningsuppsättningar.  [Skalning av klustret](./service-fabric-cluster-scale-up-down.md) beskriver hur Service Fabric-kluster kan skalas antingen manuellt eller med regler för automatisk skalning. Den här artikeln beskriver hur du hanterar autentiseringsuppgifter och skala ett kluster i eller ut med fluent Azure SDK, som är ett mer avancerat scenario för beräkning. En översikt, läsa [programmeringsmetoder att samordna Azure skalningsåtgärder](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Hantera autentiseringsuppgifter
En utmaning att skriva en tjänst kan hantera skalning är att tjänsten måste kunna komma åt skalningsuppsättningsresurser för virtuell dator utan en interaktiv inloggning. Det är enkelt att komma åt Service Fabric-klustret om tjänsten skalning ändrar sin egen Service Fabric-program, men autentiseringsuppgifter krävs för att få åtkomst till skalningsuppsättningen. Du kan använda för att logga in en [tjänstens huvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) skapats med den [Azure CLI](https://github.com/azure/azure-cli).

Du kan skapa ett huvudnamn för tjänsten med följande steg:

1. Logga in på Azure CLI (`az login`) som en användare med åtkomst till VM-skalningsuppsättningen
2. Skapa tjänsten huvudnamn med `az ad sp create-for-rbac`
    1. Anteckna appId (kallas ”Klientid” någon annanstans), namn, lösenord och klient för senare användning.
    2. Du måste också ditt prenumerations-ID som kan visas med `az account list`

Fluent beräknings-biblioteket kan logga in med autentiseringsuppgifterna på följande sätt (Observera som core fluent Azure typer som `IAzure` finns i den [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) paketet):

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

När du loggat in kan skala instansantalet för set kan efterfrågas `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Skala ut
Använda fluent Azure compute SDK, instanser kan läggas till skalningsuppsättning för virtuell dator med bara några få anrop-

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Du kan också kan VM scale set-storlek också hanteras med PowerShell-cmdletar. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) Hämta skalningsuppsättningsobjekt för virtuell dator. Den aktuella kapaciteten är tillgänglig via den `.sku.capacity` egenskapen. När du har ändrat kapaciteten till önskat värde skalningsuppsättning för virtuell dator i Azure kan uppdateras med den [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) kommando.

När du lägger till en nod manuellt lägger till en skaluppsättningsinstans bör vara innehåller allt som behövs för att starta en ny Service Fabric-noden eftersom den skaluppsättningsmall tillägg att automatiskt ansluta nya instanser till Service Fabric-klustret. 

## <a name="scaling-in"></a>Skala in

Skala i liknar skala ut. Den faktiska VM-skalningsuppsättningen ändringar är praktiskt taget samma. Men som har diskuterats, Service Fabric endast rensas automatiskt borttagna noder med av guld eller Silver. Därför i Brons hållbarhet skala in fall, det är nödvändigt att interagera med Service Fabric-klustret för att stänga av noden tas bort och sedan ta bort dess tillstånd.

Förbereda noden för avstängning gäller att hitta noden ska tas bort (den nyligen tillagda VM scale set instansen) och inaktivera den. VM-skalningsuppsättningsinstanser numreras i den ordning som de har lagts till, så nyare noder kan hittas genom att jämföra siffersuffix i nodernas namn (som matchar den underliggande VM-skalningsuppsättningen instansnamn). 

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

När noden ska tas bort har hittats, det kan inaktiveras och tas bort med samma `FabricClient` instans och `IAzure` -instansen från tidigare.

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

Som med utskalning, PowerShell-cmdletar för att ändra VM-skalningsuppsättning kan kapacitet även användas här om en scripting metod är att föredra. När den virtuella datorinstansen har tagits bort, kan Service Fabric nodens tillstånd tas bort.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Nästa steg

Kom igång med att implementera din egen logik för automatisk skalning genom att bekanta dig med följande begrepp och användbara API: er:

- [Skala manuellt eller med regler för automatisk skalning](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure Management Libraries för .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (användbart för att interagera med Service Fabric-kluster underliggande skalningsuppsättningar för virtuella datorer)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (användbart för att interagera med Service Fabric-kluster och dess noder)
