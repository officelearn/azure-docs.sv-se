---
title: 'Uppgradera en Azure Service Fabric-kluster SKU: N för virtuella datorer eller OS | Microsoft Docs'
description: Lär dig hur du uppgraderar de virtuella datorerna i ett Service Fabric-kluster primära nodetype.
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
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: bf707bf4b1c001b5467dd9954e9c6feb55e65654
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655745"
---
# <a name="upgrade-the-primary-node-type-vms-of-a-service-fabric-cluster"></a>Uppgradera den primära nodtypen virtuella datorer i ett Service Fabric-kluster
Den här artikeln beskriver hur du uppgraderar de primära noden typen virtuella datorerna i ett Service Fabric-kluster som körs i Azure.  Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som din mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Skaluppsättningar för den virtuella datorn är en resurs i Azure-beräkning som används för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster är [har angetts som en separat skaluppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala en kluster-nodtyp lodrätt (ändra resurser noder) eller uppgradera operativsystemet på nodtypen virtuella datorer.  Du kan skala klustret när som helst, även om arbetsbelastningar som körs på klustret.  När klustret skalas skalas dina program automatiskt samt.

> [!WARNING]
> Vi rekommenderar att du inte ändrar den SKU VM scale set/typen om det körs på [Silver hållbarhet eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra storlek på VM-SKU är skadliga data på plats-infrastruktur. Det är möjligt att det kan leda till dataförlust tillståndskänsliga tjänster eller orsaka andra oförutsedda driftsproblem, även för tillståndslösa arbetsbelastningar utan några möjlighet att fördröja eller övervaka den här ändringen. 
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uppgradera operativsystemet på den primära nodtypen virtuella datorer och storlek
Här är en process för att uppdatera VM-storlek och operativsystem för den primära nodtypen virtuella datorer.  Efter uppgraderingen är den primära nodtypen VMs storlek Standard D4_V2 och kör Windows Server 2016 Datacenter med behållare.

> [!WARNING]
> Innan du försöker utföra den här proceduren på ett produktionskluster rekommenderar vi att du studera exempelmallarna och kontrollera processen mot ett testkluster. Klustret är också tillgänglig under en tid.

1. Distribuera det första klustret med två nodtyper och två skalningsuppsättningar (en skaluppsättningen per nodtypen) med hjälp av dessa exempel [mallen](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) filer.  Skaluppsättningar för båda är storlek Standard D2_V2 och kör Windows Server 2012 R2 Datacenter.  Vänta tills klustret för att slutföra uppgraderingen av baslinjen.   
2. Valfritt distribuerar ett exempel på en tillståndskänslig till klustret.
3. När du har bestämt att uppgradera den primära nodtypen virtuella datorer, lägger du till en ny skala inställd på den primära nodtypen med hjälp av dessa exempel [mallen](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) filer så att den primära nodtypen har nu två skalningsuppsättningar.  Systemtjänster och program kan migrera mellan virtuella datorer i två olika skaluppsättningar.  Den nya skaluppsättning för virtuella datorer som är Standard D4_V2 storlek och kör Windows Server 2016 Datacenter med behållare.  En ny belastningsutjämnare och en offentlig IP-adress läggs till med den nya skaluppsättning.  
    Du hittar den nya skalan som angetts i mallen, söka efter ”Microsoft.Compute/virtualMachineScaleSets” resursen med namnet av den *vmNodeType2Name* parameter.  Den nya skaluppsättning har lagts till i den primära noden typ med hjälp av egenskaperna > virtualMachineProfile - > extensionProfile -> Tillägg -> Egenskaper -> Inställningar -> nodeTypeRef inställningen.
4. Kontrollera hälsotillståndet för klustret och alla noder är felfria.
5. Inaktivera noderna i den gamla skaluppsättning för den primära nodtypen med avsikt att ta bort noden. Du kan inaktivera alla samtidigt och åtgärder i kö. Vänta tills alla noder har inaktiverats, vilket kan ta en stund.  Äldre noderna i nodtypen har inaktiverats migrera system tjänster och seed noderna till de virtuella datorerna i den nya skalan som angetts i den primära nodtypen.
6. Ta bort äldre skaluppsättningen från den primära nodtypen.
7. Ta bort belastningsutjämnaren som är associerade med den gamla skaluppsättning. Klustret är inte tillgänglig medan den nya offentliga IP-adress och läsa in belastningsutjämning har konfigurerats för den nya skaluppsättning.  
8. Store DNS-inställningarna för den offentliga IP-adressen som är associerade med den gamla primära noden skriver skalningsuppsättning i en variabel och ta bort den offentliga IP-adressen.
9. Ersätt DNS-inställningarna för den offentliga IP-adressen som är associerade med den nya primära nod typen skalan med DNS-inställningarna för den offentliga IP-adressen som tagits bort.  Klustret kan nu nås igen.
10. Ta bort Nodtillstånd noder från klustret.  Om hållbarhet den gamla skaluppsättning var silver eller guld, sker det här steget automatiskt av systemet.
11. Verifiera att programmet fungerar om du har distribuerat tillståndskänsliga program i föregående steg.

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
* Lär dig mer om [programmet skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med flytande Azure compute SDK.
* [Skala ett standaone kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).

