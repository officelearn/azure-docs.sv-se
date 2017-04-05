---
title: "Ansluta ett virtuellt Azure-nätverk till ett annat VNet: PowerShell | Microsoft Docs"
description: "Den här artikeln visar hur du ansluter virtuella nätverk tillsammans med hjälp av Azure Resource Manager och PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a4cbc4cd1c48da1120c643892b19692ac583d4c3
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>Konfigurera en VNet-till-VNet-anslutning med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Klassisk – Klassisk portal](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
>

Den här artikeln beskriver steg för steg hur du skapar en anslutning mellan virtuella nätverk (VNet) med Resource Manager-distributionsmodellen med hjälp av VPN Gateway. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer.

![v2v-diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Distributionsmodeller och -metoder för anslutningar mellan virtuella nätverk
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för VNet-till-VNet-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen. 

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet-peering**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar
Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder Azures VPN-gateway för att få en säker tunnel med IPsec/IKE. De VNets du ansluter till kan finnas i olika regioner. Eller i olika prenumerationer. Du kan till och med kombinera VNet-till-VNet-kommunikation med konfigurationer för flera platser. Därmed kan du etablera nätverkstopologier som kombinerar anslutningar mellan olika anläggningar med virtuell nätverksanslutning enligt följande diagram:

![Om anslutningar](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>Varför ska man ansluta virtuella nätverk?
Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**
  
  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Traffic Manager och Load Balancer kan du konfigurera arbetsbelastning med hög tillgänglighet och geografisk redundans över flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regionala flernivåprogram med isolering eller administrativa gränser**
  
  * Inom samma region kan du konfigurera flernivåprogram med flera virtuella nätverk som är anslutna till varandra på grund av isolering eller administrativa krav.

### <a name="vnet-to-vnet-considerations"></a>Att tänka på när du använder VNet-till-VNet
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>Vilka steg ska jag använda?
I den här artikeln beskrivs två uppsättningar med steg. En uppsättning steg för [virtuella nätverk som finns i samma prenumeration](#samesub) och en annan för [virtuella nätverk som finns i olika prenumerationer](#difsub). Den viktigaste skillnaden mellan uppsättningarna är huruvida du kan skapa och konfigurera alla virtuella nätverk och gateway-resurser i samma PowerShell-session.

I stegen i den här artikeln används variabler som deklareras i början av varje avsnitt. Om du redan arbetar med befintliga virtuella nätverk kan du ändra variablerna så att de avspeglar din miljö. 

![Båda anslutningarna](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="samesub"></a>Så här ansluter du VNets som finns i samma prenumeration
![v2v-diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Innan du börjar
Innan du börjar måste du installera PowerShell-cmdlets för Azure Resource Manager. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="Step1"></a>Steg 1 – Planera dina IP-adressintervall
I följande steg ska vi skapa två virtuella nätverk och deras respektive gateway-undernät och konfigurationer. Sedan ska vi skapa en VPN-anslutning mellan de två virtuella nätverken. Det är viktigt att planera IP-adressintervallen för nätverkskonfigurationen. Tänk på att inga av dina VNet-intervall eller lokala nätverksintervall får överlappa varandra på något sätt.

Vi använder följande värden i exemplen:

**Värden för TestVNet1:**

* VNet-namn: TestVNet1
* Resursgrupp: TestRG1
* Plats: Östra USA
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* DNS-server: 8.8.8.8
* GatewayName: VNet1GW
* Offentlig IP: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5
* ConnectionType: VNet2VNet

**Värden för TestVNet4:**

* VNet-namn: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Resursgrupp: TestRG4
* Plats: Västra USA
* DNS-server: 8.8.8.8
* GatewayName: VNet4GW
* Offentlig IP: VNet4GWIP
* VPNType: RouteBased
* Anslutning: VNet4toVNet1
* ConnectionType: VNet2VNet

### <a name="Step2"></a>Steg 2 – Skapa och konfigurera TestVNet1
1. Deklarera dina variabler
   
    Börja med att deklarera variabler. I det här exemplet deklarerar vi variablerna med värdena för den här övningen. I de flesta fall bör du ersätta värdena med dina egna. Du kan dock använda dessa variabler om du bara vill följa anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna om det behövs och kopiera och klistra in dem i PowerShell-konsolen.
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. Ansluta till din prenumeration
   
    Växla till PowerShell-läget för att använda Resource Manager-cmdlets. Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:
   
        Login-AzureRmAccount
   
    Kontrollera prenumerationerna för kontot.
   
        Get-AzureRmSubscription 
   
    Ange den prenumeration som du vill använda.
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. Skapa en ny resursgrupp
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. Skapa undernätskonfigurationerna för TestVNet1
   
    I det här exemplet skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät – GatewaySubnet, FrontEnd och BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen. 
   
    I följande exempel används variablerna som du angav tidigare. I det här exemplet använder gateway-undernätet en /27. Även om det är möjligt att skapa ett gateway-subnät som är så litet som /29 så rekommenderar vi att du skapar ett större subnät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden. 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. Skapa TestVNet1
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. Begär en offentlig IP-adress
   
    Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för det virtuella nätverket. Observera att AllocationMethod är Dynamic. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till gatewayen. 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. Skapa gateway-konfigurationen
   
    Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd exemplet för att skapa gateway-konfigurationen. 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. Skapa gatewayen för TestVNet1
   
    I det här steget ska du skapa VNet-gatewayen för TestVNet1. VNet-till-VNet-konfigurationer kräver VpnType RouteBased. Det kan ta en stund att skapa en gateway (45 minuter eller mer).
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>Steg 3 – Skapa och konfigurera TestVNet4
När du har konfigurerat TestVNet1 skapar du TestVNet4. Följ stegen nedan och ersätt värdena med dina egna vid behov. Det här steget kan göras inom samma PowerShell-session, eftersom den är i samma prenumeration.

1. Deklarera dina variabler
   
    Ersätt värdena med de som du vill använda för din konfiguration.
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1.
2. Skapa en ny resursgrupp
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. Skapa undernätskonfigurationerna för TestVNet4
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. Skapa TestVNet4
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. Begär en offentlig IP-adress
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. Skapa gateway-konfigurationen
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. Skapa TestVNet4-gatewayen
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Steg 4 – Ansluta dina gateways
1. Hämta båda virtuella nätverksgatewayerna
   
    I det här exemplet, eftersom båda gatewayerna finns i samma prenumeration, kan det här steget slutföras i samma PowerShell-session.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. Skapa TestVNet1-till-TestVNet4-anslutningen
   
    I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet4. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. Skapa TestVNet4-till-TestVNet1-anslutningen
   
    Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet4 till TestVNet1. Kontrollera att de delade nycklarna matchar.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    Anslutningen bör vara upprättad efter några minuter.
4. Verifiera anslutningen. Mer information finns i avsnittet [Verifiera anslutningen](#verify).

## <a name="difsub"></a>Så här ansluter du VNets som finns i olika prenumerationer
![v2v-diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

I det här scenariot ansluter vi TestVNet1 och TestVNet5. TestVNet1 och TestVNet5 finns i olika prenumerationer. I stegen för den här konfigurationen lägger vi till ytterligare en VNet-till-VNet-anslutning för att kunna ansluta TestVNet1 till TestVNet5. 

Skillnaden är att några av konfigurationsstegen måste utföras i en separat PowerShell-session för den andra prenumerationen. Detta gäller särskilt om två prenumerationer tillhör olika organisationer. 

Anvisningarna fortsätter från föregående steg ovan. Du måste slutföra [Steg 1](#Step1) och [Steg 2](#Step2) för att kunna skapa och konfigurera TestVNet1 och VPN-gatewayen för TestVNet1. När du har slutfört steg 1 och steg 2 fortsätter du med steg 5 för att skapa TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Steg 5 – Kontrollera ytterligare IP-adressintervall
Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet5, inte överlappar några av dina VNet-intervall eller lokala intervall för nätverksgatewayen. 

I det här exemplet kan de virtuella nätverken tillhöra olika organisationer. Använd följande VNet-värden för TestVNet5 i den här övningen:

**Värden för TestVNet5:**

* VNet-namn: TestVNet5
* Resursgrupp: TestRG5
* Plats: Östra Japan
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* DNS-server: 8.8.8.8
* GatewayName: VNet5GW
* Offentlig IP: VNet5GWIP
* VPNType: RouteBased
* Anslutning: VNet5toVNet1
* ConnectionType: VNet2VNet

**Ytterligare värden för TestVNet1:**

* Anslutning: VNet1toVNet5

### <a name="step-6---create-and-configure-testvnet5"></a>Steg 6 – Skapa och konfigurera TestVNet5
Det här steget måste utföras i den nya prenumerationen. Den här delen kan utföras av administratören i en annan organisation som äger prenumerationen.

1. Deklarera dina variabler
   
    Ersätt värdena med de som du vill använda för din konfiguration.
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. Anslut till Prenumeration 5
   
    Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:
   
        Login-AzureRmAccount
   
    Kontrollera prenumerationerna för kontot.
   
        Get-AzureRmSubscription 
   
    Ange den prenumeration som du vill använda.
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. Skapa en ny resursgrupp
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. Skapa undernätskonfigurationerna för TestVNet4
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. Skapa TestVNet5
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. Begär en offentlig IP-adress
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. Skapa gateway-konfigurationen
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. Skapa TestVNet5-gatewayen
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>Steg 7 – Ansluta gateways
I det här exemplet där gatewayerna finns i olika prenumerationer, har vi delat upp steget i två PowerShell-sessioner som kallas för [Prenumeration 1] och [Prenumeration 5].

1. **[Prenumeration 1]** Hämta den virtuella nätverksgatewayen för Prenumeration 1
   
    Kontrollera att du har loggat in och anslutit till Prenumeration 1.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    Kopiera utdatan från följande element och skicka dem till administratören för Prenumeration 5 via e-post eller någon annan metod.
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    Dessa två element har värden som liknar följande exempelutdata:
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[Prenumeration 5]** Hämta den virtuella nätverksgatewayen för Prenumeration 5
   
    Kontrollera att du har loggat in och anslutit till Prenumeration 5.
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    Kopiera utdatan från följande element och skicka dem till administratören för Prenumeration 1 via e-post eller någon annan metod.
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    Dessa två element har värden som liknar följande exempelutdata:
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[Prenumeration 1]** Skapa TestVNet1-till-TestVNet5-anslutningen
   
    I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet5. Skillnaden här är att $vnet5gw inte kan hämtas direkt, eftersom den finns i en annan prenumeration. Du måste skapa ett nytt PowerShell-objekt med värdena från Prenumeration 1 i stegen ovan. Använd exemplet nedan. Ersätt namnet, ID:t och den delade nyckeln med dina värden. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.
   
    Kontrollera att du ansluter till Prenumeration 1. 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[Prenumeration 5]** Skapa TestVNet5-till-TestVNet1-anslutningen
   
    Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet5 till TestVNet1. Samma process att skapa ett PowerShell-objekt som baseras på de värden som erhållits från Prenumeration 1 gäller även här. Var noga med att de delade nycklarna matchar i det här steget.
   
    Kontrollera att du ansluter till Prenumeration 5.
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Så här verifierar du en anslutning
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [dokumentationen för Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md). 


