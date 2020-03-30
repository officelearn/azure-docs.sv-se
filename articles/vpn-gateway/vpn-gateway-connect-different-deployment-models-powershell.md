---
title: 'Ansluta klassiska virtuella nätverk till virtuella Azure Resource Manager-nätverk: PowerShell'
description: Skapa en VPN-anslutning mellan klassiska virtuella nätverk och resurshanteraren virtuella nätverk med VPN Gateway och PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152014"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Anslut virtuella nätverk från olika distributionsmodeller med hjälp av PowerShell

Den här artikeln hjälper dig att ansluta klassiska virtuella nätverk till virtuella nätverk för Resurshanteraren så att resurserna i de separata distributionsmodellerna kan kommunicera med varandra. Stegen i den här artikeln använder PowerShell, men du kan också skapa den här konfigurationen med Hjälp av Azure-portalen genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Att ansluta ett klassiskt virtuella nätverk till ett resurshanteraren-nätverk påminner om att ansluta ett virtuella nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge gatewayen som de har konfigurerats med är dynamisk eller flödesbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa en, kanske du i stället vill överväga att ansluta dina virtuella nätverk med hjälp av VNet Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

I följande steg går du igenom de inställningar som krävs för att konfigurera en dynamisk eller ruttbaserad gateway för varje virtuella nätverk och skapa en VPN-anslutning mellan gateways. Den här konfigurationen stöder inte statiska eller principbaserade gateways.

### <a name="prerequisites"></a><a name="pre"></a>Krav

* Båda virtuella nätverken har redan skapats. Om du behöver skapa ett virtuellt resurshanterarenätverk läser du [Skapa en resursgrupp och ett virtuellt nätverk](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Om du vill skapa ett klassiskt virtuellt nätverk finns i [Skapa ett klassiskt virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Adressintervallen för virtuella nätverk överlappar inte varandra eller överlappar något av intervallen för andra anslutningar som gatewayerna kan vara anslutna till.
* Du har installerat de senaste PowerShell-cmdlets. Mer [information finns i Så här installerar och konfigurerar du Azure PowerShell.](/powershell/azure/overview) Se till att du installerar både SERVICE Management (SM) och RM-cmdlets (Resource Manager). 

### <a name="example-settings"></a><a name="exampleref"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska VNet-inställningar**

VNet-namn = ClassicVNet <br>
Plats = Västra USA <br>
Virtuella nätverksadressutrymmen = 10.0.0.0/24 <br>
Undernät-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Namn på lokalt nätverk = RMVNetLocal <br>
GatewayType = DynamicRouting

**Virtuella nätverkinställningar för Resurshanteraren**

VNet-namn = RMVNet <br>
Resursgrupp = RG1 <br>
IP-adressutrymmen för virtuellt nätverk = 192.168.0.0/16 <br>
Undernät-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Plats = Östra USA <br>
Gateway offentligt IP-namn = gwpip <br>
Lokal nätverksgateway = ClassicVNetLokal <br>
Namn på Virtual Network Gateway = RMGateway <br>
Konfiguration av GATEWAY IP-adressering = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Avsnitt 1 - Konfigurera det klassiska virtuella nätverket
### <a name="1-download-your-network-configuration-file"></a>1. Ladda ner nätverkskonfigurationsfilen
1. Logga in på ditt Azure-konto i PowerShell-konsolen med förhöjda rättigheter. Följande cmdlet uppmanar dig att ange inloggningsuppgifter för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell. De klassiska Azure PowerShell-cmdlets (Service Management) används i det här avsnittet.

   ```azurepowershell
   Add-AzureAccount
   ```

   Skaffa din Azure-prenumeration.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Om du har mer än en prenumeration väljer du den du vill använda.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportera konfigurationsfilen för Azure-nätverket genom att köra följande kommando. Du kan ändra platsen för filen så att den exporteras till en annan plats om det behövs.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Öppna XML-filen som du hämtade för att redigera den. Ett exempel på nätverkskonfigurationsfilen finns i [nätverkskonfigurationsschemat](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Verifiera gateway-undernätet
I elementet **VirtualNetworkSites** lägger du till ett gateway-undernät i ditt virtuella nätverk om ett enda nätverk inte redan har skapats. När du arbetar med nätverkskonfigurationsfilen måste gateway-undernätet ha namnet "GatewaySubnet" eller azure kan inte känna igen och använda det som ett gateway-undernät.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exempel:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Lägg till den lokala nätverksplatsen
Den lokala nätverkswebbplats som du lägger till representerar det virtuella RM-nätverk som du vill ansluta till. Lägg till ett **LocalNetworkSites-element** i filen om det inte redan finns något. Vid denna punkt i konfigurationen kan VPNGatewayAddress vara en giltig offentlig IP-adress eftersom vi ännu inte har skapat gatewayen för Resource Manager VNet. När vi har skapat gatewayen ersätter vi den här platshållar-IP-adressen med rätt offentlig IP-adress som har tilldelats RM-gatewayen.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associera det virtuella nätverket med den lokala nätverksplatsen
I det här avsnittet anger vi den lokala nätverksplats som du vill ansluta det virtuella nätverket till. I det här fallet är det resurshanterarens virtuella nätverk som du refererade till tidigare. Se till att namnen matchar. Det här steget skapar inte en gateway. Den anger det lokala nätverk som gatewayen ska ansluta till.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Spara filen och ladda upp
Spara filen och importera den sedan till Azure genom att köra följande kommando. Se till att du ändrar sökvägen efter behov för din miljö.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Du kommer att se ett liknande resultat som visar att importen lyckades.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Skapa gatewayen

Innan du kör det här exemplet läser du nätverkskonfigurationsfilen som du hämtade för de exakta namnen som Azure förväntar sig att se. Nätverkskonfigurationsfilen innehåller värdena för dina klassiska virtuella nätverk. Ibland ändras namnen för klassiska virtuella nätverk i nätverkskonfigurationsfilen när klassiska VNet-inställningar skapas i Azure-portalen på grund av skillnaderna i distributionsmodellerna. Om du till exempel använde Azure-portalen för att skapa ett klassiskt VNet med namnet "Klassiskt VNet" och skapade den i en resursgrupp med namnet "ClassicRG", konverteras namnet som finns i nätverkskonfigurationsfilen till "Group ClassicRG Classic VNet". När du anger namnet på ett virtuella nätverk som innehåller blanksteg använder du citattecken runt värdet.


Använd följande exempel för att skapa en dynamisk routningsgateway:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Du kan kontrollera status för gatewayen med hjälp av cmdleten **Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Avsnitt 2 - Konfigurera RM VNet-gatewayen



Förutsättningarna förutsätter att du redan har skapat ett RM-VNet. I det här steget skapar du en VPN-gateway för RM VNet. Starta inte dessa steg förrän du har hämtat den offentliga IP-adressen för det klassiska virtuella nätverkets gateway. 

1. Logga in på ditt Azure-konto i PowerShell-konsolen. Följande cmdlet uppmanar dig att ange inloggningsuppgifter för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Du kan också använda funktionen "Prova" för att starta Azure Cloud Shell i webbläsaren.

   Om du använder Azure Cloud Shell hoppar du över följande cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Om du vill kontrollera att du använder rätt prenumeration kör du följande cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Skapa en lokal nätverksgateway. I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. I det här fallet refererar den lokala nätverksgatewayen till ditt klassiska virtuella nätverk. Ge det ett namn som Azure kan referera till, och ange även adressutrymmesprefixet. Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Om du behöver justera informationen här senare, innan du skapar gatewayen, kan du ändra värdena och köra exemplet igen.
   
   **-Namn** är det namn som du vill tilldela för att referera till den lokala nätverksgatewayen.<br>
   **-AddressPrefix** är adressutrymmet för ditt klassiska virtuella nätverk.<br>
   **-GatewayIpAddress** är den offentliga IP-adressen för det klassiska virtuella nätverkets gateway. Var noga med att ändra följande exempeltext "n.n.n.n" för att återspegla rätt IP-adress.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Begär att en offentlig IP-adress ska tilldelas den virtuella nätverksgatewayen för resurshanterarens virtuella nätverk. Du kan inte ange den IP-adress som du vill använda. IP-adressen allokeras dynamiskt till den virtuella nätverksgatewayen. Detta betyder dock inte IP-adressen kan ändras. Den enda gången ip-adress för virtuell nätverksgateway ändras är när gatewayen tas bort och återskapas. Det ändras inte mellan storleksändring, återställning eller andra interna underhåll/uppgraderingar av gatewayen.

   I det här steget anger vi också en variabel som används i ett senare steg.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Kontrollera att det virtuella nätverket har ett gateway-undernät. Om det inte finns något gateway-undernät lägger du till ett. Kontrollera att gateway-undernätet har namnet *GatewaySubnet*.
5. Hämta det undernät som används för gatewayen genom att köra följande kommando. I det här steget anger vi också en variabel som ska användas i nästa steg.
   
   **-Namnet** är namnet på ditt Resource Manager-virtuella nätverk.<br>
   **-ResourceGroupName** är den resursgrupp som det virtuella nätverket är associerat med. Gateway-undernätet måste redan finnas för det här virtuella nätverket och måste ha namnet *GatewaySubnet* för att fungera korrekt.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Skapa konfigurationen för GATEWAY IP-adressering. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration.

   I det här steget måste parametrarna **-SubnetId** och **-PublicIpAddressId** skickas till egenskapen id från undernätet respektive IP-adressobjekten. Du kan inte använda en enkel sträng. Dessa variabler anges i steget för att begära en offentlig IP och steget för att hämta undernätet.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Skapa den virtuella nätverksgatewayen för Resource Manager genom att köra följande kommando. Måste `-VpnType` vara *RouteBased*. Det kan ta 45 minuter eller mer för gatewayen att skapa.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Kopiera den offentliga IP-adressen när VPN-gatewayen har skapats. Du använder den när du konfigurerar de lokala nätverksinställningarna för ditt klassiska virtuella nätverk. Du kan använda följande cmdlet för att hämta den offentliga IP-adressen. Den offentliga IP-adressen visas i returen som *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Avsnitt 3 - Ändra de klassiska inställningarna för virtuella nätverk på lokal webbplats

I det här avsnittet arbetar du med det klassiska virtuella nätverket. Du ersätter den platshållar-IP-adress som du använde när du angav de lokala platsinställningar som ska användas för att ansluta till Resource Manager-VNet-gatewayen. Eftersom du arbetar med det klassiska virtuella nätverket använder du PowerShell som är installerat lokalt på datorn, inte Azure Cloud Shell TryIt.

1. Exportera nätverkskonfigurationsfilen.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Ändra värdet för VPNGatewayAddress med hjälp av en textredigerare. Ersätt platshållar-IP-adressen med den offentliga IP-adressen för Resource Manager-gatewayen och spara ändringarna.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importera den ändrade nätverkskonfigurationsfilen till Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Avsnitt 4 - Skapa en anslutning mellan gatewayerna
För att skapa en anslutning mellan gatewayerna krävs PowerShell. Du kan behöva lägga till ditt Azure-konto för att använda den klassiska versionen av PowerShell-cmdlets. Det gör du genom att använda **Add-AzureAccount**.

1. Ställ in den delade nyckeln i PowerShell-konsolen. Innan du kör cmdlets, se nätverkskonfigurationsfilen som du hämtade för de exakta namnen som Azure förväntar sig att se. När du anger namnet på ett virtuella nätverk som innehåller blanksteg använder du enstaka citattecken runt värdet.<br><br>I följande exempel är **-VNetName** namnet på det klassiska virtuella nätverket och **-LocalNetworkSiteName** är det namn som du har angett för den lokala nätverksplatsen. **-SharedKey** är ett värde som du genererar och anger. I exemplet använde vi "abc123", men du kan generera och använda något mer komplext. Det viktiga är att värdet du anger här måste vara samma värde som du anger i nästa steg när du skapar anslutningen. Returen ska visa **Status: Lyckad**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Skapa VPN-anslutningen genom att köra följande kommandon:
   
   Ange variablerna.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Skapa anslutningen. Observera att **-ConnectionType** är IPsec, inte Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Avsnitt 5 - Verifiera dina anslutningar

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Så här verifierar du anslutningen från det klassiska virtuella nätverket till ditt resurshanterarens virtuella nätverk

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Så här verifierar du anslutningen från resource manager-nätverket till ditt klassiska virtuella nätverk

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
