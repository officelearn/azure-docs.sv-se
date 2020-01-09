---
title: Distribuera ett kluster mellan Tillgänglighetszoner
description: Lär dig hur du skapar ett Azure Service Fabric-kluster över Tillgänglighetszoner.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609986"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuera ett Azure Service Fabric-kluster över Tillgänglighetszoner
Tillgänglighetszoner i Azure är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. En tillgänglighets zon är en unik fysisk plats utrustad med oberoende strömförsörjning, kylning och nätverk inom en Azure-region.

Service Fabric stöder kluster som sträcker sig över flera Tillgänglighetszoner genom att distribuera nodtyper som är fästa på vissa zoner. Detta säkerställer hög tillgänglighet för dina program. Azure-tillgänglighetszoner är bara tillgängliga i SELECT-regioner. Mer information finns i [Azure-tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).

Exempel på mallar är tillgängliga: [Service Fabric kors tillgänglighets zons mal len](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Rekommenderad topologi för primär nodtyp för Azure Service Fabric kluster som sträcker sig över Tillgänglighetszoner
Ett Service Fabric kluster som distribueras i Tillgänglighetszoner garanterar hög tillgänglighet för kluster tillstånd. Om du vill spänna ett Service Fabric kluster mellan zoner måste du skapa en primär nodtyp i varje tillgänglighets zon som stöds av regionen. Detta distribuerar startnoderna jämnt över var och en av de primära nodtypen.

Den rekommenderade topologin för den primära nodtypen kräver de resurser som beskrivs nedan:

* Kluster Tillförlitlighets nivån inställd på platina.
* Tre nodtyper som är markerade som primära.
    * Varje nodtyp ska mappas till en egen skalnings uppsättning för virtuella datorer som finns i olika zoner.
    * Varje skalnings uppsättning för virtuella datorer måste ha minst fem noder (Silver tålighet).
* En enda offentlig IP-resurs med standard-SKU.
* En enkel Load Balancer resurs med standard-SKU.
* En NSG som refereras till av under nätet som du distribuerar dina skalnings uppsättningar för virtuella datorer i.

>[!NOTE]
> Den virtuella datorns skal uppsättnings grupp egenskap för enskild placering måste anges till true, eftersom Service Fabric inte stöder en enskild skalnings uppsättning för virtuella datorer som omfattar zoner.

 ![Arkitektur för Azure Service Fabric tillgänglighets zon][sf-architecture]

## <a name="networking-requirements"></a>Nätverks krav
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP-adress och Load Balancer resurs
Om du vill aktivera egenskapen zoner i en resurs för skalnings uppsättning för virtuella datorer måste den belastningsutjämnare och den IP-resurs som den virtuella datorns skalnings uppsättning refererar till använda en *standard* -SKU. Genom att skapa en belastningsutjämnare eller en IP-resurs utan SKU-egenskapen skapas en grundläggande SKU, som inte stöder Tillgänglighetszoner. En standard-SKU-belastningsutjämnare blockerar all trafik från utsidan som standard. Om du vill tillåta yttre trafik måste en NSG distribueras till under nätet.

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
> Det går inte att göra en förändring på plats av SKU: n på den offentliga IP-adressen och belastnings Utjämnings resurserna. Om du migrerar från befintliga resurser som har en grundläggande SKU, kan du läsa avsnittet migrering i den här artikeln.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regler för skalnings uppsättning för virtuell dator
Belastnings utjämningens inkommande NAT-regler ska matcha NAT-poolerna från den virtuella datorns skal uppsättning. Varje skalnings uppsättning för virtuella datorer måste ha en unik inkommande NAT-pool.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard-SKU Load Balancer utgående regler
Standard Load Balancer och standard offentlig IP introducerar nya funktioner och olika beteenden för utgående anslutning jämfört med att använda Basic SKU: er. Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer. Mer information finns i [utgående anslutningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) och [Azure standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standard mal len refererar till en NSG som tillåter all utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för Service Fabric hanterings åtgärder. NSG-reglerna kan ändras för att uppfylla dina krav.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Aktivera zoner på en skalnings uppsättning för virtuella datorer
Om du vill aktivera en zon måste du ha följande tre värden i den virtuella datorns skalnings uppsättnings resurs i en skalnings uppsättning för virtuella datorer.

* Det första värdet är egenskapen **zoner** , som anger vilken tillgänglighets zon som den virtuella datorns skalnings uppsättning ska distribueras till.
* Det andra värdet är egenskapen "singlePlacementGroup", som måste vara inställd på True.
* Det tredje värdet är egenskapen "faultDomainOverride" i Service Fabric tillägget för skalnings uppsättning för virtuell dator. Värdet för den här egenskapen ska innehålla den region och zon där denna skalnings uppsättning för den virtuella datorn kommer att placeras. Exempel: "faultDomainOverride": "East-/AZ1" alla resurser för skalnings uppsättning för virtuella datorer måste placeras i samma region eftersom Azure Service Fabric-kluster inte har stöd för flera regioner.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Aktivera flera typer av primära noder i Service Fabric kluster resursen
Om du vill ange en eller flera nodtyper som primära i en kluster resurs anger du egenskapen "isPrimary" till "true". När du distribuerar ett Service Fabric kluster över Tillgänglighetszoner bör du ha tre nodtyper i olika zoner.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrera till att använda Tillgänglighetszoner från ett kluster med hjälp av en Basic SKU Load Balancer och en grundläggande SKU-IP
Om du vill migrera ett kluster som använde en Load Balancer och en IP-adress med en grundläggande SKU måste du först skapa en helt ny Load Balancer och IP-resurs med standard-SKU: n. Det går inte att uppdatera resurserna på plats.

Den nya LB och IP ska refereras till i de nya noderna för mellan tillgänglighets zoner som du vill använda. I exemplet ovan lades tre nya resurser för skalnings uppsättning för virtuell dator i zon 1, 2 och 3. De här referenserna för den virtuella datorns skalnings uppsättningar innehåller den nyligen skapade LB och IP som marker ATS som primära nodtyper i Service Fabric kluster resursen.

För att börja måste du lägga till de nya resurserna i din befintliga Resource Manager-mall. Dessa resurser omfattar:
* En offentlig IP-resurs med standard-SKU.
* En Load Balancer resurs med standard-SKU.
* En NSG som refereras till av under nätet som du distribuerar dina skalnings uppsättningar för virtuella datorer i.
* Tre nodtyper som är markerade som primära.
    * Varje nodtyp ska mappas till en egen skalnings uppsättning för virtuella datorer som finns i olika zoner.
    * Varje skalnings uppsättning för virtuella datorer måste ha minst fem noder (Silver tålighet).

Du hittar ett exempel på dessa resurser i [exempel mal len](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

När resurserna har distribuerats kan du börja inaktivera noderna i den primära nodtypen från det ursprungliga klustret. När noderna är inaktiverade migreras system tjänsterna till den nya primära nodtypen som har distribuerats i steget ovan.

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

När noderna är inaktiverade körs system tjänsterna på den primära nodtypen, som är spridda över zoner. Du kan sedan ta bort de inaktiverade noderna från klustret. När noderna har tagits bort kan du ta bort de ursprungliga IP-, Load Balancer-och skalnings uppsättnings resurserna för virtuella datorer.

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

Du bör sedan ta bort referenserna till dessa resurser från den Resource Manager-mall som du har distribuerat.

Det sista steget innebär att du uppdaterar DNS-namnet och den offentliga IP-adressen.

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
