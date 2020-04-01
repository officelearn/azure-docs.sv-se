---
title: Skala ett Service Fabric-kluster i Azure
description: I den här självstudien får du lära dig hur du skalar ett Service Fabric-kluster i Azure ut och in och hur du rensar överblivna resurser.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251796"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Självstudie: Skala ut ett Service Fabric-kluster i Azure

Den här självstudien är del tre i en serie och visar hur du skalar ut och in ditt befintliga kluster. När du är klar kommer du att veta hur du skalar ditt kluster och hur du rensar överblivna resurser.  Mer information om hur du skalar ett kluster som körs i Azure finns i [Scaling Service Fabric-kluster](service-fabric-cluster-scaling.md).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala in)
> * Lägga till och ta bort nodtyper (skala ut och skala in)
> * Öka nodresurserna (skala upp)

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * Skala in eller ut ett kluster
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure

## <a name="important-considerations-and-guidelines"></a>Viktiga överväganden och riktlinjer

Programarbetsbelastningar förändras med tiden, behöver dina befintliga tjänster mer (eller mindre) resurser?  [Lägga till eller ta bort noder](#add-nodes-to-or-remove-nodes-from-a-node-type) från en nodtyp för att öka eller minska klusterresurser.

Behöver du lägga till mer än 100 noder i klustret?  En enda service fabric-nodtyp/skalningsuppsättning kan inte innehålla mer än 100 noder/virtuella datorer.  Om du vill skala ett kluster efter 100 noder [lägger du till ytterligare nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type).

Har din ansökan har flera tjänster, och gör någon av dem måste vara offentliga eller internet inför?  Typiska program innehåller en frontend gateway-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med frontend-tjänsterna. I det här fallet rekommenderar vi att du [lägger till minst två nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) i klustret.  

Har dina tjänster olika infrastrukturbehov såsom större RAM eller högre CPU-cykler? Ditt program innehåller till exempel en frontend-tjänst och en backend-tjänst. Front-end-tjänsten kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna för internet. Backend-tjänsten är dock beräkningsintensiv och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är internetvända. I det här fallet rekommenderar vi att du [lägger till två eller flera nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) i klustret. På så sätt kan varje nodtyp ha olika egenskaper som internetanslutning eller vm-storlek. Antalet virtuella datorer kan också skalas oberoende av sig.

Tänk på följande när du skalar ett Azure-kluster:

* En enda service fabric-nodtyp/skalningsuppsättning kan inte innehålla mer än 100 noder/virtuella datorer.  Om du vill skala ett kluster efter 100 noder lägger du till ytterligare nodtyper.
* Primära nodtyper som kör produktionsarbetsbelastningar bör ha en [hållbarhetsnivå][durability] för guld eller silver och alltid ha fem eller fler noder.
* Icke-primära nodtyper som kör tillståndskänsliga produktionsarbetsbelastningar bör alltid ha fem eller fler noder.
* Icke-primära nodtyper som kör tillståndslösa produktionsarbetsbelastningar bör alltid ha två eller flera noder.
* Alla nodtyper av [hållbarhetsnivå för][durability] guld eller silver bör alltid ha fem eller fler noder.
* Om skalning i (ta bort noder från) en primär nodtyp, bör du aldrig minska antalet instanser till mindre än vad [tillförlitlighetsnivån][reliability] kräver.

Mer information finns i [vägledningen för klusterkapacitet](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportera mallen för resursgruppen

När du har skapat ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) och konfigurerat resursgruppen framgångsrikt exporterar du resurshanterarens mall för resursgruppen. Genom att exportera mallen kan du automatisera framtida distributioner av klustret och dess resurser eftersom mallen innehåller hela infrastrukturen.  Mer information om hur du exporterar mallar finns i [Hantera Azure Resource Manager-resursgrupper med hjälp av Azure-portalen](/azure/azure-resource-manager/manage-resource-groups-portal).

1. Gå till resursgruppen som innehåller**klustret ( sfclustertutorialgroup**i [Azure-portalen,](https://portal.azure.com)om du följer den här självstudien). 

2. Välj **Distributioner**i den vänstra rutan eller välj länken under **Distributioner**. 

3. Välj den senaste lyckade distributionen i listan.

4. I den vänstra rutan väljer du **Mall** och väljer sedan **Hämta** för att exportera mallen som en ZIP-fil.  Spara mallen och parametrarna på den lokala datorn.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Lägga till noder i eller ta bort noder från en nodtyp

Skala in och ut, eller vågrät skalning, ändrar antalet noder i klustret. När du skalar in eller ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall- och parameterfil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna *filen parameters.json.*  Om du har distribuerat klustret med hjälp av [exempelmallen][template] i den här självstudien finns det tre nodtyper i klustret och tre parametrar som anger antalet noder för varje *nodtyp: nt0InstanceCount*, *nt1InstanceCount*och *nt2InstanceCount*.  Parametern *nt1InstanceCount* anger till exempel instansantalet för den andra nodtypen och anger antalet virtuella datorer i den associerade skalningsuppsättningen för virtuella datorer.

Genom att uppdatera värdet *för nt1InstanceCount* ändrar du därför antalet noder i den andra nodtypen.  Kom ihåg att du inte kan skala ut en nodtyp till mer än 100 noder.  Icke-primära nodtyper som kör tillståndskänsliga produktionsarbetsbelastningar bör alltid ha fem eller fler noder. Icke-primära nodtyper som kör tillståndslösa produktionsarbetsbelastningar bör alltid ha två eller flera noder.

Om du skalar in, tar bort noder från, en nodtyp av [bronshållbarhetsnivå][durability] måste du [manuellt ta bort tillståndet för dessa noder](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  För Silver- och Gold-hållbarhetsnivån görs dessa steg automatiskt av plattformen.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara alla ändringar i *template.json-* och *parameters.json-filerna.*  Om du vill distribuera den uppdaterade mallen kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Eller följande Azure CLI-kommando:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Lägga till en nodtyp i klustret

Varje nodtyp som definieras i ett Service Fabric-kluster som körs i Azure ställs in som en [separat skaluppsättning för virtuella datorer](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. Du kan självständigt skala varje nodtyp uppåt eller nedåt, ha olika uppsättningar portar öppna och använda olika kapacitetsmått. Du kan också självständigt ändra OS SKU som körs på varje klusternod, men observera att du inte kan ha en blandning av Windows och Linux som körs i exempelklustret. En enskild nodtyp/skalningsuppsättning får inte innehålla mer än 100 noder.  Du kan skala ett kluster vågrätt till mer än 100 noder genom att lägga till ytterligare nodtyper/skaluppsättningar. Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall- och parameterfil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna *filen parameters.json.*  Om du har distribuerat klustret med hjälp av [exempelmallen][template] i den här självstudien finns det tre nodtyper i klustret.  I det här avsnittet lägger du till en fjärde nodtyp genom att uppdatera och distribuera en Resource Manager-mall. 

Förutom den nya nodtypen lägger du även till den associerade skalningsuppsättningen för virtuella datorer (som körs i ett separat undernät i det virtuella nätverket) och nätverkssäkerhetsgruppen.  Du kan välja att lägga till nya eller befintliga offentliga IP-adress och Azure belastningsutjämna resurser för den nya skalningsuppsättningen.  Den nya nodtypen har en [hållbarhetsnivå][durability] av Silver och storleken på "Standard_D2_V2".

Lägg till följande nya parametrar i *filen template.json:*
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

Lägg till följande nya variabler i *filen template.json:*
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

Lägg till ett nytt undernät i den virtuella nätverksresursen i filen *template.json:*
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

Lägg till nya offentliga IP-adress- och belastningsutjämnresurser i filen *template.json:*
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

Lägg till nya nätverkssäkerhetsgrupp och skaluppsättningsresurser för virtuella datorer i filen *template.json.*  Egenskapen NodeTypeRef i tillägget Service Fabric-egenskaperna för den virtuella datorns skalningsuppsättning mappar den angivna nodtypen till skalningsuppsättningen.

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

Uppdatera klusterresursen i *filen template.json* och lägg till en ny nodtyp:
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

Lägg till följande parametrar och värden i filen *parameters.json:*
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara alla ändringar i *template.json-* och *parameters.json-filerna.*  Om du vill distribuera den uppdaterade mallen kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Eller följande Azure CLI-kommando:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Ta bort en nodtyp från klustret
När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att ta bort en nodtyp (skaluppsättning för virtuell dator) och alla dess noder. Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret. När klustret skalas skalas även dina program automatiskt.

> [!WARNING]
> Det rekommenderas inte att använda Ta bort AzServiceFabricNodeType för att ta bort en nodtyp från ett produktionskluster som används ofta. Det är ett farligt kommando eftersom det tar bort den virtuella datorn skala uppsättning resurs bakom nodtypen. 

Om du vill ta bort nodtypen kör du cmdleten [Remove-AzServiceFabricNodeType.](/powershell/module/az.servicefabric/remove-azservicefabricnodetype)  Nodtypen måste vara [hållbarhetsnivå][durability] för silver eller guld Cmdlet tar bort skalningsuppsättningen som är associerad med nodtypen och tar lite tid att slutföra.  Kör sedan cmdleten [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) på var och en av noderna som ska tas bort, vilket tar bort nodtillståndet och tar bort noderna från klustret. Om det finns tjänster på noderna flyttas tjänsterna först ut till en annan nod. Om klusterhanteraren inte kan hitta en nod för repliken/tjänsten försenas/blockeras åtgärden.

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

## <a name="increase-node-resources"></a>Öka nodresurserna 
När du har skapat ett Service Fabric-kluster kan du skala en klusternodtyp lodrätt (ändra nodernas resurser) eller uppgradera operativsystemet för de virtuella datorerna för nodtyp.  

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM SKU för en skalningsuppsättning/nodtyp om den inte körs vid Silver hållbarhet eller större. Att ändra VM SKU-storleken är en datadestruktiv infrastrukturåtgärd på plats. Utan viss möjlighet att fördröja eller övervaka den här ändringen är det möjligt att åtgärden kan orsaka dataförlust för tillståndskänsliga tjänster eller orsaka andra oförutsedda driftproblem, även för tillståndslösa arbetsbelastningar.

> [!WARNING]
> Vi rekommenderar att du inte ändrar VM SKU för den primära nodtypen, vilket är en farlig åtgärd och stöds inte.  Om du behöver mer klusterkapacitet kan du lägga till fler VM-instanser eller ytterligare nodtyper.  Om det inte är möjligt kan du skapa ett nytt kluster och [återställa programtillstånd](service-fabric-reliable-services-backup-restore.md) (om tillämpligt) från det gamla klustret.  Om det inte är möjligt kan du [ändra VM SKU för den primära nodtypen](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall- och parameterfil](#export-the-template-for-the-resource-group) från resursgruppen för den senaste distributionen.  Öppna *filen parameters.json.*  Om du har distribuerat klustret med hjälp av [exempelmallen][template] i den här självstudien finns det tre nodtyper i klustret.  

Storleken på de virtuella datorerna i den andra nodtypen anges i parametern *vmNodeType1Size.*  Ändra parametervärdet *vmNodeType1Size* från Standard_D2_V2 till [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), vilket fördubblar resurserna för varje VM-instans.

VM SKU för alla tre nodtyper anges i parametern *vmImageSku.*  Återigen bör ändring av VM SKU av en nodtyp närmas med försiktighet och rekommenderas inte för den primära nodtypen.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara alla ändringar i *template.json-* och *parameters.json-filerna.*  Om du vill distribuera den uppdaterade mallen kör du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Eller följande Azure CLI-kommando:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala in)
> * Lägga till och ta bort nodtyper (skala ut och skala in)
> * Öka nodresurserna (skala upp)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Lägga till och ta bort nodtyper (skala ut och skala in)
> * Öka nodresurserna (skala upp)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
