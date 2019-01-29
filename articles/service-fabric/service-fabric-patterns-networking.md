---
title: Mönster för nätverk för Azure Service Fabric | Microsoft Docs
description: Beskriver vanliga nätverksmönster för Service Fabric och hur du skapar ett kluster med hjälp av funktioner för Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 7f6e95b28482ed6d75bb76773da05aebd1855a66
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093401"
---
# <a name="service-fabric-networking-patterns"></a>Nätverksmönster för Service Fabric
Du kan integrera Azure Service Fabric-klustret med andra funktioner för Azure. I den här artikeln visar vi dig hur du skapar kluster som använder följande funktioner:

- [Befintligt virtuellt nätverk eller undernät](#existingvnet)
- [Statisk offentlig IP-adress](#staticpublicip)
- [Endast intern belastningsutjämnare](#internallb)
- [Interna och externa belastningsutjämnare](#internalexternallb)

Service Fabric kan köras i en standard VM-skalningsuppsättning. Funktioner som du kan använda i en skalningsuppsättning för virtuell dator, som du kan använda med Service Fabric-kluster. Avsnitten nätverk med Azure Resource Manager-mallar för VM-skalningsuppsättningar och Service Fabric är identiska. När du distribuerar till ett befintligt virtuellt nätverk, är det enkelt att lägga till andra nätverksfunktioner som Azure ExpressRoute, Azure VPN Gateway, en nätverkssäkerhetsgrupp och vnet-peering.

Service Fabric är unik jämfört med andra funktioner i en aspekt. Den [Azure-portalen](https://portal.azure.com) internt använder Service Fabric-resursprovidern för att anropa till ett kluster för att få information om noder och program. Service Fabric-resursprovidern kräver offentligt tillgänglig ingående åtkomst till HTTP-gateway-porten (port 19080, som standard) på hanteringsslutpunkten. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) använder hanteringsslutpunkten för att hantera klustret. Service Fabric-resursprovidern använder också den här porten för att fråga efter information om ditt kluster ska visas i Azure-portalen. 

Om port 19080 inte kan nås från Service Fabric-resursprovidern, ett meddelande som *gick inte att hitta noder* visas i portalen och listan noden och programmet visas tom. Om du vill se ditt kluster i Azure-portalen belastningsutjämnaren måste exponera en offentlig IP-adress och nätverkssäkerhetsgruppen måste tillåta inkommande trafik på port 19080. Om din konfiguration inte uppfyller dessa krav visas inte status för ditt kluster i Azure-portalen.

## <a name="templates"></a>Mallar

Alla Service Fabric-mallar finns i [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Du ska kunna distribuera mallar som – är med hjälp av följande PowerShell-kommandon. Om du distribuerar den befintliga mallen i Azure Virtual Network eller statiska offentliga IP-mall, läser du först den [inledande installationen](#initialsetup) i den här artikeln.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Första installation

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

I följande exempel börjar vi med ett befintligt virtuellt nätverk med namnet ExistingRG mellan virtuella nätverk i den **ExistingRG** resursgrupp. Standard heter undernätet. Dessa standardresurser skapas när du använder Azure-portalen för att skapa en standard virtuell dator (VM). Du kan skapa virtuellt nätverk och undernät utan att skapa den virtuella datorn, men det huvudsakliga målet att lägga till ett kluster till ett befintligt virtuellt nätverk är att tillhandahålla nätverksanslutning till andra virtuella datorer. Skapa den virtuella datorn får ett bra exempel på hur ett befintligt virtuellt nätverk vanligtvis används. Om Service Fabric-klustret använder bara en intern belastningsutjämnare, utan en offentlig IP-adress, du kan använda den virtuella datorn och dess offentliga IP-Adressen som en säker *hoppa box*.

### <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

En statisk offentlig IP-adress är vanligtvis en dedikerade resurser som hanteras separat från den virtuella datorn eller virtuella datorer som den är tilldelad till. Den har etablerats i en dedikerad Nätverksresursgruppen (i motsats för att gruppera själva i Service Fabric-klusterresursen). Skapa en statisk offentlig IP-adress med namnet staticIP1 i samma ExistingRG resursgrupp, antingen i Azure portal eller med hjälp av PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric-mall

I exemplen i den här artikeln använder vi Service Fabric-template.json. Du kan använda guiden för standard-portalen för att hämta mallen från portalen innan du skapar ett kluster. Du kan också använda en av de [exempel på mallar](https://github.com/Azure-Samples/service-fabric-cluster-templates), till exempel den [säkra Service Fabric-kluster med fem noder](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Befintligt virtuellt nätverk eller undernät

1. Ändra parametern undernät med namnet på det befintliga undernätet och sedan lägga till två nya parametrar för att referera till det befintliga virtuella nätverket:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Kommentera ut `nicPrefixOverride` attribut för `Microsoft.Compute/virtualMachineScaleSets`, eftersom du använder befintliga undernät och du har inaktiverat den här variabeln i steg 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Ändra den `vnetID` variabeln så att den pekar till befintligt virtuellt nätverk:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Ta bort `Microsoft.Network/virtualNetworks` från dina resurser, så Azure skapar inte ett nytt virtuellt nätverk:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Kommentera ut det virtuella nätverket från den `dependsOn` attribut för `Microsoft.Compute/virtualMachineScaleSets`, så du förlita inte dig om hur du skapar ett nytt virtuellt nätverk:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Efter distributionen kan det virtuella nätverket ska innehålla den nya virtuella datorerna med skalningsuppsättningarna. VM scale set-nodtyp ska visa befintliga virtuellt nätverk och undernät. Du kan också använda Remote Desktop Protocol (RDP) att komma åt den virtuella dator som redan finns i det virtuella nätverket och för att pinga den nya skalan VM-skaluppsättning:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Ett annat exempel finns i [som inte är specifika för Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

1. Lägg till parametrar för namnet på den befintliga resursgruppen för statisk IP, namn och fullständigt kvalificerade domännamnet (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Ta bort den `dnsName` parametern. (Den statiska IP-adressen har redan en.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Lägg till en variabel för att referera till befintlig statisk IP-adress:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser, så Azure skapar inte en ny IP-adress:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Kommentera ut IP-adress från den `dependsOn` attribut för `Microsoft.Network/loadBalancers`, så du förlita inte dig om hur du skapar en ny IP-adress:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. I den `Microsoft.Network/loadBalancers` resursen, ändra den `publicIPAddress` element i `frontendIPConfigurations` att referera till den befintliga statisk IP-adressen i stället för en nyligen skapade:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. I den `Microsoft.ServiceFabric/clusters` resursen, ändra `managementEndpoint` till DNS-FQDN för den statiska IP-adressen. Om du använder ett säkert kluster, se till att du ändrar *http://* till *https://*. (Observera att det här steget gäller endast för Service Fabric-kluster. Om du använder en VM-skalningsuppsättning, hoppa över detta steg.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Du kan se att belastningsutjämnaren är bunden till offentliga statiska IP-adress från andra resursgruppen efter distributionen. Service Fabric-klientanslutningsslutpunkt och [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) endpoint-plats till DNS-FQDN för den statiska IP-adressen.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Endast intern belastningsutjämnare

Det här scenariot ersätter den externa belastningsutjämnaren i standardmallen för Service Fabric med en interna belastningsutjämnare. Konsekvenser för Azure-portalen och för Service Fabric-resursprovidern, finns i föregående avsnitt.

1. Ta bort den `dnsName` parametern. (Det krävs inte.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Om du använder en statisk tilldelningsmetod, kan du också lägga till parametern för en statisk IP-adress. Om du använder en dynamisk fördelning, behöver du inte utför det här steget.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser, så Azure skapar inte en ny IP-adress:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Ta bort IP-adressen `dependsOn` attribut för `Microsoft.Network/loadBalancers`, så du förlita inte dig om hur du skapar en ny IP-adress. Lägg till det virtuella nätverket `dependsOn` attributet eftersom belastningsutjämnaren nu beror på undernätet från det virtuella nätverket:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Ändra belastningsutjämnarens `frontendIPConfigurations` från med hjälp av en `publicIPAddress`, med ett undernät och `privateIPAddress`. `privateIPAddress` använder en fördefinierad statiska interna IP-adress. Om du vill använda en dynamisk IP-adress, ta bort den `privateIPAddress` element och ändrar sedan `privateIPAllocationMethod` till **dynamisk**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. I den `Microsoft.ServiceFabric/clusters` resursen, ändra `managementEndpoint` så att den pekar till den interna adressen för belastningsutjämnaren. Om du använder ett säkert kluster, se till att du ändrar *http://* till *https://*. (Observera att det här steget gäller endast för Service Fabric-kluster. Om du använder en VM-skalningsuppsättning, hoppa över detta steg.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Efter distributionen använder belastningsutjämnaren privata statiska 10.0.0.250 IP-adress. Om du har en annan dator i samma virtuella nätverk, går du till det interna [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) slutpunkt. Observera att den ansluter till en av noderna bakom belastningsutjämnaren.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interna och externa belastningsutjämnare

I det här scenariot du börjar med den befintliga externa belastningsutjämnaren på typ av nod och lägga till en intern belastningsutjämnare för samma nodtypen. En backend-port som är kopplade till en backend-adresspool kan endast tilldelas en enskild belastningsutjämnare. Välj vilka belastningsutjämnaren ska ha programmet-portar och vilka belastningsutjämnaren måste din hanteringsslutpunkter (portar 19000 och 19080). Om du placerar management-slutpunkter på den interna belastningsutjämnaren, Tänk på Service Fabric-resource provider begränsningar beskrivs tidigare i artikeln. I exemplet använder vi, hanteringen slutpunkter som finns kvar på den externa belastningsutjämnaren. Du också lägga till en port 80 programmet port och placera den på den interna belastningsutjämnaren.

En nodtyp finns på den externa belastningsutjämnaren i ett kluster med två nodtyp. Andra nodtypen används på den interna belastningsutjämnaren. Att använda ett kluster med två nodtyp, i portalen har skapat två nodtyp mallen (som levereras med två belastningsutjämnarna), växlar den andra belastningsutjämnaren till en intern belastningsutjämnare. Mer information finns i den [endast intern belastningsutjämnare](#internallb) avsnittet.

1. Lägg till parametern statiska intern Azure load balancer IP-adress. (Se tidigare avsnitt av den här artikeln för information om att använda en dynamisk IP-adress,.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Lägg till en parameter för program-port 80.

3. Lägg till interna versioner av den befintliga nätverk variabler, kopiera och klistra in dem, och lägga till ”-Int” namn:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Om du startar med den genererade portal mall som använder programmet port 80, portal standardmallen lägger till AppPort1 (port 80) på den externa belastningsutjämnaren. I det här fallet, ta bort AppPort1 från den externa belastningsutjämnaren `loadBalancingRules` och avsökningar, så du kan lägga till den till den interna belastningsutjämnaren:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Lägg till en andra `Microsoft.Network/loadBalancers` resurs. Den liknar den interna belastningsutjämnaren som skapats i den [endast intern belastningsutjämnare](#internallb) avsnittet, men den använder det ”-Int” läsa in belastningsutjämnare variabler och implementerar endast programport 80. Detta tar också bort `inboundNatPools`du vill behålla RDP-slutpunkter för offentliga belastningsutjämnare. Om du vill RDP på den interna belastningsutjämnaren måste du flytta `inboundNatPools` från externa belastningsutjämnaren till den här interna belastningsutjämnare:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. I `networkProfile` för den `Microsoft.Compute/virtualMachineScaleSets` resursen, lägga till den interna backend-adresspoolen:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Efter distributionen kan se du två belastningsutjämnare i resursgruppen. Du kan se de offentliga IP-adress och hantering av slutpunkterna (portar 19000 och 19080) tilldelas den offentliga IP-adressen om du bläddrar belastningsutjämnarna. Du kan också se den statiska interna IP-adress och programmet slutpunkten (port 80) tilldelade till den interna belastningsutjämnaren. Båda belastningsutjämnare använder samma VM scale set backend-pool.

## <a name="next-steps"></a>Nästa steg
[Skapa ett kluster](service-fabric-cluster-creation-via-arm.md)
