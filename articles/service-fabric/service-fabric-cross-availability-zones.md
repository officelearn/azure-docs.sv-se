---
title: Distribuera ett kluster över tillgänglighetszoner
description: Lär dig hur du skapar ett Azure Service Fabric-kluster över tillgänglighetszoner.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609986"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuera ett Azure Service Fabric-kluster över tillgänglighetszoner
Tillgänglighetszoner i Azure är ett erbjudande med hög tillgänglighet som skyddar dina program och data från datacenterfel. En tillgänglighetszon är en unik fysisk plats utrustad med oberoende kraft, kylning och nätverk inom en Azure-region.

Service Fabric stöder kluster som sträcker sig över tillgänglighetszoner genom att distribuera nodtyper som är fästa på specifika zoner. Detta säkerställer hög tillgänglighet för dina program. Azure-tillgänglighetszoner är endast tillgängliga i vissa regioner. Mer information finns i [Översikt över Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview).

Exempelmallar är tillgängliga: [Zonemall för trafikinfrastruktur för korstillgänglighet](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Rekommenderad topologi för primär nodtyp av Azure Service Fabric-kluster som sträcker sig över tillgänglighetszoner
Ett Service Fabric-kluster som distribueras över tillgänglighetszoner säkerställer hög tillgänglighet för klustertillståndet. Om du vill sträcka ut ett Service Fabric-kluster över zoner måste du skapa en primär nodtyp i varje tillgänglighetszon som stöds av regionen. Detta kommer att fördela frönoder jämnt över var och en av de primära nodtyperna.

Den rekommenderade topologin för den primära nodtypen kräver de resurser som beskrivs nedan:

* Klustertillförlitlighetsnivån inställd på Platinum.
* Tre nodtyper markerade som primära.
    * Varje nodtyp ska mappas till en egen skalningsuppsättning för virtuella datorer som finns i olika zoner.
    * Varje skalauppsättning för virtuella datorer bör ha minst fem noder (Silver Hållbarhet).
* En enda offentlig IP-resurs med Standard SKU.
* En enda belastningsutjämnad resurs med standard SKU.
* En NSG som refereras av undernätet där du distribuerar skaluppsättningarna för den virtuella datorn.

>[!NOTE]
> Egenskapen för en virtuell datorskalauppsättning måste anges till true, eftersom Service Fabric inte stöder en enda skalauppsättning för virtuella datorer som sträcker sig över zoner.

 ![Azure Service Fabric Tillgänglighet Zon Arkitektur][sf-architecture]

## <a name="networking-requirements"></a>Krav på nätverk
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP- och belastningsutjämnareresurs
Om du vill aktivera egenskapen zoner på en resurs för skalningsuppsättning för virtuell dator *Standard* måste belastningsutjämnaren och IP-resursen som refereras av den virtuella datorns skaluppsättning båda använda en standard-SKU. Om du skapar en belastningsutjämnare eller IP-resurs utan egenskapen SKU skapas en grundläggande SKU som inte stöder tillgänglighetszoner. En Standard SKU belastningsutjämningsman blockerar all trafik från utsidan som standard som standard. För att tillåta extern trafik måste en NSG distribueras till undernätet.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Det går inte att göra en in-place förändring av SKU på den offentliga IP- och belastningsutjämnarens resurser. Om du migrerar från befintliga resurser som har en grundläggande SKU läser du migreringsavsnittet i den här artikeln.

### <a name="virtual-machine-scale-set-nat-rules"></a>Nat-regler för virtuell datorskala
De inkommande NAT-reglerna för lastjämförare bör matcha NAT-poolerna från skalningsuppsättningen för den virtuella datorn. Varje skalauppsättning för virtuella datorer måste ha en unik inkommande NAT-pool.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Utgående standardregler för SKU-belastningsutjämnare
Standardbelastningsutjämnare och offentlig standard IP introducerar nya förmågor och olika beteenden till utgående anslutning jämfört med att använda grundläggande SKU:er. Om du vill ha utgående anslutning när du arbetar med standardSku: er måste du uttryckligen definiera den antingen med vanliga offentliga IP-adresser eller standard offentlig belastningsutjämnare. Mer information finns i [Utgående anslutningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) och [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardmallen refererar till en NSG som tillåter all utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för hantering av service fabric.Inbound traffic is limited to the ports that are required for Service Fabric management operations. NSG-reglerna kan ändras för att uppfylla dina krav.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Aktivera zoner för en skalningsuppsättning för virtuella datorer
Om du vill aktivera en zon måste du på en skalningsuppsättning för virtuella datorer inkludera följande tre värden i skaluppsättningsresursen för den virtuella datorn.

* Det första värdet är **egenskapen zoner,** som anger vilken tillgänglighetszon den virtuella datorns skaluppsättning ska distribueras till.
* Det andra värdet är egenskapen "singlePlacementGroup", som måste vara true.
* Det tredje värdet är egenskapen "faultDomainOverride" i tillägget Service Fabric-skalauppsättning för virtuell dator. Värdet för den här egenskapen ska omfatta den region och zon där den här virtuella datorns skaluppsättning ska placeras. Exempel: "faultDomainOverride": "eastus/az1" Alla resurser för skala för virtuella datorer måste placeras i samma region eftersom Azure Service Fabric-kluster inte har stöd för flera regioner.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Aktivera flera primära nodtyper i service fabric-klusterresursen
Om du vill ange en eller flera nodtyper som primära i en klusterresurs anger du egenskapen "isPrimary" till "true". När du distribuerar ett Service Fabric-kluster över tillgänglighetszoner bör du ha tre nodtyper i olika zoner.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrera till att använda tillgänglighetszoner från ett kluster med hjälp av en grundläggande SKU-belastningsutjämnare och en grundläggande SKU-IP
Om du vill migrera ett kluster, som använde en belastningsutjämning och IP med en grundläggande SKU, måste du först skapa en helt ny belastningsutjämnare och IP-resurs med standard-SKU. Det går inte att uppdatera dessa resurser på plats.

Den nya LB och IP ska refereras i de nya nodtyperna för mellantillgänglighetszon som du vill använda. I exemplet ovan lades tre nya resurser för skalning av virtuella datorer till i zonerna 1,2 och 3. Dessa skalningsuppsättningar för virtuella datorer refererar till den nyligen skapade LB och IP och markeras som primära nodtyper i service fabric-klusterresursen.

Till att börja med måste du lägga till de nya resurserna i din befintliga Resource Manager-mall. Dessa resurser omfattar:
* En offentlig IP-resurs med Standard SKU.
* En belastningsutjämnad resurs med standard SKU.
* En NSG som refereras av undernätet där du distribuerar skaluppsättningarna för den virtuella datorn.
* Tre nodtyper markerade som primära.
    * Varje nodtyp ska mappas till en egen skalningsuppsättning för virtuella datorer som finns i olika zoner.
    * Varje skalauppsättning för virtuella datorer bör ha minst fem noder (Silver Hållbarhet).

Ett exempel på dessa resurser finns i [exempelmallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

När resurserna har distribuerats klart kan du börja inaktivera noderna i den primära nodtypen från det ursprungliga klustret. När noderna är inaktiverade migreras systemtjänsterna till den nya primära nodtypen som har distribuerats i steget ovan.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

När noderna är inaktiverade körs systemtjänsterna på den primära nodtypen, som är spridd över zoner. Du kan sedan ta bort de inaktiverade noderna från klustret. När noderna har tagits bort kan du ta bort de ursprungliga ip-, belastningsutjämnaren och skaluppsättningsresurserna för virtuella datorer.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Du bör sedan ta bort referenserna till dessa resurser från resurshanteraren-mallen som du har distribuerat.

The final step will involve updating the DNS name and Public IP.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
