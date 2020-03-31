---
title: Skala upp en nodtyp för Azure Service Fabric
description: Lär dig hur du skalar ett Service Fabric-kluster genom att lägga till en skaluppsättning för virtuella datorer.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464221"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skala upp en primär nodtyp för Service Fabric-klustret
I den här artikeln beskrivs hur du skalar upp en primär nodtyp för service fabric-kluster genom att öka resurserna för den virtuella datorn. Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas en nod. Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster [ställs in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala en klusternodtyp lodrätt (ändra nodernas resurser) eller uppgradera operativsystemet för de virtuella datorerna för nodtyp.  Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.  När klustret skalas skalas även dina program automatiskt.

> [!WARNING]
> Börja inte ändra den primära nodetypen VM SKU, om klusterhälsan är fel. Om klusterhälsan är felaktig destabiliserar du bara klustret ytterligare om du försöker ändra VM SKU.
>
> Vi rekommenderar att du inte ändrar VM SKU för en skalningsuppsättning/nodtyp om den inte körs vid [Silver hållbarhet eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändra VM SKU Storlek är en datadestruktiv på plats infrastruktur drift. Utan viss möjlighet att fördröja eller övervaka den här ändringen är det möjligt att åtgärden kan orsaka dataförlust för tillståndskänsliga tjänster eller orsaka andra oförutsedda driftproblem, även för tillståndslösa arbetsbelastningar. Det innebär att din primära nodtyp, som kör tillståndskänsliga tjänstinfrastruktursystemtjänster, eller någon nodtyp som kör dina tillståndskänsliga programarbetsbelastningar.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uppgradera storlek och operativsystem för virtuella datorer av primär nodtyp
Här är processen för att uppdatera vm-storlek och operativsystem för den primära nodtypen virtuella datorer.  Efter uppgraderingen är virtuella datorer av den primära nodtypen storlek standard D4_V2 och kör Windows Server 2016 Datacenter med behållare.

> [!WARNING]
> Innan du försöker med den här proceduren i ett produktionskluster rekommenderar vi att du studerar exempelmallarna och verifierar processen mot ett testkluster. Klustret är inte heller tillgängligt under en tid. Du kan INTE göra ändringar i flera VMSS som deklarerats som samma NodeType parallellt. Du måste utföra separerade distributionsåtgärder för att tillämpa ändringar på varje NodeType VMSS individuellt.

1. Distribuera det första klustret med två nodtyper och två skalningsuppsättningar (en skalningsuppsättning per nodtyp) med hjälp av dessa [exempelmall och](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) [parameterfiler.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Båda skalningsuppsättningarna är storleksstandard D2_V2 och windows server 2012 R2-datacenter.  Vänta tills klustret har slutfört baslinjeuppgraderingen.   
2. Valfritt- distribuera ett tillståndskänsligt exempel till klustret.
3. När du har bestämt dig för att uppgradera de primära nodtypen virtuella datorer lägger du till en ny skaluppsättning till den primära nodtypen med hjälp av dessa [exempelmall och](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) [parameterfiler](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) så att den primära nodtypen nu har två skalningsuppsättningar.  Systemtjänster och användarprogram kan migrera mellan virtuella datorer i de två olika skalningsuppsättningarna.  De nya skaluppsättningen virtuella datorer är storleksstandard D4_V2 och kör Windows Server 2016 Datacenter med behållare.  En ny belastningsutjämnare och offentlig IP-adress läggs också till med den nya skalningsuppsättningen.  
    Om du vill hitta den nya skalningsuppsättningen i mallen söker du efter resursen "Microsoft.Compute/virtualMachineScaleSets" som namnges av parametern *vmNodeType2Name.*  Den nya skaluppsättningen läggs till i den primära nodtypen med inställningen egenskaper >virtualMachineProfile->->->->egenskaper->inställningar->nodeTypeRef-inställningen.
4. Kontrollera klustrets hälsa och kontrollera att alla noder är felfria.
5. Inaktivera noderna i den gamla skaluppsättningen för den primära nodtypen med avsikt att ta bort noden. Du kan inaktivera alla på en gång och åtgärderna står i kö. Vänta tills alla noder är inaktiverade, vilket kan ta lite tid.  När de äldre noderna i nodtypen är inaktiverade migrerar systemtjänsterna och frönoderna till de virtuella datorerna i den nya skalan som angetts i den primära nodtypen.
6. Ta bort den äldre skaluppsättningen från den primära nodtypen.
7. Ta bort belastningsutjämnaren som är associerad med den gamla skaluppsättningen. Klustret är inte tillgängligt medan den nya offentliga IP-adressen och belastningsutjämnaren är konfigurerade för den nya skalningsuppsättningen.  
8. Lagra DNS-inställningar för den offentliga IP-adress som är associerad med den gamla primära nodtypsskalan som angetts i en variabel och ta bort den offentliga IP-adressen.
9. Ersätt DNS-inställningarna för den offentliga IP-adress som är associerad med den nya primära nodtypsskalan som har angetts med DNS-inställningarna för den borttagna offentliga IP-adressen.  Klustret kan nu nås igen.
10. Ta bort nodtillståndet för noderna från klustret.  Om hållbarhetsnivån för den gamla skalningsuppsättningen var silver eller guld, görs detta steg automatiskt av systemet.
11. Om du har distribuerat det tillståndskänsliga programmet i ett tidigare steg kontrollerar du att programmet fungerar.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
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
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
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
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

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
* Lär dig hur du [lägger till en nodtyp i ett kluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Läs mer om [programskalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster programmässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av den flytande Azure-beräkningSDK.Scale an Azure cluster programmematically using the fluent Azure compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).

