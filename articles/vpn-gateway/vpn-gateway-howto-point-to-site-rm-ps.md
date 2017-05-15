---
title: "Ansluta en dator till ett virtuellt Azure-nätverk med hjälp av punkt-till-plats: PowerShell | Microsoft-dokument"
description: "Anslut en dator till ditt virtuella Azure-nätverk på ett säkert sätt genom att skapa en VPN-gatewayanslutning från punkt till plats."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 8e6b1dc7e17fe41db1deb03417083cfc891afa86
ms.contentlocale: sv-se
ms.lasthandoff: 05/08/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av PowerShell


Den här artikeln visar dig hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning i Resource Manager-distributionsmodellen med PowerShell. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

![Ansluta en dator till Azure VNet – punkt-till-plats-anslutningsdiagram](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

## <a name="before-beginning"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).
* Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="example"></a>Exempelvärden

Du kan använda exempelvärdena för att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. Vi anger variablerna i avsnitt [1](#declare) i artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö. 

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
* **DNS-server: IP-adressen** för den DNS-server som du vill använda för namnmatchning.
* **GW-namn: Vnet1GW**
* **Offentligt IP-namn: VNet1GWPIP**
* **VPNType: RouteBased** 

## <a name="declare"></a>1 – Logga in och ställ in variabler

I det här avsnittet ska du logga in och deklarera värdena som används i den här konfigurationen. De deklarerade värdena används i exempelskripten. Om du vill ändrar du värdena så att de återspeglar din egen miljö. Eller så använder du de deklarerade värdena och går igenom stegen som en övning.

1. Öppna PowerShell-konsolen med förhöjd behörighet och logga in på ditt Azure-konto. Denna cmdlet uppmanar dig att ange autentiseringsuppgifter för inloggningen. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Hämta en lista över dina Azure-prenumerationer.

  ```powershell  
  Get-AzureRmSubscription
  ```
3. Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs.

  ```powershell
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
  $DNS = "8.8.8.8"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2 – Konfigurera ett virtuellt nätverk

1. Skapa en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Skapa det virtuella nätverket. <br>DNS-servern är valfri. Att ange det här värdet skapar ingen ny DNS-server. Konfigurationspaketet för klienten som du genererar i ett senare skede innehåller IP-adressen för DNS-servern som du anger i den här inställningen. Om du behöver uppdatera listan över DNS-servrar i framtiden kan du generera och installera nya konfigurationspaket för VPN-klienter som reflekterar den nya listan.<br>Den angivna DNS-servern måste vara en DNS-server som kan matcha namn för de resurser som du ansluter till. I det här exemplet använder vi en offentlig IP-adress. Använd dina egna värden.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. Ange variablerna för det virtuella nätverket som du skapade.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

  Begär en dynamiskt tilldelad offentlig IP-adress.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="Certificates"></a>3 – Generera certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er.

### <a name="cer"></a>Steg 1 – Hämta CER-filen för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>Steg 2 – Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>4 – Ladda upp .cer-filen med rotcertifikatet

Överför .cer-filen (som innehåller information om den offentliga nyckeln) för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket. Du kan överföra ytterligare offentliga nycklar för rotcertifikat senare om det behövs.

1. Deklarera variabeln för certifikatnamnet och ersätt värdet med ditt eget.

  ```powershell
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. Ersätt sökvägen till filen med din egen och kör cmdlet:arna.

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>5 – Skapa VPN-gatewayen

Konfigurera och skapa VNet-gatewayen för ditt VNet. *-GatewayType* måste vara **Vpn** och *-VpnType* måste vara **RouteBased**. I det här exemplet associeras den offentliga nyckeln för rotcertifikatet med VPN-gatewayen. En VPN-gateway kan behöva upp till 45 minuter för att slutföras.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```

## <a name="clientconfig"></a>6 – Ladda ned VPN-klientkonfigurationspaketet

För att ansluta till ett virtuellt nätverk med hjälp av ett VPN för punkt-till-plats måste varje klient installera ett VPN-klientkonfigurationspaket. Paketet installerar inte en VPN-klient. Du kan använda samma VPN-klientkonfigurationspaket på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten. En lista över klientoperativsystem som stöds finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

Konfigurationspaketet konfigurerar den inbyggda VPN-klienten för Windows med inställningarna som behövs för att ansluta till det virtuella nätverket, och om du har angett en DNS-server för ditt virtuella nätverk innehåller den DNS-serverns IP-adress som klienten använder för namnmatchning. Om du ändrar den angivna DNS-servern senare, efter att du har genererat konfigurationspaketet för klienten, bör du generera ett nytt konfigurationpaket för klienten som du kan använda för att installera på klientdatorerna.

1. När gatewayen har skapats kan du generera och hämta klientkonfigurationspaketet. Det här exemplet hämtar paketet för 64-bitars klienter. Om du vill hämta 32-bitars klienten ersätter du Amd64 med x86. Du kan också hämta VPN-klienten med hjälp av Azure-portalen.

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. Kopiera och klistra in länken som returneras till en webbläsare för att ladda ned paketet. Glöm inte att ta bort de omgivande citattecknen. 
3. Ladda ned och installera paketet på klientdatorn. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

## <a name="clientcertificate"></a>7 – Installera ett exporterat klientcertifikat

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. Vanligtvis behöver du bara dubbelklicka på certifikatet och installera det. Mer information finns i [Installera ett exporterat klientcertifikat](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>8 – Ansluta till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. Klicka på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

  ![VPN-klient ansluter till Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Anslutningen upprättas.

  ![Anslutning upprättad](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

Om du har problem med att ansluta kontrollerar du följande:

- Öppna **Hantera användarcertifikat** och gå till **Betrodda rotcertifikatutfärdare\Certifikat**. Kontrollera att rotcertifikatet visas i listan. Rotcertifikatet måste finnas för att autentiseringen ska fungera. När du exporterar ett klientcertifikat av typen .pfx med hjälp av standardvärdet ”Ta med om möjligt alla certifikat i certifieringssökvägen” exporteras också rotcertifikatsinformationen. När du installerar klientcertifikatet installeras också rotcertifikatet på klientdatorn. 

- Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan.  

## <a name="verify"></a>9 – Verifiera din anslutning

1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att IP-adressen som du fick är en av adresserna i klientadresspoolen för VPN för punkt-till-plats som du angav i konfigurationen. Resultatet är ungefär som i det här exemplet:
   
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


## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>Lägga till eller ta bort ett rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kommer klienter som har ett certifikat som genereras från den roten inte att kunna autentisera, och därmed kommer de inte heller att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure.

### <a name="to-add-a-root-certificate"></a>Lägga till ett rotcertifikat

Du kan lägga till upp till 20 CER-filer för rotcertifikat i Azure. Följande steg beskriver hur du lägger till ett rotcertifikat:

1. Skapa och förbered det nya rotcertifikatet som du ska lägga till i Azure. Exportera den offentliga nyckeln som en Base64-kodad X.509-fil (.CER) och öppna den i en textredigerare. Kopiera värdena, som du ser i följande exempel:

  ![certifikat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > När du kopierar certifikatdata ska du se till att kopiera texten som en kontinuerlig rad utan vagnreturer och radmatningar. Du kan behöva ändra vyn i textredigeraren till ”Visa symbol/Visa alla tecken” så att vagnreturer och radmatningar visas.
  >
  >

2. Ange certifikatnamnet och viktig information som en variabel. Ersätt informationen med din egen enligt följande exempel:

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Lägg till det nya rotcertifikatet. Du kan bara lägga till ett certifikat i taget.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. Du kan kontrollera att det nya certifikatet har lagts till korrekt med hjälp av följande exempel:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="to-remove-a-root-certificate"></a>Ta bort ett rotcertifikat

1. Deklarera variablerna.

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Ta bort certifikatet.

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. Använd följande exempel till att kontrollera att certifikatet togs bort ordentligt.

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Återkalla ett klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Detta skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat, snarare än rotcertifikatet, så kan de andra certifikat som har genererats från rotcertifikatet fortfarande användas för autentisering.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="to-revoke-a-client-certificate"></a>Återkalla du ett klientcertifikat

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke. Detta deklareras som en variabel i nästa steg.
3. Deklarera variablerna. Deklarera tumavtrycket du hämtade i föregående steg.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Lägg till tumavtrycket i listan med återkallade certifikat. När tumavtrycket har lagts till visas ett meddelande om att åtgärden lyckades.

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Kontrollera att tumavtrycket har lagts till i listan över återkallade certifikat.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. När tumavtrycket har lagts till kan certifikatet inte längre användas för anslutning. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

### <a name="to-reinstate-a-client-certificate"></a>Så här återställer du ett klientcertifikat

Du kan återställa ett klientcertifikat genom att ta bort tumavtrycket från listan över återkallade klientcertifikat.

1. Deklarera variablerna. Se till att du deklarerar rätt tumavtryck för det certifikat du vill återställa.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Ta bort certifikatets tumavtryck från listan med återkallade certifikat.

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Kontrollera att tumavtrycket har tagits bort från listan över återkallade certifikat.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

