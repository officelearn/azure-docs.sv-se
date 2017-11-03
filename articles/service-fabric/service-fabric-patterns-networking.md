---
title: "Nätverk mönster för Azure Service Fabric | Microsoft Docs"
description: "Beskriver vanliga nätverk mönster för Service Fabric och hur du skapar ett kluster med hjälp av Azure nätverksfunktioner."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 535ea21a2c08be5f676ee24269b323a415b92607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric nätverk mönster
Du kan integrera Azure Service Fabric-kluster med andra funktioner för Azure. I den här artikeln hur vi du kan skapa kluster som använder följande funktioner:

- [Befintligt virtuellt nätverk eller undernät](#existingvnet)
- [Statiska offentliga IP-adressen](#staticpublicip)
- [Endast interna belastningsutjämnare](#internallb)
- [Interna och externa belastningsutjämnare](#internalexternallb)

Service Fabric körs i en standard virtuella datorns skaluppsättning. Funktioner som du kan använda i en skaluppsättning för virtuell dator, som du kan använda med ett Service Fabric-kluster. Avsnitten nätverk med Azure Resource Manager-mallar för virtuella datorer och Service Fabric är identiska. När du distribuerar till ett befintligt virtuellt nätverk, är det enklare att använda andra funktioner, t.ex. Azure ExpressRoute, Azure VPN-Gateway, en säkerhetsgrupp för nätverk och virtuella nätverk peering.

Service Fabric är unikt från andra nätverksfunktioner i en aspekt. Den [Azure-portalen](https://portal.azure.com) använder internt Service Fabric-resursprovidern för att ringa till ett kluster för att få information om noderna och program. Service Fabric-resursprovidern kräver offentligt tillgänglig inkommande åtkomst till HTTP-gateway-porten (port 19080, som standard) på management-slutpunkten. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) använder management-slutpunkten för att hantera klustret. Service Fabric-resursprovidern använder också den här porten för att begära information om ditt kluster ska visas i Azure-portalen. 

Om port 19080 inte är tillgänglig från Service Fabric-resursprovidern, ett meddelande som *noder hittades inte* visas på portalen och listan noden och programmet visas tomt. Om du vill se ditt kluster i Azure portal din belastningsutjämnare måste exponera en offentlig IP-adress och din nätverkssäkerhetsgruppen måste tillåta inkommande Porttrafik 19080. Om din konfiguration inte uppfyller dessa krav visas inte status för klustret i Azure-portalen.

## <a name="templates"></a>Mallar

Alla Service Fabric-mallar finns i [en nedladdning filen](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Du ska kunna distribuera mallar som-är med hjälp av följande PowerShell-kommandon. Om du distribuerar mallen befintliga Azure-nätverk eller statiska offentliga IP-mall, läser du först den [inledande installationen](#initialsetup) i den här artikeln.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Installationen

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

I följande exempel vi börjar med ett befintligt virtuellt nätverk med namnet ExistingRG-vnet, i den **ExistingRG** resursgruppen. Undernätet kallas standard. Dessa standardresurser skapas när du använder Azure-portalen för att skapa en standard virtuell dator (VM). Du kan skapa virtuellt nätverk och undernät utan att skapa den virtuella datorn, men det huvudsakliga målet med att lägga till ett kluster till ett befintligt virtuellt nätverk är att ge nätverksanslutning till andra virtuella datorer. Om du skapar den virtuella datorn får ett bra exempel på hur ett befintligt virtuellt nätverk normalt används. Om din Service Fabric-klustret använder endast en intern belastningsutjämnare, utan en offentlig IP-adress du kan använda den virtuella datorn och dess offentliga IP-Adressen som en säker *hoppa rutan*.

### <a name="static-public-ip-address"></a>Statiska offentliga IP-adressen

En statisk offentlig IP-adress är vanligtvis en dedikerad resurs som hanteras separat från virtuell dator eller virtuella datorer som den är tilldelad till. Det har etablerats i ett dedikerat nätverk resursgrupp (och inte för att gruppera i klusterresurs Service Fabric sig själv). Skapa en statisk offentlig IP-adress med namnet staticIP1 i samma ExistingRG resursgrupp, antingen i Azure-portalen eller med hjälp av PowerShell:

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

I exemplen i den här artikeln använder vi Service Fabric-template.json. Du kan använda guiden för standard-portal för att hämta mallen från portalen innan du skapar ett kluster. Du kan också använda en av mallar i den [mallgalleriet](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), till exempel den [Service Fabric-kluster med fem noder](https://azure.microsoft.com/en-us/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Befintligt virtuellt nätverk eller undernät

1. Ändra parametern undernät med namnet på det befintliga undernätet och sedan lägga till två nya parametrar för att referera till det befintliga virtuella nätverket:

    ```
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


2. Ändra den `vnetID` variabeln för att peka på ett befintligt virtuellt nätverk:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Ta bort `Microsoft.Network/virtualNetworks` från dina resurser, så Azure skapar inte ett nytt virtuellt nätverk:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
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

4. Kommentera det virtuella nätverket från den `dependsOn` attribut för `Microsoft.Compute/virtualMachineScaleSets`, så att du inte beror på hur du skapar ett nytt virtuellt nätverk:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Efter distributionen kan det virtuella nätverket ska inkludera den nya skaluppsättning för virtuella datorer. Virtuella scale set nodtypen ska visa befintliga virtuella nätverk och undernät. Du kan också använda Remote Desktop Protocol (RDP) att komma åt den virtuella datorn som redan finns i det virtuella nätverket och för att pinga den nya skalan ange virtuella datorer:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Ett annat exempel är finns [ett som inte är specifika för Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statiska offentliga IP-adressen

1. Lägg till parametrar för namnet på den befintliga resursgruppen för statisk IP-, namn och fullständigt kvalificerade domännamnet (FQDN):

    ```
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

2. Ta bort den `dnsName` parameter. (Den statiska IP-adressen har redan en.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Lägg till en variabel för att referera till befintlig statisk IP-adress:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser, så Azure skapar inte en ny IP-adress:

    ```
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

5. Kommentera ut IP-adress från den `dependsOn` attribut för `Microsoft.Network/loadBalancers`, så att du inte beror på hur du skapar en ny IP-adress:

    ```
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

6. I den `Microsoft.Network/loadBalancers` resurs, ändra den `publicIPAddress` element av `frontendIPConfigurations` att referera till befintlig statisk IP-adress i stället för en nyligen skapade:

    ```
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

7. I den `Microsoft.ServiceFabric/clusters` resurs, ändra `managementEndpoint` till DNS-FQDN för den statiska IP-adressen. Om du använder en säker kluster, kontrollerar du att du ändrar *http://* till *https://*. (Observera att det här steget gäller endast Service Fabric-kluster. Om du använder en skaluppsättning för virtuell dator kan du hoppa över det här steget.)

    ```
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

Efter distributionen kan se du att din belastningsutjämning är kopplad till den offentliga statiska IP-adressen i resursgruppen. Service Fabric-klienten Anslutningens slutpunkt och [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) endpoint peka DNS FQDN för den statiska IP-adressen.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Endast interna belastningsutjämnare

Det här scenariot ersätter den externa belastningsutjämnaren i standardmallen för Service Fabric med en endast är interna belastningsutjämnare. Konsekvenser för Azure-portalen och för Service Fabric-resursprovidern finns i föregående avsnitt.

1. Ta bort den `dnsName` parameter. (Det krävs inte.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Alternativt kan du använda en statisk tilldelningsmetod kan du lägga till parametern för en statisk IP-adress. Du behöver inte göra det här steget om du använder en dynamisk fördelning.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser, så Azure skapar inte en ny IP-adress:

    ```
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

4. Ta bort IP-adressen `dependsOn` attribut för `Microsoft.Network/loadBalancers`, så att du inte beror på hur du skapar en ny IP-adress. Lägg till det virtuella nätverket `dependsOn` attributet eftersom belastningsutjämnaren nu beror på undernätet från det virtuella nätverket:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Ändra belastningsutjämnarens `frontendIPConfigurations` från med hjälp av en `publicIPAddress`, med ett undernät och `privateIPAddress`. `privateIPAddress`använder en fördefinierad statiska interna IP-adress. Om du vill använda en dynamisk IP-adress, ta bort den `privateIPAddress` element och ändrar sedan `privateIPAllocationMethod` till **dynamiska**.

    ```
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

6. I den `Microsoft.ServiceFabric/clusters` resurs, ändra `managementEndpoint` så att den pekar till den interna adressen för belastningsutjämnaren. Om du använder en säker kluster, se till att du ändrar *http://* till *https://*. (Observera att det här steget gäller endast Service Fabric-kluster. Om du använder en skaluppsättning för virtuell dator kan du hoppa över det här steget.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Distribuera mallen:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Efter distributionen kan använder din belastningsutjämnare privata statiska 10.0.0.250 IP-adress. Om du har en annan dator i samma virtuella nätverk, går du till det interna [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) slutpunkt. Observera att det ansluter till en av noderna bakom belastningsutjämnaren.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interna och externa belastningsutjämnare

I detta scenario börjar med den befintliga externa nod typ belastningsutjämnaren och Lägg till en intern belastningsutjämnare för samma nodtypen. En backend-port som är kopplad till en backend-adresspool kan endast tilldelas en enda belastningsutjämnare. Välj vilka belastningsutjämnare ska ha application-portar och vilka belastningsutjämnaren har dina hanteringsslutpunkter (portar 19000 och 19080). Om du placerar management-slutpunkter på den interna belastningsutjämnaren Kom ihåg Service Fabric-resource provider begränsningar som beskrivs tidigare i artikeln. I exemplet använder vi, hanteringen slutpunkter finns kvar på den externa belastningsutjämnaren. Du också lägga till en port 80 programmet port och placera den på den interna belastningsutjämnaren.

En nodtyp finns på den externa belastningsutjämnaren i ett kluster med två nodtypen. Andra nodtyp finns på den interna belastningsutjämnaren. Om du vill använda ett kluster med två nodtyp i portalen skapas två nodtypen mallen (som levereras med två belastningsutjämnare) växla andra belastningsutjämnaren till en intern belastningsutjämnare. Mer information finns i [endast interna belastningsutjämnare](#internallb) avsnitt.

1. Lägg till parametern statiska interna belastningen belastningsutjämnaren IP-adress. (Se tidigare avsnitt i den här artikeln för information om att använda en dynamisk IP-adress,.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Lägg till ett program port 80-parameter.

3. Lägg till interna versioner av den befintliga nätverk variabler, kopiera och klistra in dem, och lägga till ”-Int” till namnet:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Om du börjar med den portal-genererade mall som använder programmet port 80 portal standardmallen lägger till AppPort1 (port 80) på den externa belastningsutjämnaren. I så fall bort AppPort1 från den externa belastningsutjämnaren `loadBalancingRules` och avsökningar, så du kan lägga till den interna belastningsutjämnaren:

    ```
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

5. Lägga till ett andra `Microsoft.Network/loadBalancers` resurs. Det liknar den interna belastningsutjämnaren som skapats i den [endast interna belastningsutjämnare](#internallb) avsnittet, men den används i ”-Int” läsa in belastningsutjämning variabler och implementerar endast programmet port 80. Detta tar också bort `inboundNatPools`, för att hålla RDP-slutpunkter för offentliga belastningsutjämnare. Om du vill RDP på den interna belastningsutjämnaren flyttar `inboundNatPools` från externa belastningsutjämnaren till den här interna belastningsutjämnare:

    ```
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

6. I `networkProfile` för den `Microsoft.Compute/virtualMachineScaleSets` resursen, Lägg till den interna backend-adresspoolen:

    ```
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

Efter distributionen kan se du två belastningsutjämnare i resursgruppen. Om du bläddrar belastningsutjämnare kan du se de offentliga IP-adress och hantering av slutpunkter (portar 19000 och 19080) tilldelade till den offentliga IP-adressen. Du kan också se statiska interna IP-adress och programmet slutpunkten (port 80) tilldelas den interna belastningsutjämnaren. Både belastningsutjämnare använder samma virtuella scale set backend-pool.

## <a name="next-steps"></a>Nästa steg
[Skapa ett kluster](service-fabric-cluster-creation-via-arm.md)
