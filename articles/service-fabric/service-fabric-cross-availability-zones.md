---
title: Distribuera ett Azure Service Fabric-kluster i olika Tillgänglighetszoner | Microsoft Docs
description: Lär dig hur du skapar ett Azure Service Fabric-kluster i olika Tillgänglighetszoner.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077462"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuera ett Azure Service Fabric-kluster i olika Tillgänglighetszoner
Tillgänglighetszoner i Azure är en hög tillgänglighet erbjudande som skyddar dina program och data från datacenter havererar. En Tillgänglighetszon är en unik fysisk plats som är utrustade med oberoende kraft, kylning och nätverk inom en Azure-region.

Service Fabric har stöd för kluster som sträcker sig över Tillgänglighetszoner genom att distribuera nodtyper som är fästa till specifika zoner. Det säkerställer hög tillgänglighet för dina program. Azure Availability Zones är endast tillgänglig i utvalda regioner. Mer information finns i [Azure Tillgänglighetszoner i Översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).

Exempelmallar är tillgängliga: [Service Fabric mellan tillgänglighet zon mall](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Rekommenderade topologin för primära nodtypen av Azure Service Fabric-kluster som täcker över Tillgänglighetszoner
Service Fabric-kluster distribuerade i olika Tillgänglighetszoner säkerställer hög tillgänglighet för klustertillstånd. Om du vill sträcka sig över Service Fabric-kluster i olika zoner, måste du skapa en primära nodtypen i varje Tillgänglighetszon som stöds av regionen. Detta kommer att distribuera startvärdesnoder jämnt mellan varje primära noden.

Den rekommenderade topologin för den primära nodtypen krävs de resurser som beskrivs nedan:

* Kluster-tillförlitlighetsnivån inställd Platina.
* Tre nodtyper markerats som primär.
    * Varje nodtyp bör mappas till en egen virtual machine scale Sets finns i olika zoner.
    * Varje VM-skalningsuppsättning bör ha minst fem noder (Silver hållbarhet).
* En enda offentlig IP-resurs med hjälp av Standard-SKU.
* En enda Belastningsutjämningsresursen med hjälp av Standard-SKU.
* En NSG som refererar till undernätet där du distribuerar VM-skalningsuppsättningar.

>[!NOTE]
> Virtual machine scale Sets enda placering grupp egenskapen måste anges till true, eftersom Service Fabric inte stöder en skaluppsättning för virtuell dator som sträcker sig över zoner.

 ![Tillgänglighetszon för Azure Service Fabric-arkitektur][sf-architecture]

## <a name="networking-requirements"></a>Nätverkskrav
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP-adress och Belastningsutjämningsresursen
Aktivera zonerna på en virtuell datorskalning egenskapsuppsättning resurs, belastningsutjämnare och IP-resurs som refereras av virtuella datorns skaluppsättning måste båda använda en *Standard* SKU. Skapa en belastningsutjämnare eller IP-resurs utan SKU-egenskapen skapar en grundläggande SKU som inte stöder Tillgänglighetszoner. En Standard-SKU-belastningsutjämnare blockerar all trafik från utsidan som standard. en NSG måste distribueras till undernätet för att tillåta utanför trafik.

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
> Det går inte att göra en ändring på plats av SKU: N på offentlig IP-adress och load balancer resurser. Om du migrerar från befintliga resurser som har en grundläggande SKU finns i avsnittet migrering av den här artikeln.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regler för VM-skalningsuppsättningen
Belastningsutjämnarens inkommande NAT-regler måste matcha NAT-pooler från virtuella datorns skalningsuppsättning. Varje VM-skalningsuppsättning måste ha en unik inkommande NAT-pool.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard utgående regler för belastningsutjämnaren med SKU
Standard Load Balancer och offentlig IP innehåller nya funktioner och olika beteenden till utgående anslutning jämfört med grundläggande SKU: er. Om du vill utgående anslutning när du arbetar med Standard-SKU: er, måste du explicit definiera den antingen med offentliga IP-adresser eller offentlig Load Balancer. Mer information finns i [utgående anslutningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) och [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardmallen refererar till en NSG som tillåter alla utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för hanteringsåtgärder för Service Fabric. NSG-reglerna kan ändras efter dina behov.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Aktivera zoner på en virtuell datorskalning ange
Om du vill aktivera en zon på en virtuell datorskalning som du måste inkludera följande tre värden i VM scale set resursen.

* Det första värdet är den **zoner** egenskapen, som anger vilka Tillgänglighetszon som virtuella datorns skalningsuppsättning ska distribueras till.
* Det andra värdet är ”singlePlacementGroup”-egenskapen, som måste anges till true.
* Det tredje värdet är ”faultDomainOverride”-egenskap i Service Fabric VM scale set-tillägget. Värdet för den här egenskapen ska inkludera region och zon som den här skalningsuppsättningen för virtuell dator ska placeras. Exempel: ”faultDomainOverride”: ”eastus/az1” alla resurser för VM-skalningsuppsättningen måste placeras i samma region eftersom Service Fabric-kluster inte har mellan regionsstöd.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Aktivera flera primära nodtyperna i Service Fabric-kluster-resurs
Ange egenskapen ”isPrimary” till ”true” om du vill ange en eller flera nodtyper som primär i en klusterresurs. När du distribuerar Service Fabric-kluster i olika Tillgänglighetszoner, bör du ha tre nodtyper i olika zoner.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrera till med hjälp av Tillgänglighetszoner från ett kluster med en grundläggande SKU-belastningsutjämnare och den grundläggande SKU-IP
Om du vill migrera ett kluster, vilket med en belastningsutjämnare och IP-med en grundläggande SKU, måste du först skapa en helt ny belastningsutjämnare och IP-resurs med hjälp av standard-SKU. Det går inte att uppdatera dessa resurser på plats.

Den nya LB och IP-bör refereras i nya mellan Tillgänglighetszon nodtyperna som du vill använda. I exemplet ovan tre nya VM scale set resurser har lagts till i zoner 1,2 och 3. Dessa VM-skalningsuppsättning anger referens nyskapade Belastningsutjämnaren och IP- och markeras som primär nodtyperna i Service Fabric-klusterresursen.

Om du vill börja, behöver du lägga till nya resurser i din befintliga Resource Manager-mall. De här resurserna inkluderar:
* En offentlig IP-resurs med hjälp av Standard-SKU.
* En Belastningsutjämningsresursen med hjälp av Standard-SKU.
* En NSG som refererar till undernätet där du distribuerar VM-skalningsuppsättningar.
* Tre nodtyper markerats som primär.
    * Varje nodtyp bör mappas till en egen virtual machine scale Sets finns i olika zoner.
    * Varje VM-skalningsuppsättning bör ha minst fem noder (Silver hållbarhet).

Ett exempel på dessa resurser finns i den [exempelmallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

När resurserna har slutfört distributionen, kan du börja inaktivera noderna i den primära nodtypen från det ursprungliga klustret. När noderna är inaktiverade, migreras systemtjänster till den nya primära nodtypen som har distribuerats i ovanstående steg.

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

När noderna är alla inaktiverade, körs systemtjänster på den primära nodtypen som fördelas mellan zoner. Du kan sedan ta bort inaktiverat noder från klustret. När noderna har tagits bort kan du ta bort den ursprungliga IP-Adressen, belastningsutjämnare, och resurser för VM-skalningsuppsättningen.

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

Du bör sedan ta bort referenser till dessa resurser från Resource Manager-mallen som du har distribuerat.

Det sista steget måste du uppdatera DNS-namn och offentlig IP-adress.

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
