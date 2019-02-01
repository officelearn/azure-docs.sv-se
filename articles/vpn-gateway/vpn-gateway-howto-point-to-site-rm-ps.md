---
title: 'Ansluta en dator till en Azure-nätverk med punkt-till-plats och intern Azure-certifikatautentisering: PowerShell | Microsoft Docs'
description: Ansluta Windows- och Mac OS X-klienter på ett säkert sätt till ett virtuellt Azure-nätverk med P2S och självsignerade certifikat eller certifikat som är utfärdade av certifikatutgivare. I den här artikeln används PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.openlocfilehash: f688c0e277f807ff27731c103ca407807052c9d3
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508325"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med intern Azure-certifikatautentisering: PowerShell

Den här artikeln hjälper dig att ansluta enskilda klienter som kör Windows eller Mac OS X till ett Azure VNet på ett säkert sätt. Punkt-till-plats-VPN-anslutningar är användbara när du vill fjärransluta till ditt VNet, exempelvis när du distansarbetar från hemmet eller en konferens. Du kan också använda P2S i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet. Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol) eller IKEv2. Mer information om punkt-till-plats-VPN finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).

![Ansluta en dator till Azure VNet – punkt-till-plats-anslutningsdiagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Arkitektur

Ursprungliga autentiseringsanslutningar för Azure-certifikat från punkt-till-plats använder följande objekt, som du konfigurerar i den här övningen:

* En RouteBased VPN gateway.
* Den offentliga nyckeln (CER-fil) för ett rotcertifikat, som överförts till Azure. När certifikatet har laddats upp betraktas det som betrott och används för autentisering.
* Ett klientcertifikat som genereras från rotcertifikatet. Klientcertifikatet installeras på varje klientdator som ska ansluta till VNet. Det här certifikatet används för klientautentisering.
* En VPN-klientkonfiguration. VPN-klientkonfigurationsfilerna innehåller all information som krävs för att klienten ska kunna ansluta till det virtuella nätverket. Filerna konfigurerar den befintliga VPN-klienten som är inbyggd i operativsystemet. Alla klienter som ansluter måste vara konfigurerade med inställningarna i konfigurationsfilerna.

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>Exempelvärden

Du kan använda exempelvärdena för att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. Variablerna anges i avsnitt [1](#declare) i artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö.

* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16** och **10.254.0.0/16**<br>Det här exemplet använder mer än ett adressutrymme för att illustrera att den här konfigurationen fungerar med flera adressutrymmen. Flera adressutrymmen krävs dock inte för den här konfigurationen.
* **Namn på undernät: FrontEnd**
  * **Adressintervall för undernätet: 192.168.1.0/24**
* **Namn på undernät: BackEnd**
  * **Adressintervall för undernätet: 10.254.1.0/24**
* **Namn på undernät: GatewaySubnet**<br>Undernätsnamnet *GatewaySubnet* är obligatoriskt för att VPN-gatewayen ska fungera.
  * **GatewaySubnet-adressintervallet: 192.168.200.0/24** 
* **VPN-klientadresspool: 172.16.201.0/24**<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från VPN-klientadresspoolen.
* **Prenumeration:** Om du har mer än en prenumeration kan du kontrollera att du använder rätt.
* **Resursgrupp: TestRG**
* **Plats: USA, östra**
* **DNS-Server: IP-adress** för DNS-servern som du vill använda för namnmatchning. (valfritt)
* **GW-namn: Vnet1GW**
* **Namn på offentlig IP: VNet1GWPIP**
* **VpnType: Routningsbaserad** 

## <a name="declare"></a>1. Logga in och ange variabler

I det här avsnittet ska du logga in och deklarera de värden som används för den här konfigurationen. De deklarerade värdena används i exempelskripten. Om du vill ändrar du värdena så att de återspeglar din egen miljö. Eller så använder du de deklarerade värdena och går igenom stegen som en övning.

### <a name="sign-in"></a>Logga in

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Deklarera variabler

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs.

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

## <a name="ConfigureVNet"></a>2. Konfigurera ett virtuellt nätverk

1. Skapa en resursgrupp.

  ```azurepowershell-interactive
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

  ```azurepowershell-interactive
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Skapa det virtuella nätverket.

  I det här exemplet är serverparametern -DnsServer valfri. Ingen ny DNS-server skapas när du anger ett värde. IP-adressen för DNS-servern som du anger måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till från ditt virtuella nätverk. Det här exemplet använder en privat IP-adress, men förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden. Värdet som du anger används av de resurser som du distribuerar till det virtuella nätverket, inte av P2S-anslutningen eller VPN-klienten.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Ange variablerna för det virtuella nätverket som du skapade.

  ```azurepowershell-interactive
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats till din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

  Begär en dynamiskt tilldelad offentlig IP-adress.

  ```azurepowershell-interactive
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="creategateway"></a>3. Skapa VPN gateway

Konfigurera och skapa VNet-gatewayen för ditt VNet.

* -GatewayType måste vara **Vpn** och -VpnType måste vara **RouteBased**.
* -VpnClientProtocol används för att ange vilka typer av tunnlar som ska aktiveras. De två tunnelalternativen är **SSTP** och **IKEv2**. Du kan välja att aktivera den ena typen eller båda. Om du vill aktivera båda anger du båda namnen avgränsade med kommatecken. StrongSwan-klienten i Android och Linux och den inbyggda IKEv2 VPN-klienten i iOS och OSX använder endast IKEv2-tunnlar för att ansluta. Windows-klienter provar IKEv2 först, och går över till SSTP om det inte går att ansluta.
* Den virtuella nätverksgatewayen ”grundläggande” SKU: N stöder inte IKEv2 eller RADIUS-autentisering. Om du planerar att Mac-klienter ansluter till det virtuella nätverket, Använd inte en grundläggande SKU.
* Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken [gateway-sku](vpn-gateway-about-vpn-gateway-settings.md) du väljer. I det här exemplet används IKEv2.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="addresspool"></a>4. Lägga till VPN-klientadresspoolen

När VPN-gatewayen har skapats kan du lägga till VPN-klientadresspoolen. VPN-klientens adresspool är det adressintervall som VPN-klienterna får en IP-adress från när de ansluter. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. I det här exemplet deklareras VPN-klientens adresspool som en [variabel](#declare) i steg 1.

```azurepowershell-interactive
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5. Generera certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara ”betrodd”. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare/Personlig. Certifikatet används för att autentisera klienten när den upprättar en anslutning till det virtuella nätverket. 

Om du använder självsignerade certifikat, måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md). Om du inte har Windows 10 kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du skapar självsignerade rotcertifikat och klientcertifikat. Annars kommer de certifikat som du genererar inte att vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="cer"></a>1. Hämta .cer-filen för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2. Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6. Ladda upp informationen om den offentliga nyckeln för rotcertifikatet

Kontrollera att din VPN-gateway har skapats. När det är klart kan du ladda upp CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure. När en CER-fil har överförts kan Azure använda den för att autentisera klienter som har ett installerat klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan överföra ytterligare betrodda rotcertifikatfiler (upp till 20 stycken) senare om det behövs.

1. Deklarera variabeln för certifikatnamnet och ersätt värdet med ditt eget.

  ```azurepowershell-interactive
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. Ersätt sökvägen till filen med din egen och kör cmdlet:arna.

  ```azurepowershell-interactive
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```
3. Ladda upp informationen om den offentliga nyckeln till Azure. När certifikatinformationen har laddats upp betraktar Azure det som ett betrott rotcertifikat.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
  ```

## <a name="clientcertificate"></a>7. Installera ett exporterat klientcertifikat

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades.

Kontrollera att klientcertifikatet har exporterats som PFX-fil tillsammans med hela certifikatkedjan (standardinställningen). I annat fall finns inte rotcertifikatuppgifterna på klientdatorn och klienten kan inte autentiseras. 

Installationsanvisningar finns i [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installera ett klientcertifikat).

## <a name="clientconfig"></a>8. Konfigurera den inbyggda VPN-klienten

VPN-klientkonfigurationsfilerna innehåller inställningar för att konfigurera enheter så att de ansluter till ett virtuellt nätverk via en P2S-anslutning. Anvisningar som beskriver hur du genererar och installerar VPN-klientkonfigurationsfiler finns i [Create and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md) (Skapa och installera VPN-klientkonfigurationsfiler för intern Azure-certifikatautentisering med P2S-konfigurationer).

## <a name="connect"></a>9. Anslut till Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Så här ansluter du från en Windows VPN-klient

>[!NOTE]
>Du måste ha administratörsbehörigheter på den Windows-klientdator som du använder för att ansluta.
>
>

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. Klicka på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

  ![VPN-klient ansluter till Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Anslutningen upprättas.

  ![Anslutning upprättad](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Felsöka P2S-anslutningar på Windows-klienter

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Så här ansluter du från en Mac VPN-klient

Från dialogrutan Nätverk letar du upp den klientprofil som du vill använda och klickar sedan på **Anslut**.
Kontrollera [installera - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) detaljerade anvisningar. Om du har problem med att ansluta kan du kontrollera att den virtuella nätverksgatewayen inte använder en grundläggande SKU. Grundläggande SKU stöds inte för Mac-klienter.

  ![Mac-anslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Så här verifierar du anslutningen

Dessa anvisningar gäller för Windows-klienter.

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

## <a name="connectVM"></a>Ansluta till en virtuell dator

Dessa anvisningar gäller för Windows-klienter.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>Så här lägger du till eller tar bort ett rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kan klienter som har ett certifikat som genereras från rotcertifikatet inte autentisera och kommer inte att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure.

### <a name="addtrustedroot"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 CER-filer för rotcertifikat i Azure. Följande steg beskriver hur du lägger till ett rotcertifikat:

#### <a name="certmethod1"></a>Metod 1

Det här är den effektivaste metoden för att ladda upp ett rotcertifikat.

1. Förbered CER-filen för uppladdning:

  ```azurepowershell-interactive
  $filePathForCert = "C:\cert\P2SRootCert3.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
  ```
2. Ladda upp filen. Du kan bara ladda upp en fil i taget.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
  ```

3. Kontrollera att certifikatfilen har laddats upp:

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

#### <a name="certmethod2"></a>Metod 2

Den här metoden har fler steg än metod 1, men resultatet är samma. Den finns med om du behöver visa certifikatdata.

1. Skapa och förbered det nya rotcertifikatet som du ska lägga till i Azure. Exportera den offentliga nyckeln som en Base64-kodad X.509-fil (.CER) och öppna den i en textredigerare. Kopiera värdena, som du ser i följande exempel:

  ![certifikat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > När du kopierar certifikatdata ska du se till att kopiera texten som en kontinuerlig rad utan vagnreturer och radmatningar. Du kan behöva ändra vyn i textredigeraren till ”Visa symbol/Visa alla tecken” så att vagnreturer och radmatningar visas.
  >
  >

2. Ange certifikatnamnet och viktig information som en variabel. Ersätt informationen med din egen enligt följande exempel:

  ```azurepowershell-interactive
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Lägg till det nya rotcertifikatet. Du kan bara lägga till ett certifikat i taget.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. Du kan kontrollera att det nya certifikatet har lagts till korrekt med hjälp av följande exempel:

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="removerootcert"></a>Ta bort ett rotcertifikat

1. Deklarera variablerna.

  ```azurepowershell-interactive
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Ta bort certifikatet.

  ```azurepowershell-interactive
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. Använd följande exempel till att kontrollera att certifikatet togs bort ordentligt.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Återkalla ett klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Det här skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat, snarare än rotcertifikatet, så kan de andra certifikat som har genererats från rotcertifikatet fortfarande användas för autentisering.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="revokeclientcert"></a>Återkalla ett klientcertifikat

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke. Den här strängen deklareras som en variabel i nästa steg.
3. Deklarera variablerna. Deklarera tumavtrycket du hämtade i föregående steg.

  ```azurepowershell-interactive
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Lägg till tumavtrycket i listan med återkallade certifikat. När tumavtrycket har lagts till visas ett meddelande om att åtgärden lyckades.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Kontrollera att tumavtrycket har lagts till i listan över återkallade certifikat.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. När tumavtrycket har lagts till kan certifikatet inte längre användas för anslutning. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

### <a name="reinstateclientcert"></a>Återställa ett klientcertifikat

Du kan återställa ett klientcertifikat genom att ta bort tumavtrycket från listan över återkallade klientcertifikat.

1. Deklarera variablerna. Se till att du deklarerar rätt tumavtryck för det certifikat du vill återställa.

  ```azurepowershell-interactive
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Ta bort certifikatets tumavtryck från listan med återkallade certifikat.

  ```azurepowershell-interactive
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Kontrollera att tumavtrycket har tagits bort från listan över återkallade certifikat.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

P2S felsökningsinformation [Felsökning: Problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
