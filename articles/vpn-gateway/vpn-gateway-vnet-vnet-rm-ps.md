<properties
   pageTitle="Skapa en VPN Gateway-anslutning från VNet till VNet med Azure Resource Manager och PowerShell för VNets | Microsoft Azure"
   description="Den här artikeln visar hur du ansluter virtuella nätverk tillsammans med hjälp av Azure Resource Manager och PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="cherylmc"/>

# Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell

> [AZURE.SELECTOR]
- [Klassisk Azure-portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell – Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Den här artikeln visar dig hur du skapar en anslutning mellan virtuella nätverk med hjälp av Resource Manager-distributionsmodellen och PowerShell. De virtuella nätverken kan finnas i samma eller olika regioner, från samma eller olika prenumerationer.


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Distributionsmodeller och verktyg för VNet-till-VNet**

En VNet-till-VNet-anslutning kan konfigureras i båda distributionsmodellerna och med hjälp av flera olika verktyg. Se tabellen nedan för mer information. Vi uppdaterar tabellen när nya artiklar, nya distributionsmodeller och fler verktyg blir tillgängliga för den här konfigurationen. När det finns en artikel länkar vi till den direkt från tabellen.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## Om VNet-till-VNet-anslutningar

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) liknar att ansluta ett VNet till en lokal plats. Båda typerna av anslutning använder Azures VPN-gateway för att få en säker tunnel med IPsec/IKE. De VNets du ansluter till kan finnas i olika regioner. Eller i olika prenumerationer. Du kan till och med kombinera VNet-till-VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intervirtuell nätverksanslutning, enligt diagrammet nedan. 


![Om anslutningar](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

- **Geografisk redundans i flera regioner och geografisk närvaro**
    - Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
    - Med Azure Traffic Manager och Load Balancer kan du konfigurera arbetsbelastning med hög tillgänglighet och geografisk redundans över flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.

- **Regionala flernivåprogram med isolering eller administrativa gränser**
    - Inom samma region kan du konfigurera flernivåprogram med flera virtuella nätverk som är anslutna till varandra på grund av isolering eller administrativa krav.


### Vanliga frågor och svar om VNet-till-VNet

- De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).

- En molntjänst eller en slutpunkt för belastningsutjämning KAN INTE omfatta flera virtuella nätverk, även om de är sammankopplade.

- Att ansluta flera virtuella Azure-nätverk tillsammans kräver inte några lokala VPN-gatewayer, såvida inte en anslutning mellan flera platser krävs.

- VNet-till-VNet stöder anslutning av virtuella nätverk. Det stöder INTE anslutning av virtuella datorer eller molntjänster i ett virtuellt nätverk.

- VNet-till-VNet kräver Azure VPN-gatewayer med VPN-typen RouteBased (tidigare kallad dynamisk routning). 

- En virtuell nätverksanslutning kan användas samtidigt med VPN för flera platser, med högst 10 (standard-gateways) eller 30 (gateways med hög kapacitet) VPN-tunnlar för en VPN-gateway i ett virtuellt nätverk, som antingen ansluter till andra virtuella nätverk eller till lokala platser.

- Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adressutrymmen kommer att innebära att VNet-till-VNet-anslutningarna inte skapas.

- Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.

- Alla VPN-tunnlar i det virtuella nätverket delar på den tillgängliga bandbredden i Azures VPN-gateway och samma VPN-gateways upptids-SLA i Azure.

- VNet-till-VNet-trafik skickas via Microsoft Network, inte Internet.

- VNet-till-VNet-trafik i samma region är kostnadsfri i båda riktningarna. VNet-till-VNets utgående trafik i flera regioner debiteras med priset på utgående dataöverföring mellan virtuella nätverk, baserat på källregionerna. Se [sidan med prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) för mer information.


## Vilka steg ska jag använda?

I den här artikeln visas olika uppsättningar med åtgärder som gäller för VNets som skapats med hjälp av Resource Manager-distributionsmodellen. Vilka VNet-till-VNet-konfigurationssteg som används beror på om dina VNets finns i samma prenumeration eller i olika prenumerationer. 

![Båda anslutningarna](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Den stora skillnaden mellan de båda konfigurationsstegen är om du kan skapa och konfigurera alla virtuella nätverk och gateway-resurser inom samma PowerShell-session. Det här dokumentet innehåller anvisningar för båda. I bilden ovan visas både en VNet-till-VNet-anslutning med samma prenumeration och en VNet-till-VNet-anslutning över flera prenumerationer. 


- [VNets som finns i samma prenumeration](#samesub)
- [VNets som finns i olika prenumerationer](#difsub)


## <a name="samesub"></a>Så här ansluter du VNets som finns i samma prenumeration

Den här konfigurationen gäller virtuella nätverk som finns i samma prenumeration, enligt diagrammet nedan:

![VNet2VNet i samma prenumeration](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Innan du börjar

- Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Du måste installera Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).

### <a name="Step1"></a>Steg 1 – Planera dina IP-adressintervall


Det är viktigt att bestämma vilka intervall du ska använda för att konfigurera din nätverkskonfiguration. Tänk på att inga av dina VNet-intervall eller lokala nätverksintervall får överlappa varandra på något sätt.

I stegen nedan skapar vi två virtuella nätverk tillsammans med respektive gateway-undernät och konfigurationer. Sedan skapar vi en VPN Gateway-anslutning mellan två VNets.

Använd följande VNet-värden för den här övningen:

**Värden för TestVNet1:**

- VNet-namn: TestVNet1
- Resursgrupp: TestRG1
- Plats: Östra USA
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS-server: 8.8.8.8
- GatewayName: VNet1GW
- Offentlig IP: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Värden för TestVNet4:**

- VNet-namn: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- Resursgrupp: TestRG4
- Plats: Västra USA
- DNS-server: 8.8.8.8
- GatewayName: VNet4GW
- Offentlig IP: VNet4GWIP
- VPNType: RouteBased
- Anslutning: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Steg 2 – Skapa och konfigurera TestVNet1

1. Deklarera dina variabler

    I den här övningen börjar vi med att deklarera våra variabler. I exemplet nedan deklarerar vi variablerna med de värden som gäller för den här övningen. Se till att ersätta värdena med dina egna när du konfigurerar för produktion. Du kan använda dessa variabler om du använder anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna samt kopiera och klistra in dem i PowerShell-konsolen.

        $Sub1          = "Replace_With_Your_Subcription_Name"
        $RG1           = "TestRG1"
        $Location1     = "East US"
        $VNetName1     = "TestVNet1"
        $FESubName1    = "FrontEnd"
        $BESubName1    = "Backend"
        $GWSubName1    = "GatewaySubnet"
        $VNetPrefix11  = "10.11.0.0/16"
        $VNetPrefix12  = "10.12.0.0/16"
        $FESubPrefix1  = "10.11.0.0/24"
        $BESubPrefix1  = "10.12.0.0/24"
        $GWSubPrefix1  = "10.12.255.0/27"
        $DNS1          = "8.8.8.8"
        $GWName1       = "VNet1GW"
        $GWIPName1     = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14  = "VNet1toVNet4"
        $Connection15  = "VNet1toVNet5"

2. Anslut till Prenumeration 1

    Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

    Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

        Login-AzureRmAccount

    Kontrollera prenumerationerna för kontot.

        Get-AzureRmSubscription 

    Ange den prenumeration som du vill använda.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Skapa en ny resursgrupp

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Skapa undernätskonfigurationerna för TestVNet1

    Exemplet nedan skapar ett virtuellt nätverk med namnet TestVNet1 och tre undernät, där ett kallas GatewaySubnet, ett kallas FrontEnd och ett kallas BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen. 

    I exemplet nedan använder vårt gateway-undernät en /27. Även om du tekniskt sett kan skapa ett gateway-undernät med ett undernät så litet som en /29, rekommenderar vi inte att göra detta. Vi rekommenderar att du använder något större, till exempel en /27 eller /26, för att kunna utnyttja befintliga eller framtida konfigurationer som kan kräva ett större gateway-undernät. 


        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Skapa TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Begär en offentlig IP-adress

    Därefter begär du en offentlig IP-adress som ska allokeras till den gateway du skapar för ditt VNet. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till din gateway. I nästa konfigurationsavsnitt ska du använda denna IP-adress. Använd exemplet nedan. Allokeringsmetoden för den här adressen måste vara dynamisk. 

        $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Skapa gateway-konfigurationen

    Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd exemplet nedan för att skapa din gateway-konfiguration. 

        $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Skapa gatewayen för TestVNet1

    I det här steget ska du skapa den virtuella nätverksgatewayen för TestVNet1. VNet-till-VNet-konfigurationer kräver VpnType RouteBased. Att skapa en gateway kan ta ett tag (30 minuter eller mer att slutföra).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Steg 3 – Skapa och konfigurera TestVNet4

När du har konfigurerat TestVNet1, måste du upprepa stegen för att skapa TestVNet4. Följ stegen nedan och ersätt värdena med dina egna vid behov. Det här steget kan göras inom samma PowerShell-session, eftersom den är i samma prenumeration.

1. Deklarera dina variabler

    Ersätt värdena med de som du vill använda för din konfiguration.

        $RG4           = "TestRG4"
        $Location4     = "West US"
        $VnetName4     = "TestVNet4"
        $FESubName4    = "FrontEnd"
        $BESubName4    = "Backend"
        $GWSubName4    = "GatewaySubnet"
        $VnetPrefix41  = "10.41.0.0/16"
        $VnetPrefix42  = "10.42.0.0/16"
        $FESubPrefix4  = "10.41.0.0/24"
        $BESubPrefix4  = "10.42.0.0/24"
        $GWSubPrefix4  = "10.42.255.0/27"
        $DNS4          = "8.8.8.8"
        $GWName4       = "VNet4GW"
        $GWIPName4     = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41  = "VNet4toVNet1"

    Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1.

2. Skapa en ny resursgrupp

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Skapa undernätskonfigurationerna för TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Skapa TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Begär en offentlig IP-adress

        $gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Skapa gateway-konfigurationen

        $vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Skapa TestVNet4-gatewayen

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Steg 4 – Ansluta gateways

1. Hämta båda virtuella nätverksgatewayerna

    I det här exemplet, eftersom båda gatewayerna finns i samma prenumeration, kan det här steget slutföras i samma PowerShell-session.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Skapa TestVNet1-till-TestVNet4-anslutningen

    I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet4. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Skapa TestVNet4-till-TestVNet1-anslutningen

    Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet4 till TestVNet1. Kontrollera att de delade nycklarna matchar.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    Anslutningen bör vara upprättad efter några minuter.

## <a name="Verify"></a>Så här kontrollerar du en VNet-till-VNet-anslutning

Exemplen nedan visar hur du verifierar anslutningen. Var noga med att ändra till värden som matchar din miljö.

### Så här verifierar du din anslutning med Azure-portalen

Du kan verifiera en VPN-anslutning i Azure Portal genom att gå till **Virtuella nätverksgatewayer** -> **klicka på ditt gatewaynamn** -> **Inställningar** -> **Anslutningar**. När du väljer namnet på anslutningen ser du mer information på bladet **Anslutning**. 


### Så här verifierar du din anslutning med PowerShell

Det går också att kontrollera att anslutningen har utförts genom att använda *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Du kan använda följande exempel och ändra värdena till dina egna. Välj A när du uppmanas, för att köra Alla.

    Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Bläddra för att se värdena efter att cmdleten har slutförts. I exemplet på PowerShell-resultat nedan visas anslutningsstatusen som *Ansluten* och du kan se ingående och utgående byte.



    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    LocalNetworkGateway2       :
    Peer                       :
    ConnectionType             : Vnet2Vnet
    RoutingWeight              : 0
    SharedKey                  : AzureA1b2C3
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 1376
    IngressBytesTransferred    : 1232
    ProvisioningState          : Succeeded
    VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
    VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
    LocalNetworkGateway2Text   :
    PeerText                   :
    ResourceGroupName          : TestRG1
    Location                   : eastus
    ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
    Tag                        : {}
    TagsTable                  :
    Name                       : VNet1toVNet4
    Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>Så här ansluter du VNets som finns i olika prenumerationer

Konfigurationsstegen nedan lägger till ytterligare en VNet-till-VNet-anslutning för att ansluta TestVNet1 till TestVNet5, som finns i en annan prenumeration. Skillnaden är att en del av konfigurationsstegen måste utföras i en separat PowerShell-session i den andra prenumerationen, särskilt om de två prenumerationerna tillhör olika organisationer. Den resulterande konfigurationen visas i diagrammet nedan när du slutfört stegen:

![VNet2VNet i olika prenumerationer](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Anvisningarna nedan fortsätter från föregående steg ovan. Du måste slutföra [Steg 1](#Step1) och [Steg 2](#Step2) för att kunna skapa och konfigurera TestVNet1 och VPN Gateway för TestVNet1. Om du planerar att endast ansluta VNets i olika prenumerationer, behöver du inte göra steg 3 och 4 från föregående övning, utan du kan gå vidare till steg 5 nedan. 

### Steg 5 – Kontrollera ytterligare IP-adressintervall

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet5, inte överlappar några av dina VNet-intervall eller lokala intervall för nätverksgatewayen. 

I det här exemplet kan de virtuella nätverken tillhöra olika organisationer. Använd följande VNet-värden för TestVNet5 i den här övningen:

**Värden för TestVNet5:**

- VNet-namn: TestVNet5
- Resursgrupp: TestRG5
- Plats: Östra Japan
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS-server: 8.8.8.8
- GatewayName: VNet5GW
- Offentlig IP: VNet5GWIP
- VPNType: RouteBased
- Anslutning: VNet5toVNet1
- ConnectionType: VNet2VNet

**Ytterligare värden för TestVNet1:**

- Anslutning: VNet1toVNet5


### Steg 6 – Skapa och konfigurera TestVNet5

Det här steget måste utföras i den nya prenumerationen. Den här delen kan utföras av administratören i en annan organisation som äger prenumerationen.

1. Deklarera dina variabler

    Ersätt värdena med de som du vill använda för din konfiguration.

        $Sub5          = "Replace_With_the_New_Subcription_Name"
        $RG5           = "TestRG5"
        $Location5     = "Japan East"
        $VnetName5     = "TestVNet5"
        $FESubName5    = "FrontEnd"
        $BESubName5    = "Backend"
        $GWSubName5    = "GatewaySubnet"
        $VnetPrefix51  = "10.51.0.0/16"
        $VnetPrefix52  = "10.52.0.0/16"
        $FESubPrefix5  = "10.51.0.0/24"
        $BESubPrefix5  = "10.52.0.0/24"
        $GWSubPrefix5  = "10.52.255.0/27"
        $DNS5          = "8.8.8.8"
        $GWName5       = "VNet5GW"
        $GWIPName5     = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51  = "VNet5toVNet1"

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

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Begär en offentlig IP-adress

        $gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Skapa gateway-konfigurationen

        $vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Skapa TestVNet5-gatewayen

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Steg 7 – Ansluta gateways

I det här exemplet där gatewayerna finns i olika prenumerationer, har vi delat upp steget i två PowerShell-sessioner som kallas för [Prenumeration 1] och [Prenumeration 5].

1. **[Prenumeration 1]** Hämta den virtuella nätverksgatewayen för Prenumeration 1

    Kontrollera att du loggat in och anslutit till Prenumeration 1.

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

    Kontrollera att du loggat in och anslutit till Prenumeration 5.

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

    I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet5. Skillnaden här är att $vnet5gw inte kan hämtas direkt, eftersom den finns i en annan prenumeration. Du måste skapa ett nytt PowerShell-objekt med värdena från Prenumeration 1 i stegen ovan. Byt ut namn, ID och delad nyckel mot dina egna värden. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

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
        $vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Kontrollera din anslutning

    När du har slutfört de här stegen kan du kontrollera anslutningen med hjälp av metoderna i [Så här verifierar du en VNet-till-VNet-anslutning](#Verify).

## Nästa steg

- När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) för anvisningar.
- Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md). 




<!--HONumber=jun16_HO2-->


