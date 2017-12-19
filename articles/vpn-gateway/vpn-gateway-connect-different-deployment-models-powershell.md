---
title: "Ansluta klassiska virtuella nätverk till Azure Resource Manager VNets: PowerShell | Microsoft Docs"
description: "Lär dig hur du skapar en VPN-anslutning mellan klassiska Vnet och Resource Manager VNets med VPN-Gateway och PowerShell"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
ms.openlocfilehash: da5bddba3a1fad74b2ee08fd2f34d1b01c7345c8
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Anslut virtuella nätverk från olika distributionsmodeller med hjälp av PowerShell



Den här artikeln visar hur du ansluter klassiska Vnet till Resource Manager VNets så att de resurser som finns i separata distributionsmodeller för att kommunicera med varandra. Stegen i den här artikeln använder PowerShell, men du kan också skapa den här konfigurationen med hjälp av Azure portal genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ansluta ett klassiskt virtuellt nätverk till ett VNet Resource Manager liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan Vnet som finns i olika prenumerationer och i olika regioner. Du kan också ansluta Vnet som redan har anslutningar till lokalt nätverk, så länge som de har konfigurerats med gatewayen är dynamisk eller ruttbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om ditt Vnet i samma region, kanske du vill i stället bör överväga att ansluta dem med hjälp av VNet-Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md). 

## <a name="before"></a>Innan du börjar

Följande steg vägleder dig genom inställningarna som behövs för att konfigurera en gateway dynamisk eller ruttbaserad för varje VNet och skapa en VPN-anslutning mellan gatewayer. Den här konfigurationen stöder inte statisk eller principbaserad gateways.

### <a name="pre"></a>Förhandskrav

* Båda Vnet har redan skapats.
* Adressintervall för till Vnet inte överlappar varandra eller överlappar ett intervall för andra anslutningar gateway kan anslutas till.
* Du har installerat de senaste PowerShell-cmdletarna. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information. Kontrollera att du installerar både Service Management (SM) och Resource Manager (RM)-cmdletar. 

### <a name="exampleref"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska VNet-inställningarna**

VNet-Name = ClassicVNet <br>
Plats = västra USA <br>
Virtuellt nätverk adressutrymmen = 10.0.0.0/24 <br>
Undernät-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokala nätverksnamn = RMVNetLocal <br>
GatewayType = DynamicRouting

**Hanteraren för filserverresurser VNet-inställningarna**

VNet-Name = RMVNet <br>
Resursgruppens namn = RG1 <br>
Virtuellt IP-adressutrymmen = 192.168.0.0/16 <br>
Undernät-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Plats = östra USA <br>
Gateway offentliga IP-namn = gwpip <br>
Lokal nätverksgateway = ClassicVNetLocal <br>
Virtuell nätverksgateway name = RMGateway <br>
Gateway-IP-adressering konfigurationen = gwipconfig

## <a name="createsmgw"></a>Avsnittet 1 – konfigurera klassiska VNet
### <a name="1-download-your-network-configuration-file"></a>1. Hämta din nätverks-konfigurationsfil
1. Logga in på ditt Azure-konto i PowerShell-konsol med utökade behörigheter. Följande cmdlet efterfrågar autentiseringsuppgifter för inloggning för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell. Du kan använda SM PowerShell-cmdlets för att slutföra den här delen av konfigurationen.

  ```powershell
  Add-AzureAccount
  ```
2. Exportera konfigurationsfilen Azure-nätverk genom att köra följande kommando. Du kan ändra platsen för den fil som ska exporteras till en annan plats om det behövs.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Öppna XML-filen som du hämtade för att redigera den. Ett exempel på nätverket konfigurationsfilen finns på [nätverk Konfigurationsschemat](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Kontrollera gateway-undernätet.
I den **VirtualNetworkSites** element, lägga till en gateway-undernät i ditt VNet om något inte redan har skapats. När du arbetar med nätverk konfigurationsfilen gateway-undernätet måste ha namnet ”GatewaySubnet” eller Azure kan inte identifiera och använda den som en gateway-undernätet.

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

### <a name="3-add-the-local-network-site"></a>3. Lägg till lokal nätverksplats
Den lokala nätverksplatsen som du lägger till representerar RM VNet som du vill ansluta till. Lägg till en **LocalNetworkSites** element i filen om det inte redan finns. Nu i konfigurationen, kan VPNGatewayAddress vara någon giltig offentlig IP-adress eftersom vi inte har skapat en gateway för Resource Manager-VNet. När vi skapa gatewayen Ersätt vi denna platshållare IP-adress med rätt offentliga IP-adressen som har tilldelats RM-gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Koppla VNet till lokal nätverksplats
I det här avsnittet anger vi den lokala nätverksplatsen som du vill ansluta VNet. I det här fallet är det Resource Manager-VNet som refererar till tidigare. Kontrollera att namnen stämmer överens. Det här steget kan inte skapa en gateway. Anger det lokala nätverket som gatewayen ska ansluta till.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Spara filen och ladda upp
Spara filen och sedan importera den till Azure genom att köra följande kommando. Kontrollera att du ändrar sökvägen till filen som behövs för din miljö.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Du ser ett liknande resultat som visar att importen lyckades.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Skapa gatewayen

Innan du kör det här exemplet finns nätverket konfigurationsfilen som du hämtade för de exakta namn som Azure förväntar sig att se. Konfigurationsfilen för nätverk innehåller värden för din klassiska virtuella nätverk. Ibland har namnen för klassiska Vnet ändrats i konfigurationsfilen nätverk när du skapar klassiska VNet-inställningarna i Azure-portalen på grund av skillnader i distributionsmodellerna. Om du använder Azure-portalen för att skapa ett klassiskt virtuellt nätverk med namnet 'klassiska virtuella nätverk, och skapas i en resursgrupp med namnet 'ClassicRG', till exempel konverteras det namn som finns i konfigurationsfilen på nätverket till ”grupp ClassicRG klassiska virtuella nätverk”. När du anger namnet på ett virtuellt nätverk som innehåller blanksteg, använder du värdet inom citattecken.


Använd följande exempel för att skapa en dynamisk routning gateway:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Du kan kontrollera status för gatewayen med hjälp av den **Get-AzureVNetGateway** cmdlet.

## <a name="creatermgw"></a>Avsnitt 2 – konfigurera RM VNet-gateway
Följ instruktionerna nedan om du vill skapa en VPN-gateway för RM-VNet. Starta inte stegen tills när du har hämtat den offentliga IP-adressen för det klassiska VNet-gateway. 

1. Logga in på ditt Azure-konto i PowerShell-konsolen. Följande cmdlet efterfrågar autentiseringsuppgifter för inloggning för ditt Azure-konto. När du loggar in laddas inställningarna för ditt konto så att de blir tillgängliga för Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ``` 
   
  Hämta en lista över dina Azure-prenumerationer om du har mer än en prenumeration.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Skapa en lokal nätverksgateway. I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. I det här fallet refererar den lokala nätverksgatewayen till ditt klassiska VNet. Ge det ett namn som Azure kan referera till det och även ange adressprefixet utrymme. Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Om du behöver ändra informationen här senare innan du skapar din gateway kan du ändra värdena och köra exemplet igen.
   
   **-Namnet** är det namn som du vill tilldela för att referera till den lokala nätverksgatewayen.<br>
   **-AddressPrefix** är adressutrymmet för din klassiska VNet.<br>
   **-GatewayIpAddress** offentliga IP-adressen för det klassiska VNet gateway. Kom ihåg att ändra i följande exempel för att återspegla rätt IP-adress.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Begär offentlig IP-adress som ska allokeras till den virtuella nätverksgatewayen för Resource Manager-VNet. Du kan inte ange den IP-adress som du vill använda. IP-adressen tilldelas dynamiskt till den virtuella nätverksgatewayen. Detta betyder dock inte IP-adressen kan ändras. Endast virtuella nätverk gateway IP-adressändringarna är när gatewayen har tagits bort och återskapas. Ändringen inte över storleksändring, återställa eller andra internt Underhåll/uppgraderingar av gateway.

  I det här steget ska ange vi också en variabel som används i ett senare steg.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Kontrollera att det virtuella nätverket har en gateway-undernätet. Om det finns ingen gateway-undernät, kan du lägga till en. Se till gateway-undernätet heter *GatewaySubnet*.
5. Hämta det undernät som används för gatewayen genom att köra följande kommando. I det här steget ska ange vi också en variabel som ska användas i nästa steg.
   
   **-Namnet** är namnet på ditt VNet Resource Manager.<br>
   **-ResourceGroupName** är den resursgrupp som VNet som är associerad med. Gateway-undernätet måste finnas för detta virtuella nätverk och måste ha namnet *GatewaySubnet* ska fungera korrekt.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Skapa gatewaykonfigurationen för IP-adressering. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration.

  I det här steget i **- SubnetId** och **- PublicIpAddressId** parametrar måste överföras id-egenskapen från undernätet och IP-adress-objekt, respektive. Du kan inte använda en sträng. Dessa variabler som anges i steg att begära en offentlig IP-adress och steget för att hämta undernätet.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Skapa den virtuella nätverksgatewayen för Resource Manager genom att köra följande kommando. Den `-VpnType` måste vara *RouteBased*. Det kan ta 45 minuter eller mer att skapa gatewayen.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Kopiera den offentliga IP-adressen när VPN-gateway har skapats. Du kan använda den för när du konfigurerar lokala nätverksinställningarna för din klassiska VNet. Du kan använda följande cmdlet för att hämta den offentliga IP-adressen. Den offentliga IP-adressen anges i returnera som *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Avsnitt 3 - ändra inställningarna för klassiska VNet-lokala platsen

I det här avsnittet kan du arbeta med klassiska virtuella nätverk. Du ersätta platshållaren IP-adressen som du använde när du anger de lokala platsinställningar som ska användas för att ansluta till Resource Manager VNet-gateway. 

1. Exportera konfigurationsfilen nätverk.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Använd en textredigerare och ändra värdet för VPNGatewayAddress. Ersätt platshållaren IP-adress med offentliga IP-adressen för gateway för Resource Manager och sedan spara ändringarna.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importera konfigurationsfilen ändrade nätverk till Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Avsnitt 4 - skapa en anslutning mellan gatewayer
Skapa en anslutning mellan gatewayer kräver PowerShell. Du kan behöva lägga till ditt Azure-konto om du vill använda den klassiska versionen av PowerShell-cmdlets. Det gör du genom att använda **Add-AzureAccount**.

1. Ange den delade nyckeln i PowerShell-konsolen. Innan du kör cmdlet: arna finns nätverket konfigurationsfilen som du hämtade för de exakta namn som Azure förväntar sig att se. När du anger namnet på ett virtuellt nätverk som innehåller blanksteg, Använd enkla citattecken runt värdet.<br><br>I följande exempel **- VNetName** är namnet på det klassiska virtuella nätverket och **- LocalNetworkSiteName** är det namn du angav för den lokala nätverksplatsen. Den **- SharedKey** är ett värde som du skapar och ange. I exempel använde vi 'abc123', men du kan skapa och använda något mer komplicerad. Viktigt är att värdet som du anger här måste ha samma värde som du anger i nästa steg när du skapar anslutningen. Returen ska visa **Status: lyckade**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Skapa VPN-anslutningen genom att köra följande kommandon:
   
  Ange variablerna.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Skapa anslutningen. Observera att den **- ConnectionType** är IPsec, inte Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Avsnittet 5 – Kontrollera anslutningarna

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Kontrollera anslutningen från ditt klassiska VNet till Resource Manager-VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Kontrollera anslutningen från Resource Manager-VNet till ditt klassiska VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

