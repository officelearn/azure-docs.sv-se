---
title: 'Ansluta en dator till ett virtuellt nätverk med punkt-till-plats- och RADIUS-autentisering: PowerShell | Azure'
description: Ansluta Windows- och Mac OS X-klienter på ett säkert sätt till en virtuell netowrk med P2S och RADIUS-autentisering.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: anzaman
ms.openlocfilehash: df7afe9324831ffb8e79d7320f2c716ed18a7b4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719693"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med RADIUS-autentisering: PowerShell

Den här artikeln visar hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning som använder RADIUS-autentisering. Den här konfigurationen är endast tillgänglig för distributionsmodellen i Resource Manager.

Med en VPN-gateway med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. Punkt-till-plats VPN-anslutningar är användbara när du vill ansluta till ditt VNet från en annan plats, till exempel när du distansarbetar från hemmet eller en konferens. En P2S-VPN-anslutning är också en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet.

En P2S VPN-anslutning startas från Windows- och Mac-enheter. Anslutande klienter kan använda följande autentiseringsmetoder:

* RADIUS-server
* Inbyggd certifikatautentisering för VPN-Gateway

Den här artikeln hjälper dig att konfigurera en P2S-konfiguration med autentisering med hjälp av RADIUS-server. Om du vill autentisera med hjälp av genererade certifikat och inbyggd certifikatautentisering för VPN-gateway i stället kan du läsa [konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med VPN gateway inbyggd certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md).

![Anslutningsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol) eller IKEv2.

* SSTP är en SSL-baserad VPN-tunnel som endast stöds på Windows-klientplattformar. Den kan ta sig förbi brandväggar, vilket gör den till ett utmärkt alternativ för att ansluta till Azure var som helst. På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard.

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

P2S-anslutningar kräver följande:

* En RouteBased VPN gateway. 
* En RADIUS-server för att hantera autentisering av användare. RADIUS-servern kan vara distribueras lokalt, eller i Azure VNet.
* En VPN-klientkonfigurationspaketet för Windows-enheter som ska ansluta till det virtuella nätverket. Ett konfigurationspaket för VPN-klienten innehåller de inställningar som krävs för en VPN-klient att ansluta via P2S.

## <a name="aboutad"></a>Om Active Directory (AD) Domain-autentisering för P2S VPN

AD Domain authentication tillåter användare att logga in på Azure med hjälp av domänautentiseringsuppgifterna organisation. Det krävs en RADIUS-server som är integrerad med AD-servern. Organisationer kan även använda sina befintliga RADIUS-distributionen.
 
RADIUS-servern kan finnas lokalt eller i ditt Azure VNet. VPN-gateway fungerar som en direkt och vidarebefordran autentiseringsmeddelanden fram och tillbaka mellan RADIUS-servern och enheten som ansluter under autentiseringen. Det är viktigt för VPN-gateway för att kunna nå RADIUS-servern. Om RADIUS-servern är finns lokalt, krävs en VPN plats-till-plats-anslutning från Azure till lokal plats.

Förutom de Active Directory, kan en RADIUS-server också integreras med andra system för extern Identitetshantering. Detta öppnar gott om autentiseringsalternativ för punkt-till-plats-VPN, inklusive alternativ för MFA. Kontrollera på RADIUS-server tillverkarens dokumentation för att hämta listan över identitetssystem som den kan integreras med.

![Anslutningsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Endast en VPN plats-till-plats-anslutning kan användas för att ansluta till en RADIUS-server lokalt. En ExpressRoute-anslutning kan inte användas.
>
>

## <a name="before"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

* Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="log-in"></a>Logga in

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>Exempelvärden

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
* **DNS-Server: IP-adress** för DNS-servern som du vill använda för namnmatchning för ditt virtuella nätverk. (valfritt)
* **GW-namn: Vnet1GW**
* **Offentligt IP-namn: VNet1GWPIP**
* **VPNType: RouteBased** 

## 1. <a name="vnet"></a>Skapa resursgrupp, VNet och offentlig IP-adress

Följande steg kan du skapa en resursgrupp och ett virtuellt nätverk i resursgruppen med tre undernät. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt ”GatewaySubnet”. Om du ger det något annat, misslyckas skapa gatewayen.

1. Skapa en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Skapa det virtuella nätverket.

  I det här exemplet är serverparametern -DnsServer valfri. Ingen ny DNS-server skapas när du anger ett värde. IP-adressen för DNS-servern som du anger måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till från ditt virtuella nätverk. I det här exemplet har vi använt en privat IP-adress, men förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden. Värdet som du anger används av de resurser som du distribuerar till det virtuella nätverket, inte av P2S-anslutning.

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

  Ange variablerna för att begära en dynamiskt tilldelad offentlig IP-adress.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Konfigurera RADIUS-servern

Innan du skapar och konfigurerar den virtuella nätverksgatewayen, ska RADIUS-servern vara korrekt konfigurerat för autentisering.

1. Om du inte har en RADIUS-server som har distribuerats kan du distribuera en. Distributionssteg finns installationsguide som tillhandahålls av leverantören RADIUS.  
2. Konfigurera VPN-gateway som en RADIUS-klient på RADIEN. Ange det virtuella nätverket GatewaySubnet som du skapade när du lägger till den här RADIUS-klienten. 
3. När RADIUS-servern har konfigurerats kan du hämta RADIUS-serverns IP-adress och den delade hemligheten som RADIUS-klienter ska använda för att kommunicera med RADIUS-servern. Om RADIUS-servern är i Azure VNet, kan du använda CA IP-Adressen för RADIUS-server-dator.

Den [nätverksprincipserver (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) artikeln innehåller råd om hur du konfigurerar en Windows-RADIUS-server (NPS) för AD-domain-autentisering.

## 3. <a name="creategw"></a>Skapa VPN-gateway

Konfigurera och skapa VPN-gatewayen för ditt virtuella nätverk.

* -GatewayType måste vara ”Vpn” och - VpnType måste vara 'RouteBased'.
* En VPN-gateway kan ta upp till 45 minuter att slutföra, beroende på den [gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) du väljer.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Lägg till RADIUS-servern och klienten adresspool
 
* -RadiusServer kan anges efter namn eller IP-adress. Om du anger namnet och servern finns på plats, sedan att VPN-gatewayen inte kunna matcha namnet. Om så är fallet är det bättre att ange IP-adressen för servern. 
* -RadiusSecret måste matcha vad är konfigurerad på RADIUS-servern.
* -VpnCientAddressPool är intervallet som ansluter VPN-klienterna får en IP-adress. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Kontrollera att du har en tillräckligt stor-adresspool.  

1. Skapa en säker sträng för RADIEN hemliga.

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Du uppmanas att ange RADIUS-hemlighet. Tecknen som du anger kommer inte att visas och i stället kommer att ersättas av den ”*” tecken.

  ```powershell
  RadiusSecret:***
  ```
3. Lägga till klientadresspoolen för VPN- och RADIUS-serverinformation.

  För SSTP konfigurationer:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  För IKEv2-konfigurationer:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  För SSTP och IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Hämta konfigurationspaketet för VPN-klienten och konfigurera VPN-klienten

VPN-klientkonfiguration kan enheter som ansluter till ett virtuellt nätverk via en P2S-anslutning. För att generera ett konfigurationspaket för VPN-klienten och konfigurera VPN-klienten, se [skapa en VPN-klientkonfiguration för RADIUS-autentisering](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Anslut till Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Så här ansluter du från en Windows VPN-klient

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Ange dina autentiseringsuppgifter för domänen och klicka på ”Anslut”. Visas ett popup-meddelande som begär utökade behörigheter. Godkänn den och ange autentiseringsuppgifter.

  ![VPN-klient ansluter till Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Anslutningen upprättas.

  ![Anslutning upprättad](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Ansluta från en Mac VPN-klient

Från dialogrutan Nätverk letar du upp den klientprofil som du vill använda och klickar sedan på **Anslut**.

  ![Mac-anslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Så här verifierar du anslutningen

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

Om du vill felsöka en P2S-anslutning, se [punkt-till-plats-anslutningar i felsökning av Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Vanliga frågor och svar

Den här vanliga Frågorna gäller för P2S med hjälp av RADIUS-autentisering

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).
