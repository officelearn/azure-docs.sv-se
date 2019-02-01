---
title: 'Ansluta klassiska virtuella nätverk till Azure Resource Manager-VNets: PowerShell | Microsoft Docs'
description: Skapa en VPN-anslutning mellan klassiska virtuella nätverk och Resource Manager-VNets med VPN-Gateway och PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: d515363e1413634d8222e043fff0b91aa464002c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509329"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Anslut virtuella nätverk från olika distributionsmodeller med hjälp av PowerShell

Den här artikeln hjälper dig ansluta klassiska virtuella nätverk till Resource Manager-VNets så att resurserna som finns i separata distributionsmodeller ska kunna kommunicera med varandra. I stegen i den här artikeln används PowerShell, men du kan också skapa den här konfigurationen med hjälp av Azure portal genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ansluta ett klassiskt virtuellt nätverk till ett Resource Manager-VNet liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge som de har konfigurerats med gatewayen är dynamisk eller routningsbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa ett du istället du överväga att ansluta dina virtuella nätverk med VNet-Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNET-peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Innan du börjar

Följande steg vägleder dig genom inställningarna som behövs för att konfigurera en dynamisk eller routningsbaserad gateway för varje virtuellt nätverk och skapa en VPN-anslutning mellan gateway. Den här konfigurationen stöder inte statisk eller principbaserade gatewayer.

### <a name="pre"></a>Förhandskrav

* Båda de virtuella nätverken har redan skapats. Om du vill skapa en resource manager virtuellt nätverk finns i [skapa en resursgrupp och ett virtuellt nätverk](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). För att skapa ett klassiskt virtuellt nätverk, se [skapa ett klassiskt virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Adressintervall för de virtuella nätverken inte överlappar varandra eller överlappar med något av intervallen för andra anslutningar som gatewayer kan anslutas till.
* Du har installerat det senaste PowerShell-cmdlet. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information. Kontrollera att du installerar både Service Management (SM) och cmdletar för Resource Manager (RM). 

### <a name="exampleref"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska VNet-inställningarna**

Namn på virtuellt nätverk = ClassicVNet <br>
Plats = USA, västra <br>
Adressutrymmen för virtuellt nätverk = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>
GatewayType = DynamicRouting

**Resource Manager-VNet-inställningarna**

Namn på virtuellt nätverk = RMVNet <br>
Resursgrupp = RG1 <br>
IP-adressutrymmen för virtuellt nätverk = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Plats = USA, östra <br>
Gatewaynamn på offentlig IP = gwpip <br>
Lokal nätverksgateway = ClassicVNetLocal <br>
Namn på virtuellt nätverksgateway = RMGateway <br>
Gateway-IP-adresseringskonfiguration = gwipconfig

## <a name="createsmgw"></a>Avsnitt 1 – konfigurera klassiskt virtuellt nätverk
### <a name="1-download-your-network-configuration-file"></a>1. Ladda ned din nätverkskonfigurationsfil
1. Logga in på ditt Azure-konto i PowerShell-konsol med utökade behörigheter. Följande cmdlet uppmanar dig inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell. Klassiska Service Management (SM) Azure PowerShell-cmdlets som används i det här avsnittet.

  ```azurepowershell
  Add-AzureAccount
  ```

  Hämta din Azure-prenumeration.

  ```azurepowershell
  Get-AzureSubscription
  ```

  Om du har mer än en prenumeration väljer du den du vill använda.

  ```azurepowershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Exportera konfigurationsfilen Azure-nätverk genom att köra följande kommando. Du kan ändra platsen för den fil som ska exporteras till en annan plats om det behövs.

  ```azurepowershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Öppna XML-filen som du laddade ned för att redigera den. Ett exempel på nätverkskonfigurationsfilen finns i den [nätverk konfigurationsschema](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Verifiera gateway-undernätet
I den **VirtualNetworkSites** element, lägga till ett gateway-undernät i ditt virtuella nätverk om något inte redan har skapats. När du arbetar med nätverkskonfigurationsfilen, gateway-undernätet måste ha namnet ”GatewaySubnet” eller Azure kan inte identifiera och använda det som ett gateway-undernät.

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
Den lokala nätverksplatsen som du lägger till representerar RM VNet som du vill ansluta till. Lägg till en **LocalNetworkSites** elementet i filen om det inte redan finns. I det här läget i konfigurationen, kan VPNGatewayAddress vara någon giltig offentlig IP-adress eftersom vi inte har skapat gatewayen för Resource Manager-VNet. När vi skapar gatewayen, ersätta vi platshållaren IP-adressen med den korrekta offentliga IP-adressen som har tilldelats till RM-gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Koppla det virtuella nätverket till den lokala nätverksplatsen
I det här avsnittet ska ange vi den lokala nätverksplatsen som du vill ansluta det virtuella nätverket kan. I det här fallet är det Resource Manager-VNet som refererar till tidigare. Kontrollera att namnen matchar. Det här steget skapar inte en gateway. Den anger det lokala nätverket som gatewayen ska ansluta till.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Spara filen och ladda upp
Spara filen och sedan importera den till Azure genom att köra följande kommando. Kontrollera att du ändrar filsökväg som krävs för miljön.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Du kan se liknande resultat som visar att importen lyckades.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Skapa gatewayen

Innan du kör det här exemplet måste du referera till nätverkskonfigurationsfilen som du hämtade för de exakta namn som Azure förväntar sig att se. Nätverkskonfigurationsfilen innehåller värden för din klassiska virtuella nätverk. Ibland ändras namnen för klassiska virtuella nätverk i nätverkskonfigurationsfilen när du skapar klassiska VNet-inställningarna i Azure-portalen på grund av skillnader i distributionsmodeller. Om du använde Azure-portalen för att skapa ett klassiskt virtuellt nätverk med namnet ”klassiskt virtuellt nätverk” och skapade den i en resursgrupp med namnet ”ClassicRG”, till exempel konverteras det namn som finns i nätverkskonfigurationsfilen till ”grupp ClassicRG klassiskt virtuellt nätverk”. När du anger namnet på ett virtuellt nätverk som innehåller blanksteg, använder du värdet inom citattecken.


Använd följande exempel för att skapa en dynamisk routningsgateway:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Du kan kontrollera statusen för gatewayen med hjälp av den **Get-AzureVNetGateway** cmdlet.

## <a name="creatermgw"></a>Avsnitt 2 – konfigurera RM-VNet-gateway

Kraven förutsätter att du redan har skapat ett virtuellt RM-nätverk. I det här steget skapar du en VPN-gateway för RM-VNet. Starta inte de här stegen tills när du har hämtat offentliga IP-adress för den klassiska VNet-gateway. 

1. Logga in på ditt Azure-konto i PowerShell-konsolen. Följande cmdlet uppmanar dig inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Du kan också använda funktionen ”prova” för att starta Azure Cloud Shell i webbläsaren.

  Om du använder Azure Cloud Shell kan du hoppa över följande cmdlet:

  ```azurepowershell
  Connect-AzureRmAccount
  ``` 
  Kontrollera att du använder rätt prenumeration genom att köra följande cmdlet:  

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
   
  Om du har mer än en prenumeration kan du ange den prenumeration som du vill använda.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Skapa en lokal nätverksgateway. I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. I det här fallet avser den lokala nätverksgatewayen klassiska VNet. Ge den ett namn som Azure kan referera till det och även ange adressutrymmets prefix. Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Om du behöver ändra informationen här senare innan du skapar din gateway kan du ändra värdena och köra exemplet igen.
   
   **-Namnet** är det namn som du vill tilldela för att referera till den lokala nätverksgatewayen.<br>
   **-AddressPrefix** är adressutrymmet för det klassiska virtuella nätverket.<br>
   **-GatewayIpAddress** är den offentliga IP-adressen på den klassiska VNet-gatewayen. Glöm inte att ändra följande exempeltexten ”n.n.n.n” för att återspegla rätt IP-adress.<br>

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Begär en offentlig IP-adress som ska allokeras till den virtuella nätverksgatewayen för Resource Manager-VNet. Du kan inte ange den IP-adress som du vill använda. IP-adressen tilldelas dynamiskt till den virtuella nätverksgatewayen. Detta betyder dock inte IP-adressen kan ändras. Den enda gången virtuellt nätverk gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt Underhåll/uppgraderingar av gateway.

  I det här steget ska ange vi också en variabel som används i ett senare steg.

  ```azurepowershell-interactive
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Kontrollera att det virtuella nätverket har en gateway-undernätet. Om det finns ingen gateway-undernät, kan du lägga till en. Se till att gateway-undernätet har namnet *GatewaySubnet*.
5. Hämta det undernät som används för gatewayen genom att köra följande kommando. I det här steget ska ange vi också en variabel som ska användas i nästa steg.
   
   **-Namnet** är namnet på ditt Resource Manager-VNet.<br>
   **-ResourceGroupName** är den resursgrupp som det virtuella nätverket är associerad med. Gateway-undernätet måste finnas för det här virtuella nätverket och måste ha namnet *GatewaySubnet* ska fungera korrekt.<br>

  ```azurepowershell-interactive
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Skapa gateway-IP-adressering konfigurationen. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration.

  I det här steget i **- SubnetId** och **- PublicIpAddressId** parametrar måste skickas id-egenskapen från undernätet och IP-adress-objekt, respektive. Du kan inte använda en enkel sträng. Dessa variabler anges i steg för att begära en offentlig IP-adress och steget för att hämta undernätet.

  ```azurepowershell-interactive
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Skapa den virtuella nätverksgatewayen för Resource Manager genom att köra följande kommando. Den `-VpnType` måste vara *RouteBased*. Det kan ta 45 minuter eller mer för att skapa gatewayen.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Kopiera den offentliga IP-adressen när VPN-gatewayen har skapats. Du kan använda den för när du konfigurerar inställningar för lokalt nätverk för ditt klassiska virtuella nätverk. Du kan använda följande cmdlet för att hämta den offentliga IP-adressen. Offentliga IP-adress visas i avkastningen som *IP-adress*.

  ```azurepowershell-interactive
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Avsnitt 3 – ändra inställningar för lokal plats av klassiska virtuella nätverk

I det här avsnittet kan du arbeta med det klassiska virtuella nätverket. Du ersätta platshållaren IP-adressen som du använde när du anger inställningar för lokal plats som ska användas för att ansluta till Resource Manager-VNet-gateway. Eftersom du arbetar med den klassiska VNet, använder du PowerShell installerat lokalt till datorn, inte Azure Cloud Shell TryIt.

1. Exportera nätverkskonfigurationsfilen.

  ```azurepowershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Använd en textredigerare och ändra värdet för VPNGatewayAddress. Ersätta PLATSHÅLLAR-IP-adressen med offentliga IP-adressen för Resource Manager-gateway och sedan spara ändringarna.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importera ändrade nätverkskonfigurationsfilen till Azure.

  ```azurepowershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Avsnitt 4 – skapa en anslutning mellan gateway
Skapa en anslutning mellan gateway kräver PowerShell. Du kan behöva lägga till ditt Azure-konto om du vill använda den klassiska versionen av PowerShell-cmdletar. Du gör detta genom att använda **Add-AzureAccount**.

1. Ange den delade nyckeln i PowerShell-konsolen. Innan du kör cmdletarna måste referera till nätverkskonfigurationsfilen som du hämtade för de exakta namn som Azure förväntar sig att se. När du anger namnet på ett virtuellt nätverk som innehåller blanksteg, Använd enkla citattecken runt värdet.<br><br>I följande exempel **- VNetName** är namnet på det klassiska virtuella nätverket och **- LocalNetworkSiteName** är det namn du angav för den lokala nätverksplatsen. Den **- SharedKey** är ett värde som du genererar och anger. I det här exemplet vi ' abc123 ', men du kan skapa och använda ett mer komplext. Viktigt är att värdet som du anger här måste vara samma värde som du anger i nästa steg när du skapar din anslutning. Avkastningen ska visa **Status: Lyckad**.

  ```azurepowershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Skapa VPN-anslutningen genom att köra följande kommandon:
   
  Ange variablerna.

  ```azurepowershell-interactive
  $vnet01gateway = Get-AzureRmLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Skapa anslutningen. Observera att den **- ConnectionType** är IPsec, inte Vnet2Vnet.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Avsnitt 5 – verifiera dina anslutningar

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Verifiera anslutningen från klassiska VNet till ditt VNet i Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Verifiera anslutningen från Resource Manager-VNet till ditt klassiska virtuella nätverk

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
