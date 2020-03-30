---
title: 'Ansluta en dator till ett virtuellt nätverk med Point-to-Site- och RADIUS-autentisering: PowerShell | Azure'
description: Anslut Windows- och Mac OS X-klienter på ett säkert sätt till ett virtuellt nätverk med P2S- och RADIUS-autentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 25bc25d9ec12804cc20baa558dce67fb3f8269a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149220"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurera en point-to-site-anslutning till ett VNet med RADIUS-autentisering: PowerShell

Den här artikeln visar hur du skapar ett virtuella nätverk med en point-to-site-anslutning som använder RADIUS-autentisering. Den här konfigurationen är endast tillgänglig för Resurshanterarens distributionsmodell.

Med en VPN-gateway med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. VPN-anslutningar från punkt till plats är användbara när du vill ansluta till ditt virtuella nätverk från en fjärrplats, till exempel när du pendlar hemifrån eller en konferens. En P2S-VPN-anslutning är också en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet.

En P2S VPN-anslutning startas från Windows- och Mac-enheter. Anslutande klienter kan använda följande autentiseringsmetoder: 

* RADIUS-server
* Autentisering av inbyggt certifikat för VPN Gateway

Den här artikeln hjälper dig att konfigurera en P2S-konfiguration med autentisering med RADIUS-servern. Om du vill autentisera med hjälp av genererade certifikat och inbyggd certifikatautentisering för [VPN-gateway läser du Konfigurera en point-to-site-anslutning till ett VNet med inbyggd certifikatautentisering för inbyggd certifikat för VPN-gateway](vpn-gateway-howto-point-to-site-rm-ps.md).

![Anslutningsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via antingen SSTP (Secure Socket Tunneling Protocol), OpenVPN eller IKEv2.

* SSTP är en SSL-baserad VPN-tunnel som endast stöds på Windows-klientplattformar. Den kan ta sig förbi brandväggar, vilket gör den till ett utmärkt alternativ för att ansluta till Azure var som helst. På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard.

* OpenVPN® Protocol, ett SSL/TLS-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443 utgående, som SSL använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11.0 och högre), Windows, Linux och Mac-enheter (OSX-versioner 10.13 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

P2S-anslutningar kräver följande:

* En RouteBased VPN gateway. 
* En RADIUS-server för att hantera användarautentisering. RADIUS-servern kan distribueras lokalt eller i Azure VNet.
* Ett VPN-klientkonfigurationspaket för Windows-enheter som ansluter till det virtuella nätverket. Ett VPN-klientkonfigurationspaket innehåller de inställningar som krävs för att en VPN-klient ska kunna ansluta via P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Om AD-domänautentisering (Active Directory) för P2S-VPN

AD-domänautentisering tillåter användare att logga in på Azure med sina autentiseringsuppgifter för organisationsdomäner. Det kräver en RADIUS-server som integreras med AD-servern. Organisationer kan också utnyttja sin befintliga RADIUS-distribution.
 
RADIUS-servern kan finnas lokalt eller i ditt Virtuella Azure-nätverk. Under autentiseringen fungerar VPN-gatewayen som en genomströmning och vidarebefordrar autentiseringsmeddelanden fram och tillbaka mellan RADIUS-servern och anslutningsenheten. Det är viktigt för VPN-gatewayen att kunna nå RADIUS-servern. Om RADIUS-servern finns lokalt krävs en VPN-anslutning från plats till plats från Azure till den lokala platsen.

Förutom Active Directory kan en RADIUS-server även integreras med andra externa identitetssystem. Detta öppnar många autentiseringsalternativ för virtuella vpn från punkt till plats, inklusive MFA-alternativ. Kontrollera dokumentationen för RADIUS-serverns leverantör för att få en lista över identitetssystem som den integreras med.

![Anslutningsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Endast en VPN-anslutning från plats till plats kan användas för att ansluta till en RADIUS-server lokalt. Det går inte att använda en ExpressRoute-anslutning.
>
>

## <a name="before-beginning"></a><a name="before"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Exempelvärden

Du kan använda exempelvärdena för att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö.

* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16** and **10.254.0.0/16**<br>I det här exemplet används mer än ett adressutrymme för att illustrera att den här konfigurationen fungerar med flera adressutrymmen. Flera adressutrymmen krävs dock inte för den här konfigurationen.
* **Undernätsnamn: FrontEnd**
  * **Adressintervall för undernätet: 192.168.1.0/24**
* **Undernätsnamn: BackEnd**
  * **Adressintervall för undernätet: 10.254.1.0/24**
* **Undernätsnamn: GatewaySubnet**<br>Undernätsnamnet *GatewaySubnet* är obligatoriskt för att VPN-gatewayen ska fungera.
  * **Adressintervall för gateway-undernätet: 192.168.200.0/24** 
* **VPN-klientadresspool: 172.16.201.0/24**<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från VPN-klientadresspoolen.
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp: TestRG**
* **Plats: Östra USA**
* **DNS-server: IP-adressen** till den DNS-server som du vill använda för namnmatchning för ditt virtuella nätverk. (valfritt)
* **GW-namn: Vnet1GW**
* **Offentligt IP-namn: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Ställ in variablerna

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs. Om du stänger powershell/cloud shell-sessionen när som helst under övningen kopierar och klistrar du bara in värdena igen för att deklarera variablerna igen.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"> </a>Skapa resursgruppen, virtuella nätverk och offentliga IP-adress

Följande steg skapar en resursgrupp och ett virtuellt nätverk i resursgruppen med tre undernät. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt "GatewaySubnet". Om du namnger det något annat, misslyckas din gateway skapa;

1. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Skapa det virtuella nätverket.

   I det här exemplet är serverparametern -DnsServer valfri. Ingen ny DNS-server skapas när du anger ett värde. IP-adressen för DNS-servern som du anger måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till från ditt virtuella nätverk. I det här exemplet har vi använt en privat IP-adress, men förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden. Värdet du anger används av de resurser som du distribuerar till det virtuella nätverket, inte av P2S-anslutningen.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

   Ange de variabler som ska begära en dynamiskt tilldelad offentlig IP-adress.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"> </a>Konfigurera RADIUS-servern

Innan du skapar och konfigurerar den virtuella nätverksgatewayen bör RADIUS-servern konfigureras korrekt för autentisering.

1. Om du inte har distribuerat en RADIUS-server distribuerar du en. Information om distributionssteg finns i installationsguiden som tillhandahålls av RADIUS-leverantören.  
2. Konfigurera VPN-gatewayen som RADIUS-klient på RADIUS. När du lägger till den här RADIUS-klienten anger du det virtuella nätverket GatewaySubnet som du skapade. 
3. När RADIUS-servern har konfigurerats hämtar du RADIUS-serverns IP-adress och den delade hemlighet som RADIUS-klienter ska använda för att prata med RADIUS-servern. Om RADIUS-servern finns i Azure VNet använder du CERTIFIKAT-IP-ADRESSEN för RADIUS-serverns virtuella dator.

I artikeln [Network Policy Server (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) finns vägledning om hur du konfigurerar en Windows RADIUS-server (NPS) för AD-domänautentisering.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"> </a>Skapa VPN-gatewayen

Konfigurera och skapa VPN-gatewayen för ditt virtuella nätverk.

* -GatewayType måste vara "Vpn" och -VpnType måste vara "RouteBased".
* En VPN-gateway kan ta upp till 45 minuter att slutföra, beroende på [vilken gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) du väljer.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"> </a>Lägg till RADIUS-servern och klientadresspoolen
 
* -RadiusServer kan anges med namn eller IP-adress. Om du anger namnet och servern finns lokalt kanske VPN-gatewayen inte kan matcha namnet. Om så är fallet är det bättre att ange serverns IP-adress. 
* -RadiusSecret ska matcha det som är konfigurerat på RADIUS-servern.
* -VpnClientAddressPool är det intervall från vilket de anslutande VPN-klienterna får en IP-adress.Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Kontrollera att du har konfigurerat en tillräckligt stor adresspool.  

1. Skapa en säker sträng för RADIUS-hemligheten.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Du uppmanas att ange RADIUS-hemligheten. De tecken som du anger visas inte och ersätts i stället av tecknet "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Lägg till VPN-klientadresspoolen och RADIUS-serverinformationen.

   För SSTP-konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   För OpenVPN® konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   För IKEv2-konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   För SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"> </a>Ladda ner VPN-klientkonfigurationspaketet och konfigurera VPN-klienten

Med VPN-klientkonfigurationen kan enheter ansluta till ett virtuella nätverk via en P2S-anslutning.Information om hur du genererar ett VPN-klientkonfigurationspaket och konfigurerar VPN-klienten finns i [Skapa en VPN-klientkonfiguration för RADIUS-autentisering](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Anslut till Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Så här ansluter du från en Windows VPN-klient

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Ange dina domänuppgifter och klicka på Anslut. Ett popup-meddelande som begär förhöjda rättigheter visas. Acceptera det och ange autentiseringsuppgifterna.

   ![VPN-klient ansluter till Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Anslutningen upprättas.

   ![Anslutning upprättad](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Ansluta från en Mac VPN-klient

Från dialogrutan Nätverk letar du upp den klientprofil som du vill använda och klickar sedan på **Anslut**.

  ![Mac-anslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Så här verifierar du anslutningen

1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att IP-adressen som du fick är en av adresserna i klientadresspoolen för VPN för punkt-till-plats som du angav i konfigurationen. Resultatet är ungefär som i det här exemplet:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Information om felsökning av en P2S-anslutning finns i [Felsöka Azure point-to-site-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a><a name="faq"></a>Faq

Den här vanliga frågor och svar gäller P2S med RADIUS-autentisering

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).
