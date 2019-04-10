---
title: Skala ut ett Service Fabric-kluster i Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur du skalar ett Service Fabric-kluster i Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fa9b091beacbc98c6939ec0454bd04da2b7561e7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278708"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Självstudier: Skala ett Service Fabric-kluster i Azure

Den här självstudien är del tre i en serie och visar hur du skalar ditt befintliga kluster ut och in. När du är klar kommer du att veta hur du skalar ditt kluster och hur du rensar överblivna resurser.  Mer information om att skala ett kluster som körs i Azure [skalning Service Fabric-kluster](service-fabric-cluster-scaling.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala i)
> * Lägga till och ta bort nodtyper (skala ut och skala i)
> * Öka noden resurser (skala upp)

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * Skala in eller ut ett kluster
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure

## <a name="important-considerations-and-guidelines"></a>Att tänka på och riktlinjer

Arbetsbelastningar för program ändras med tiden, dina befintliga tjänster som behöver mer (eller mindre) resurser?  [Lägga till eller ta bort noder](#add-nodes-to-or-remove-nodes-from-a-node-type) från en nodtyp att öka eller minska klusterresurser.

Behöver du lägga till fler än 100 noder i klustret?  En enda Service Fabric-noden typ/skalningsuppsättning kan inte innehålla fler än 100 noder/VM: ar.  Skala ett kluster utöver 100 noder [lägga till ytterligare nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type).

Har ditt program flera tjänster och behöver någon av dem vara offentligt eller mot internet?  Vanliga program innehåller en klientdelsgateway som-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med frontend-tjänster. I det här fallet rekommenderar vi att du [lägga till minst två nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) till klustret.  

Har dina tjänster annan infrastrukturbehov, till exempel RAM-minne eller högre CPU-cykler? Programmet innehåller till exempel en frontend-tjänst och en backend tjänst. Frontend-tjänst kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna till internet. Backend-tjänsten, men är beräkning intensiva och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är internet som riktas mot. I det här fallet vi rekommenderar att du [lägga till två eller flera nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) till ditt kluster. På så sätt kan varje nodtyp har olika egenskaper, till exempel internet-anslutning eller VM-storlek. Hur många virtuella datorer kan skalas oberoende av varandra, samt.

Tänk på följande riktlinjer när du skalar en Azure-kluster:

* En enda Service Fabric-noden typ/skalningsuppsättning kan inte innehålla fler än 100 noder/VM: ar.  Lägg till ytterligare en nod-typer för att skala ett kluster utöver 100 noder.
* Primär nodtyper som kör produktionsarbetsbelastningar ska ha en [hållbarhetsnivå] [ durability] guld eller Silver och alltid ha fem eller fler noder.
* icke-primär nodtyper tillståndskänsliga produktionsarbetsbelastningar körs bör alltid ha fem eller fler noder.
* icke-primär nodtyper tillståndslösa produktionsarbetsbelastningar körs bör alltid ha två eller flera noder.
* Alla nod slags [hållbarhetsnivå] [ durability] guld eller Silver alltid ska ha fem eller fler noder.
* Om skalning i (ta bort noder från) en primära nodtypen aldrig bör du minska antalet instanser till färre än vad den [tillförlitlighetsnivån] [ reliability] kräver.

Mer information finns i [kluster kapacitet vägledning](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportera mallen för resursgruppen

När du har skapat en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) och hur du konfigurerar din resursgrupp, exportera Resource Manager-mallen för resursgruppen. Genom att exportera mallen kan du automatisera framtida distributioner för klustret och dess resurser eftersom mallen innehåller alla hela infrastrukturen.  Mer information om hur du exporterar mallar läsa [hantera Azure Resource Manager-resursgrupper med hjälp av Azure portal](/azure/azure-resource-manager/manage-resource-groups-portal).

1. I den [Azure-portalen](https://portal.azure.com)går du till resursgruppen som innehåller klustret (**sfclustertutorialgroup**, om du följer den här kursen). 

2. I den vänstra rutan väljer **distributioner**, eller klicka på länken under **distributioner**. 

3. Välj den senaste distributionen i listan.

4. I den vänstra rutan väljer **mall** och välj sedan **hämta** och exportera mallen som en ZIP-fil.  Spara mall och parametrar till den lokala datorn.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Lägga till noder eller ta bort noder från en nodtyp

Skala in och ut eller horisontell skalning, ändrar du antalet noder i klustret. När du skalar in eller ut kan du lägga till flera instanser av virtuella datorer till skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parametrar fil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna den *parameters.json* fil.  Om du har distribuerat klustret med den [exempelmallen] [ template] i den här självstudien finns tre nodtyperna i klustret och tre parametrar som anger antalet noder för varje nodtyp:  *nt0InstanceCount*, *nt1InstanceCount*, och *nt2InstanceCount*.  Den *nt1InstanceCount* parameter, till exempel anger instansantalet för den andra nodtypen och anger hur många virtuella datorer i skalningsuppsättningen tillhörande virtuella datorn.

I så fall genom att uppdatera värdet för den *nt1InstanceCount* du ändra antalet noder i den andra nodtypen.  Kom ihåg att du inte skala en nodtyp ut till fler än 100 noder.  icke-primär nodtyper tillståndskänsliga produktionsarbetsbelastningar körs bör alltid ha fem eller fler noder. icke-primär nodtyper tillståndslösa produktionsarbetsbelastningar körs bör alltid ha två eller flera noder.

Om du arbetar för att bygga i, ta bort noder från en nodtyp Brons [hållbarhetsnivå] [ durability] måste du [manuellt ta bort tillstånd av dessa noder](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  De här stegen utförs automatiskt av plattformen för Silver och Gold hållbarhetsnivå.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringar av den *template.json* och *parameters.json* filer.  Om du vill distribuera den uppdaterade mallen, kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Eller med följande Azure CLI-kommando:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Lägga till en nodtyp i klustret

Varje nodtyp som definieras i Service Fabric-kluster som körs i Azure konfigureras som en [separata virtuella datorskaluppsättningen](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. Du kan oberoende skala varje nodtyp upp eller ned, ha olika portar öppna och använda olika kapacitet. Du kan även oberoende ändra OS-SKU som körs på varje nod i klustret, men Observera att du inte kan ha en blandning av Windows och Linux som körs i exemplet kluster. En enskild nod typ/skalningsuppsättning får inte innehålla fler än 100 noder.  Du kan skala ett kluster vågrätt mot fler än 100 noder genom att lägga till ytterligare en nod typer/skalningsuppsättningar. Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parametrar fil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna den *parameters.json* fil.  Om du har distribuerat klustret med den [exempelmallen] [ template] i den här självstudien finns tre typer av noden i klustret.  I det här avsnittet lägger du till en fjärde nodtyp genom att uppdatera och distribuera en Resource Manager-mall. 

Förutom nya nodtyp du också lägga till associerade VM-skalningsuppsättningen (som körs i ett separat undernät för det virtuella nätverket) och nätverk säkerhetsgrupp.  Du kan välja att lägga till ny eller befintlig offentlig IP-adress och Azure load balancer resurser för den nya skaluppsättningen.  Den nya nodtypen har en [hållbarhetsnivå] [ durability] Silver och storleken på ”Standard_D2_V2”.

I den *template.json* Lägg till följande nya parametrar:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

I den *template.json* Lägg till följande nya variabler:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

I den *template.json* filen, lägga till ett nytt undernät i den virtuella nätverksresursen:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

I den *template.json* Lägg till nya offentliga IP-adress och load balancer-resurser:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

I den *template.json* Lägg till ny network security group och den virtuella datorn skalningsuppsättningsresurser.  Egenskapen NodeTypeRef egenskaperna Service Fabric-tillägget för virtuella datorns skalningsuppsättning mappar den angivna nodtypen till skalningsuppsättningen.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

I den *template.json* filen, uppdatera klusterresursen och lägga till en ny nod:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

I den *parameters.json* Lägg till följande nya parametrar och värden:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringar av den *template.json* och *parameters.json* filer.  Om du vill distribuera den uppdaterade mallen, kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Eller med följande Azure CLI-kommando:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Ta bort en nodtyp från klustret
När du har skapat ett Service Fabric-kluster, kan du skala ett kluster horisontellt genom att ta bort en nodtyp (virtual machine scale Sets) och alla dess noder. Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret. När klustret skalas skalas programmen automatiskt samt.

> [!WARNING]
> Du bör inte använda Remove-AzServiceFabricNodeType att ta bort en nodtyp från ett produktionskluster som ska användas regelbundet. Det är ett farliga kommando som tar bort VM scale set resursen bakom nodtyp. 

Om du vill ta bort nodtyp, kör den [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet.  Nodtypen måste vara Silver eller Gold [hållbarhetsnivå] [ durability] cmdlet: en tar bort skalningsuppsättningen som är associerade med nodtyp och tar lite tid att slutföra.  Kör sedan den [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) cmdleten på varje nod att ta bort, vilket tar bort noden tillstånd och tar bort noder från klustret. Om det finns tjänster på noderna, sedan flyttas tjänsterna först till en annan nod. Om cluster manager i inte hittar en nod för repliken/tjänst, är åtgärden för fördröjd/blockerad.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Öka noden resurser 
När du har skapat ett Service Fabric-kluster, kan du skala en klusternodstyp lodrätt (ändra resurser noder) eller uppgradera operativsystemet på nodtyp virtuella datorer.  

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM-SKU på en skala set/nodtyp såvida den inte körs med Silver hållbarhet eller större. Ändrar storlek på VM-SKU är en data-destruktiv plats infrastruktur-åtgärd. Det är möjligt att det kan leda till dataförlust för tillståndskänsliga tjänster eller orsakar andra oförutsedda driftsproblem, även för tillståndslösa arbetsbelastningar utan några möjligheten att fördröja eller övervaka den här ändringen.

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM-SKU på den primära nodtypen, vilket är en farliga åtgärd och som inte stöds.  Om du behöver mer kapacitet i klustret kan du lägga till fler instanser av virtuella datorer eller ytterligare nodtyper.  Om detta inte är möjligt kan du skapa ett nytt kluster och [Återställ programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från ditt gamla kluster.  Om detta inte är möjligt kan du [ändra VM-SKU på den primära nodtypen](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parametrar fil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna den *parameters.json* fil.  Om du har distribuerat klustret med den [exempelmallen] [ template] i den här självstudien finns tre typer av noden i klustret.  

Storleken på de virtuella datorerna i den andra nodtypen har angetts i den *vmNodeType1Size* parametern.  Ändra den *vmNodeType1Size* parametervärdet från Standard_D2_V2 till [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), vilket fördubblar resurser på varje virtuell datorinstans.

VM-SKU för alla typer av tre noder har angetts i den *vmImageSku* parametern.  Igen, ändra VM-SKU på en nodtyp bör vara närmat sig med försiktighet och rekommenderas inte för den primära nodtypen.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringar av den *template.json* och *parameters.json* filer.  Om du vill distribuera den uppdaterade mallen, kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Eller med följande Azure CLI-kommando:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala i)
> * Lägga till och ta bort nodtyper (skala ut och skala i)
> * Öka noden resurser (skala upp)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
ND skalar in))
> * Lägga till och ta bort nodtyper (skala ut och skala i)
> * Öka noden resurser (skala upp)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
