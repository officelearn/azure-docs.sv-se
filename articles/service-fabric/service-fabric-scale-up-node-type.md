---
title: Skala upp ett Azure Service Fabric-nodtyp | Microsoft Docs
description: Lär dig hur du skalar ett Service Fabric-kluster genom att lägga till en Virtual Machine Scale Sets.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: ryanwi
ms.openlocfilehash: 7c90556916f86f58fa479f9f14b03a90e6405d2b
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302709"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skala upp en Service Fabric-kluster primära nodtypen
Den här artikeln beskriver hur du skalar upp ett Service Fabric-kluster primära nodtypen genom att öka virtuella datorresurser. Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Virtual machine scale sets är en Azure-beräkningsresurs som används för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [ställa in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster, kan du skala en klusternodstyp lodrätt (ändra resurser noder) eller uppgradera operativsystemet på nodtyp virtuella datorer.  Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.  När klustret skalas skalas programmen automatiskt samt.

> [!WARNING]
> Starta inte att ändra den primära nodetype VM SKU, om hälsotillståndet för klustret är i feltillstånd. Om hälsotillståndet för klustret är i feltillstånd, du kommer endast göra instabil klustret ytterligare, om du försöker ändra VM-SKU.
>
> Vi rekommenderar att du inte ändrar VM-SKU på en skala set/nodtyp, såvida inte den körs på [Silver hållbarhet med eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra VM SKU-storlek är en data-destruktiv plats infrastruktur-åtgärd. Det är möjligt att det kan leda till dataförlust för tillståndskänsliga tjänster eller orsakar andra oförutsedda driftsproblem, även för tillståndslösa arbetsbelastningar utan några möjligheten att fördröja eller övervaka den här ändringen. Det innebär att den primära nodtypen som kör tillståndskänslig service fabric systemtjänster, eller valfri nodtyp av som kör ditt arbete med tillståndskänsliga program läses in.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uppgradera storlek och operativsystemet på den primära nodtypen virtuella datorer
Här är en process för att uppdatera VM-storleken och operativsystemet på den primära nodtypen virtuella datorer.  Efter uppgraderingen är den primära nodtypen VMs storlek Standard D4_V2 och som kör Windows Server 2016 Datacenter med behållare.

> [!WARNING]
> Innan du försöker den här proceduren på ett produktionskluster rekommenderar vi att du studera exempelmallarna och verifiera processen mot ett testkluster. Klustret är också tillgänglig under en tid. Du kan inte göra ändringar på flera VMSS deklarerats som samma NodeType parallellt; Du behöver utföra avgränsas distributionsåtgärder för att tillämpa ändringar på varje NodeType VMSS individuellt.

1. Distribuera det första klustret med två nodtyper och två skalningsuppsättningar (en skalningsuppsättning per nodtyp) med hjälp av dessa exempel [mall](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) filer.  Båda skalningsuppsättningar är storlek Standard D2_V2 och som kör Windows Server 2012 R2 Datacenter.  Vänta tills klustret för att slutföra uppgraderingen baslinje.   
2. Valfritt – distribuera en tillståndskänslig exemplet till klustret.
3. När du har bestämt att uppgradera den primära nodtypen virtuella datorer, lägger du till en ny skalningsuppsättning till den primära nodtypen med hjälp av dessa exempel [mall](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) filer så att den primära nodtypen har nu två skalningsuppsättningar.  Systemtjänster och program kan migrera mellan virtuella datorer i två olika skalningsuppsättningar.  Ny skalningsuppsättningen virtuella datorer är storleken Standard D4_V2 och köra Windows Server 2016 Datacenter med behållare.  En ny belastningsutjämnare och offentliga IP-adressen läggs till med den nya skaluppsättningen.  
    Du hittar nya skaluppsättningen i mallen, söka efter ”Microsoft.Compute/virtualMachineScaleSets” resursen med namnet genom att den *vmNodeType2Name* parametern.  Nya skalningsuppsättningen har lagts till i den primära noden typ med hjälp av Egenskaper -> virtualMachineProfile - > extensionProfile -> Tillägg -> Egenskaper -> Inställningar -> nodeTypeRef inställningen.
4. Kontrollera hälsotillståndet för klustret och alla noder är felfria.
5. Inaktivera noderna i gamla skalningsuppsättningen för den primära nodtypen med avsikt att ta bort noden. Du kan inaktivera alla samtidigt och åtgärder i kö. Vänta tills alla noder är inaktiverade, vilket kan ta lite tid.  Eftersom äldre noderna i nodtyp är inaktiverade, migrera tjänster och seed noder för system till de virtuella datorerna för nya skalningsuppsättningen i den primära nodtypen.
6. Ta bort den äldre skalningsuppsättning från den primära nodtypen.
7. Ta bort belastningsutjämnaren som är associerade med den gamla skalningsuppsättningen. Klustret är inte tillgänglig medan den nya offentliga IP-adress och load balancer har konfigurerats för den nya skaluppsättningen.  
8. Store DNS-inställningarna för offentliga IP-adress som är associerade med den gamla primära noden skriver skalningsuppsättning i en variabel och ta bort den offentliga IP-adressen.
9. Ersätt DNS-inställningarna för offentliga IP-adress som är associerade med den nya primära noden typ skalningsuppsättning med DNS-inställningarna för den offentliga IP-adressen som har tagits bort.  Klustret kan nu nås igen.
10. Ta bort Nodtillstånd noder från klustret.  Om hållbarhetsnivå av gammal skalningsuppsättningen var silver eller gold, görs det här steget automatiskt av systemet.
11. Verifiera att programmet fungerar om du har distribuerat det tillståndskänsliga programmet i ett föregående steg.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägga till en nodtyp i ett kluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).

