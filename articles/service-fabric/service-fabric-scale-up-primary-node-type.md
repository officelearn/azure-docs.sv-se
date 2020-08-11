---
title: Skala upp en Azure Service Fabric Primary Node-typ
description: Lär dig hur du skalar ett Service Fabric kluster genom att lägga till en nodtyp.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: eecf398359470f6e5e151c53eb63b3cb56efbe39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056762"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Skala upp en Service Fabric-kluster primär nodtyp genom att lägga till en nodtyp
Den här artikeln beskriver hur du skalar upp ett Service Fabric-klusters primära nodtyp genom att lägga till ytterligare en nodtyp i klustret. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat.

Exempel mallarna i följande självstudie finns här: [Service Fabric exempel på den primära nodtypen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample) för att skala

> [!WARNING]
> Försök inte att skala upp en primär nodtyp om klustrets status är ohälsosam, eftersom detta bara kommer att göra klustret ytterligare.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Process för att uppgradera storlek och operativ system för den primära nodtypen
Följande är en process för att uppdatera VM-storlek och operativ system för de virtuella datorernas primära nodtyper.  Efter uppgraderingen är den primära nodtypen för virtuella datorer storlek standard D4_V2 och kör Windows Server 2019 Data Center med behållare.

> [!WARNING]
> Innan du försöker utföra den här proceduren i ett produktions kluster rekommenderar vi att du studerar exempelfilerna och kontrollerar processen mot ett test kluster. Klustret kan också vara otillgängligt under en kort tids period. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Distribuera det första Service Fabric klustret 
Om du vill följa med i exemplet distribuerar du det första klustret med en enda primär nodtyp och en enda skalnings uppsättning [Service Fabric-initialt kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Du kan hoppa över det här steget om du redan har ett befintligt Service Fabric-kluster som redan har distribuerats. 

1. Logga in på ditt Azure-konto. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Skapa en ny resursgrupp. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. Fyll i parametervärdena i mallfilerna. 
4. Distribuera klustret till resurs gruppen som skapades i steg 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Lägg till en ny typ av primär nod i klustret
> [!Note]
> Resurserna som skapas i följande steg blir den nya primära nodtypen i klustret när skalnings åtgärden har slutförts. Se till att du använder namn som är unika från det första under nätet, offentliga IP-adresser, Load Balancer, skalnings uppsättning för virtuella datorer och nodtypen. 

Du kan hitta en mall med alla följande steg som slutförs här: [Service Fabric – nytt kluster för nodtyp](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). Följande steg innehåller partiella resurs kods tycken som markerar ändringarna i de nya resurserna.  

1. Skapa ett nytt undernät i din befintliga Virtual Network.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Skapa en ny offentlig IP-resurs med en unik domainNameLabel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Skapa en ny Load Balancer-resurs som är beroende av den offentliga IP-adress som skapats ovan. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Skapa en ny skalnings uppsättning för virtuella datorer som använder den nya SKU: n för VM och OS-SKU: n som du vill skala upp till. 

Referens för nodtyp 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM-SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS-SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. Lägg till en ny nodtyp i klustret som refererar till den skalnings uppsättning för virtuella datorer som skapades ovan. Egenskapen **isPrimary** för den här nodtypen ska vara inställd på True. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Distribuera den uppdaterade ARM-mallen. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Service Fabric klustret har nu två nodtyper när distributionen är klar. 

### <a name="remove-the-existing-node-type"></a>Ta bort den befintliga nodtypen 
När resurserna har distribuerats kan du börja inaktivera noderna i den ursprungliga primära nodtypen. När noderna är inaktiverade migreras system tjänsterna till den nya primära nodtypen som har distribuerats i steget ovan.

1. Ange egenskapen primär nodtyp i Service Fabric kluster resurs till false. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Distribuera mallen med den uppdaterade isPrimary-egenskapen på den ursprungliga nodtypen. Du kan hitta en mall med den primära flaggan inställt på falskt för den ursprungliga nodtypen här: [Service Fabric-primär nodtyp false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Inaktivera noderna i nodtypen 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* För brons hållbarhet väntar du tills alla noder får inaktiverat tillstånd.
* För silver-och guld tålighet kommer vissa noder att gå in till inaktiverade och resten kommer att inaktive ras. På fliken information i noderna inaktive ras, om alla har fastnat på att säkerställa kvorum för infrastruktur tjänst partitioner, är det säkert att fortsätta.

> [!Note]
> Det här steget kan ta en stund att slutföra. 

4. Stoppa data på nodtyp 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Frigör noder i den ursprungliga skalnings uppsättningen för virtuella datorer 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Steg 6 och 7 är valfria om du redan använder en offentlig IP-adress för standard-SKU och en standard-SKU-belastningsutjämnare. I det här fallet kan du ha flera skalnings uppsättningar för virtuella datorer/Node-typer under samma belastningsutjämnare. 

6. Du kan nu ta bort den ursprungliga IP-adressen och Load Balancer resurser. I det här steget ska du också uppdatera DNS-namnet. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Uppdatera hanterings slut punkten på klustret för att referera till den nya IP-adressen. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Ta bort nod-tillstånd från nodtyp 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Ta bort referensen till den ursprungliga nodtypen från Service Fabric resursen i ARM-mallen. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Uppdatera kluster resursen i mallen och konfigurera hälso principer för att ignorera infrastruktur resurser:/systemets program hälsa genom att lägga till applicationDeltaHealthPolicies under egenskaper för kluster resurs enligt vad som framgår nedan. Principen nedan bör ignorera befintliga fel men inte tillåta nya hälso fel.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Ta bort alla andra resurser som är relaterade till den ursprungliga nodtypen från ARM-mallen. Se [Service Fabric-ny nodtyp kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) för en mall med alla dessa ursprungliga resurser borttagna.

11. Distribuera den ändrade Azure Resource Manager-mallen. * * Detta steg tar en stund, vanligt vis upp till två timmar. Den här uppgraderingen ändrar inställningarna till InfrastructureService, vilket innebär att en omstart av noden krävs. I det här fallet ignoreras forceRestart. Parametern upgradeReplicaSetCheckTimeout anger den maximala tid som Service Fabric väntar på att en partition ska vara i ett säkert tillstånd, om den inte redan är i säkert tillstånd. När säkerhets kontrollerna har godkänts för alla partitioner på en nod fortsätter Service Fabric med uppgraderingen på noden. Värdet för parametern upgradeTimeout kan minskas till 6 timmar, men för maximal säkerhet 12 timmar bör användas.
Kontrol lera sedan att Service Fabric resursen i portalen visas som klar. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Klustrets primära Node-typ har nu uppgraderats. Kontrol lera att alla distribuerade program fungerar korrekt och att kluster hälsan är OK.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en nodtyp i ett kluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).
