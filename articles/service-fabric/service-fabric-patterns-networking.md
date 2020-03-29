---
title: Nätverksmönster för Azure Service Fabric
description: Beskriver vanliga nätverksmönster för Service Fabric och hur du skapar ett kluster med hjälp av Azure-nätverksfunktioner.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466287"
---
# <a name="service-fabric-networking-patterns"></a>Nätverksmönster för service fabric
Du kan integrera ditt Azure Service Fabric-kluster med andra Azure-nätverksfunktioner. I den här artikeln visar vi hur du skapar kluster som använder följande funktioner:

- [Befintligt virtuellt nätverk eller befintligt undernät](#existingvnet)
- [Statisk offentlig IP-adress](#staticpublicip)
- [Belastningsutjämnad intern lastbalanserare](#internallb)
- [Intern och extern belastningsutjämnare](#internalexternallb)

Service Fabric körs i en standarduppsättning för virtuella datorer. Alla funktioner som du kan använda i en skalningsuppsättning för virtuella datorer kan du använda med ett Service Fabric-kluster. Nätverksavsnitten i Azure Resource Manager-mallarna för skalningsuppsättningar för virtuella datorer och Service Fabric är identiska. När du har distribuerat till ett befintligt virtuellt nätverk är det enkelt att införliva andra nätverksfunktioner, till exempel Azure ExpressRoute, Azure VPN Gateway, en nätverkssäkerhetsgrupp och virtuell nätverks peering.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Så att resursprovidern för Service Fabric kan fråga klustret

Service Fabric är unikt från andra nätverksfunktioner i en aspekt. [Azure-portalen](https://portal.azure.com) använder internt resursleverantören Service Fabric för att ringa till ett kluster för att få information om noder och program. Resursleverantören Service Fabric kräver allmänt tillgänglig inkommande åtkomst till HTTP-gatewayporten (port 19080, som standard) på hanteringsslutpunkten. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) använder hanteringsslutpunkten för att hantera klustret. Service Fabric-resursprovidern använder också den här porten för att fråga information om klustret, för att visas i Azure-portalen. 

Om port 19080 inte är tillgänglig från resursleverantören Service Fabric visas ett meddelande som Noder som *inte hittas* i portalen och noden och programlistan visas tomma. Om du vill se ditt kluster i Azure-portalen måste belastningsutjämningen visa en offentlig IP-adress och nätverkssäkerhetsgruppen måste tillåta inkommande port 19080-trafik. Om din konfiguration inte uppfyller dessa krav visar Azure-portalen inte status för klustret.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Mallar

Alla Service Fabric-mallar finns i [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Du bör kunna distribuera mallarna enligt följande PowerShell-kommandon. Om du distribuerar den befintliga Azure Virtual Network-mallen eller den statiska offentliga IP-mallen läser du först avsnittet [Inledande installation i](#initialsetup) den här artikeln.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Inledande installation

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

I följande exempel börjar vi med ett befintligt virtuellt nätverk med namnet ExistingRG-vnet i resursgruppen **ExistingRG.** Undernätet heter standard. Dessa standardresurser skapas när du använder Azure-portalen för att skapa en virtuell standarddator (VM). Du kan skapa det virtuella nätverket och undernätet utan att skapa den virtuella datorn, men huvudsyftet med att lägga till ett kluster i ett befintligt virtuellt nätverk är att tillhandahålla nätverksanslutning till andra virtuella datorer. Att skapa den virtuella datorn ger ett bra exempel på hur ett befintligt virtuellt nätverk vanligtvis används. Om service fabric-klustret bara använder en intern belastningsutjämnare, utan en offentlig IP-adress, kan du använda den virtuella datorn och dess offentliga IP som en säker *hoppruta*.

### <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

En statisk offentlig IP-adress är i allmänhet en dedikerad resurs som hanteras separat från den virtuella datorn eller virtuella datorer som den har tilldelats. Den etableras i en dedikerad nätverksresursgrupp (i motsats till i själva resursgruppen Service Fabric-klusterresurs). Skapa en statisk offentlig IP-adress med namnet staticIP1 i samma ExistingRG-resursgrupp, antingen i Azure-portalen eller med PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

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

### <a name="service-fabric-template"></a>Mall för Service Fabric

I exemplen i den här artikeln använder vi mallen Service Fabric.json. Du kan använda standardportalguiden för att hämta mallen från portalen innan du skapar ett kluster. Du kan också använda en av [exempelmallarna,](https://github.com/Azure-Samples/service-fabric-cluster-templates)till exempel det [säkra servicetyrklustret med fem noder](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Befintligt virtuellt nätverk eller befintligt undernät

1. Ändra parametern undernät till namnet på det befintliga undernätet och lägg sedan till två nya parametrar för att referera till det befintliga virtuella nätverket:

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

2. Kommentar `nicPrefixOverride` ut `Microsoft.Compute/virtualMachineScaleSets`attribut för , eftersom du använder befintliga undernät och du har inaktiverat den här variabeln i steg 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Ändra `vnetID` variabeln så att den pekar på det befintliga virtuella nätverket:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Ta `Microsoft.Network/virtualNetworks` bort från dina resurser, så att Azure inte skapar ett nytt virtuellt nätverk:

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

5. Kommentera det virtuella nätverket `dependsOn` från `Microsoft.Compute/virtualMachineScaleSets`attributet , så att du inte är beroende av att skapa ett nytt virtuellt nätverk:

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
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Efter distributionen bör det virtuella nätverket innehålla de nya virtuella skaluppsättningarna. Nodtypen för virtuell datorskala bör visa det befintliga virtuella nätverket och undernätet. Du kan också använda RDP (Remote Desktop Protocol) för att komma åt den virtuella datorn som redan fanns i det virtuella nätverket och pinga de nya skaluppsättningen virtuella datorer:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Ett annat exempel finns [i ett som inte är specifikt för Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

1. Lägg till parametrar för namnet på den befintliga statiska IP-resursgruppen, namnet och fullständigt kvalificerade domännamnet (FQDN):

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

2. Ta `dnsName` bort parametern. (Den statiska IP-adressen har redan en.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Lägg till en variabel som refererar till den befintliga statiska IP-adressen:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Ta `Microsoft.Network/publicIPAddresses` bort från dina resurser, så att Azure inte skapar en ny IP-adress:

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

5. Kommentera IP-adressen från `dependsOn` attributet , så att du inte är beroende av `Microsoft.Network/loadBalancers`att skapa en ny IP-adress:

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

6. I `Microsoft.Network/loadBalancers` resursen ändrar du elementet `publicIPAddress` `frontendIPConfigurations` för att referera till den befintliga statiska IP-adressen i stället för en nyskapade:

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

7. I `Microsoft.ServiceFabric/clusters` resursen `managementEndpoint` ändrar du till DNS FQDN för den statiska IP-adressen. Om du använder ett säkert kluster kontrollerar du att du ändrar *http://* till *https://*. (Observera att det här steget endast gäller för Service Fabric-kluster. Om du använder en skalningsuppsättning för virtuella datorer hoppar du över det här steget.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Efter distributionen kan du se att din belastningsutjämnare är bunden till den offentliga statiska IP-adressen från den andra resursgruppen. Slutpunkten för klientanslutningen för Service Fabric och [slutpunkten för Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pekar på DNS FQDN för den statiska IP-adressen.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Belastningsutjämnad intern lastbalanserare

Det här scenariot ersätter den externa belastningsutjämnaren i standardmallen Service Fabric med en intern belastningsutjämnare. Se [tidigare i artikeln](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) om konsekvenser för Azure-portalen och för resursleverantören Service Fabric.

1. Ta `dnsName` bort parametern. (Det behövs inte.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Om du använder en statisk allokeringsmetod kan du också lägga till en statisk IP-adressparameter. Om du använder en dynamisk allokeringsmetod behöver du inte göra det här steget.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Ta `Microsoft.Network/publicIPAddresses` bort från dina resurser, så att Azure inte skapar en ny IP-adress:

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

4. Ta bort `dependsOn` IP-adressattributet för , så att du inte är beroende av `Microsoft.Network/loadBalancers`att skapa en ny IP-adress. Lägg till `dependsOn` attributet för virtuellt nätverk eftersom belastningsutjämnaren nu beror på undernätet från det virtuella nätverket:

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

5. Ändra belastningsutjämnarens `frontendIPConfigurations` `publicIPAddress`inställning från att använda `privateIPAddress`ett , till att använda ett undernät och . `privateIPAddress`använder en fördefinierad statisk intern IP-adress. Om du vill använda en `privateIPAddress` dynamisk IP-adress tar du bort elementet och ändrar `privateIPAllocationMethod` sedan till **Dynamisk**.

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

6. I `Microsoft.ServiceFabric/clusters` resursen `managementEndpoint` ändrar du så att den pekar på den interna belastningsutjämnarens adress. Om du använder ett säkert kluster kontrollerar du att du ändrar *http://* till *https://*. (Observera att det här steget endast gäller för Service Fabric-kluster. Om du använder en skalningsuppsättning för virtuella datorer hoppar du över det här steget.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Efter distributionen använder din belastningsutjämnare den privata statiska 10.0.0.250 IP-adressen. Om du har en annan dator i samma virtuella nätverk kan du gå till den interna [slutpunkten för Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md) Observera att den ansluter till en av noderna bakom belastningsutjämnaren.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Intern och extern belastningsutjämnare

I det här fallet börjar du med den befintliga ennodtypen extern belastningsutjämnare och lägger till en intern belastningsutjämnare för samma nodtyp. En backend-port som är kopplad till en backend-adresspool kan endast tilldelas en enda belastningsutjämnare. Välj vilken belastningsutjämnare som ska ha dina programportar och vilken belastningsutjämnare som har hanteringsslutpunkterna (portarna 19000 och 19080). Om du placerar hanteringsslutpunkterna på den interna belastningsutjämnaren bör du tänka på de resursproviderbegränsningar för Tjänst fabric som beskrivs [tidigare i artikeln](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). I exemplet som vi använder finns hanteringsslutpunkterna kvar på den externa belastningsutjämnaren. Du kan också lägga till en port 80-programport och placera den på den interna belastningsutjämnaren.

I ett kluster av tvånodtyp finns en nodtyp på den externa belastningsutjämnaren. Den andra nodtypen finns på den interna belastningsutjämnaren. Om du vill använda ett kluster av tvånodtyp växlar du den andra belastningsutjämnaren till en intern belastningsutjämnare i mallen för portalen skapade tvånodtyp (som levereras med två belastningsutjämnare). Mer information finns i avsnittet [Intern belastningsutjämnad.](#internallb)

1. Lägg till den statiska ip-adressparametern för intern belastningsutjämning. (För anteckningar relaterade till att använda en dynamisk IP-adress finns i tidigare avsnitt i den här artikeln.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Lägg till en programport 80-parameter.

3. Så här lägger du till interna versioner av de befintliga nätverksvariablerna genom att kopiera och klistra in dem och lägger till "-Int" i namnet:

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

4. Om du börjar med den portalgenererade mallen som använder programport 80 läggs Standardportalmallen till AppPort1 (port 80) på den externa belastningsutjämnaren. I det här fallet tar du bort `loadBalancingRules` AppPort1 från den externa belastningsutjämnaren och avsökningarna, så att du kan lägga till den i den interna belastningsutjämnaren:

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

5. Lägg till `Microsoft.Network/loadBalancers` en andra resurs. Det liknar den interna belastningsutjämnaren som skapats i avsnittet [Intern belastningsutjämnad,](#internallb) men den använder variablerna "-Int" belastningsutjämnare och implementerar endast programporten 80. Detta tar `inboundNatPools`också bort , för att hålla RDP-slutpunkter på den offentliga belastningsutjämnaren. Om du vill ha RDP på `inboundNatPools` den interna belastningsutjämnaren flyttar du från den externa belastningsutjämnaren till den här interna belastningsutjämnaren:

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

6. I `networkProfile` för `Microsoft.Compute/virtualMachineScaleSets` resursen lägger du till den interna backend-adresspoolen:

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
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Efter distributionen kan du se två belastningsutjämnare i resursgruppen. Om du bläddrar i belastningsutjämnare kan du se de offentliga IP-adress- och hanteringsslutpunkterna (portarna 19000 och 19080) som tilldelats den offentliga IP-adressen. Du kan också se den statiska interna IP-adressen och programslutpunkten (port 80) som tilldelats den interna belastningsutjämnaren. Båda belastningsutjämnare använder samma virtuella datorskala som backend-pool.

## <a name="notes-for-production-workloads"></a>Anteckningar för produktionsarbetsbelastningar

Ovanstående GitHub-mallar är utformade för att fungera med standard-SKU för Azure Standard Load Balancer (SLB), Basic SKU. Detta SLB har inget SLA, så för produktionsarbetsbelastningar bör standard-SKU användas. Mer information om detta finns i [översikten över Azure Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview). Alla Service Fabric-kluster som använder Standard SKU för SLB måste se till att varje nodtyp har en regel som tillåter utgående trafik på port 443. Detta är nödvändigt för att slutföra klusterkonfigurationen och all distribution utan en sådan regel misslyckas. I ovanstående exempel på en "intern endast" belastningsutjämnare måste ytterligare en extern belastningsutjämnare läggas till i mallen med en regel som tillåter utgående trafik för port 443.

## <a name="next-steps"></a>Nästa steg
[Skapa ett kluster](service-fabric-cluster-creation-via-arm.md)

Efter distributionen kan du se två belastningsutjämnare i resursgruppen. Om du bläddrar i belastningsutjämnare kan du se de offentliga IP-adress- och hanteringsslutpunkterna (portarna 19000 och 19080) som tilldelats den offentliga IP-adressen. Du kan också se den statiska interna IP-adressen och programslutpunkten (port 80) som tilldelats den interna belastningsutjämnaren. Båda belastningsutjämnare använder samma virtuella datorskala som backend-pool.

