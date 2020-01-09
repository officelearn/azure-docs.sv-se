---
title: Skala upp en typ av Azure-Service Fabric Node
description: Lär dig hur du skalar ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464221"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skala upp en Service Fabric primär nodtyp för kluster
Den här artikeln beskriver hur du skalar upp en Service Fabric-klusters primära nodtyp genom att öka resurserna för virtuella datorer. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala en typ av klusternod lodrätt (ändra resurserna för noderna) eller uppgradera operativ systemet för de virtuella datorernas nodtyper.  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

> [!WARNING]
> Börja inte ändra den primära VM-SKU: n om klustrets hälso tillstånd inte är felfritt. Om kluster hälsan inte är felfri kommer du bara att göra klustret ytterligare om du försöker ändra VM-SKU: n.
>
> Vi rekommenderar att du inte ändrar VM-SKU: n för en skalnings uppsättning/nodtyp om den inte körs vid [silver tålighet eller större](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Ändring av den virtuella datorns SKU-storlek är en data-destruktiv infrastruktur åtgärd på plats. Utan någon möjlighet att fördröja eller övervaka den här ändringen är det möjligt att åtgärden kan leda till data förlust för tillstånds känsliga tjänster eller orsaka andra oförutsedda drifts problem, även för tillstånds lösa arbets belastningar. Det innebär att den primära nodtypen, som kör tillstånds känsliga Service Fabric-systemtjänster, eller en nodtyp som kör din tillstånds känsliga program arbets belastningar.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uppgradera storlek och operativ system för de virtuella datorernas primära nodtyp
Här är processen för att uppdatera VM-storlek och operativ system för de virtuella datorernas primära nodtypen.  Efter uppgraderingen är den primära nodtypen för virtuella datorer storlek standard D4_V2 och kör Windows Server 2016 Data Center med behållare.

> [!WARNING]
> Innan du försöker utföra den här proceduren i ett produktions kluster rekommenderar vi att du studerar exempelfilerna och kontrollerar processen mot ett test kluster. Klustret är inte heller tillgängligt under en tid. Du kan inte göra ändringar i flera VMSS som deklareras som samma NodeType parallellt. Du måste utföra åtskilda distributions åtgärder för att tillämpa ändringar på varje NodeType-VMSS individuellt.

1. Distribuera det första klustret med två nodtyper och två skalnings uppsättningar (en skalnings uppsättning per nodtyp) med hjälp av dessa exempel [mal len](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) filer.  Båda skalnings uppsättningarna är storlek standard D2_V2 och kör Windows Server 2012 R2 Data Center.  Vänta tills klustret har slutfört bas linje uppgraderingen.   
2. Valfritt – distribuera ett tillstånds känsligt exempel till klustret.
3. När du har bestämt dig för att uppgradera den primära nodtypen för virtuella datorer lägger du till en ny skalnings uppsättning till den primära nodtypen med hjälp av dessa exempel- [mall](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) filer så att den primära nodtypen nu har två skalnings uppsättningar.  System tjänster och användar program kan migreras mellan virtuella datorer i de två olika skalnings uppsättningarna.  De nya virtuella datorerna med skalnings uppsättningen är storlek standard D4_V2 och kör Windows Server 2016 Data Center med behållare.  En ny belastningsutjämnare och en offentlig IP-adress läggs också till med den nya skalnings uppsättningen.  
    Sök efter den nya skalnings uppsättningen i mallen genom att söka efter resursen "Microsoft. Compute/virtualMachineScaleSets" som heter med parametern *vmNodeType2Name* .  Den nya skalnings uppsättningen läggs till i den primära nodtypen med hjälp av egenskaperna-> virtualMachineProfile-> extensionProfile-> tillägg-> Egenskaper-> Inställningar-> nodeTypeRef inställning.
4. Kontrol lera kluster hälsan och kontrol lera att alla noder är felfria.
5. Inaktivera noderna i den gamla skalnings uppsättningen för den primära nodtypen med avsikt att ta bort noden. Du kan inaktivera alla samtidigt och åtgärderna placeras i kö. Vänta tills alla noder har inaktiverats, vilket kan ta lite tid.  Eftersom de äldre noderna i nodtypen är inaktiverade migreras system tjänsterna och dirigeringsrouters till de virtuella datorerna för den nya skalnings uppsättningen i den primära nodtypen.
6. Ta bort den äldre skalnings uppsättningen från den primära nodtypen.
7. Ta bort belastningsutjämnaren som är associerad med den gamla skalnings uppsättningen. Klustret är inte tillgängligt medan den nya offentliga IP-adressen och belastningsutjämnaren har kon figurer ATS för den nya skalnings uppsättningen.  
8. Lagra DNS-inställningar för den offentliga IP-adress som är kopplad till den gamla skalnings uppsättningen för den primära nodtypen i en variabel och ta bort den offentliga IP-adressen.
9. Ersätt DNS-inställningarna för den offentliga IP-adress som är associerad med den nya primära nodens typ skalnings uppsättning med DNS-inställningarna för den borttagna offentliga IP-adressen.  Klustret kan nu uppnås igen.
10. Ta bort nodens tillstånd för noderna från klustret.  Om hållbarhets nivån för den gamla skalnings uppsättningen är silver eller guld utförs det här steget automatiskt av systemet.
11. Om du har distribuerat det tillstånds känsliga programmet i föregående steg kontrollerar du att programmet fungerar.

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
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).

