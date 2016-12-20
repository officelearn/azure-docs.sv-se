---
title: "Konfigurera en punkt-till-plats VPN-gateway-anslutning till ett virtuellt nätverk med hjälp av Resource Manager-distributionsmodellen | Microsoft Docs"
description: "Anslut säkert till Azure Virtual Network genom att skapa en VPN-gatewayanslutning från punkt till plats."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 41bba6660c52d4aa7d10d846ad65e1f6aa5e582c


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Planering och design](vpn-gateway-plan-design.md). 

Den här artikeln beskriver steg för steg hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning i Resource Manager-distributionsmodellen med PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

I det här scenariot skapar du ett virtuellt nätverk med en punkt-till-plats-anslutning. Anvisningarna beskriver också hur du genererar certifikat, som krävs för den här konfigurationen. En P2S-anslutning består av följande objekt: ett VNet med en VPN-gateway, en CER-fil för ett rotcertifikat (offentlig nyckel), ett klientcertifikat och VPN-konfigurationen på klienten. 

Vi använder följande värden för den här konfigurationen. Vi anger variablerna i avsnitt [1](#declare) i artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Exempelvärden
* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16** and **10.254.0.0/16**<br>I det här exemplet används mer än ett adressutrymme för att illustrera att den här konfigurationen fungerar med flera adressutrymmen. Flera adressutrymmen krävs dock inte för den här konfigurationen.
* **Undernätsnamn: FrontEnd**
  * **Adressintervall för undernätet: 192.168.1.0/24**
* **Undernätsnamn: BackEnd**
  * **Adressintervall för undernätet: 10.254.1.0/24**
* **Undernätsnamn: GatewaySubnet**<br>Undernätsnamnet *GatewaySubnet* är obligatoriskt för att VPN-gatewayen ska fungera.
  * **Adressintervall för undernätet: 192.168.200.0/24** 
* **VPN-klientadresspool: 172.16.201.0/24**<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från VPN-klientadresspoolen.
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp: TestRG**
* **Plats: Östra USA**
* **DNS-server: IP-adressen** för den DNS-server som du vill använda för namnmatchning.
* **GW-namn: Vnet1GW**
* **Offentligt IP-namn: VNet1GWPIP**
* **VPNType: RouteBased**

## <a name="before-beginning"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).
* Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs). När du arbetar med PowerShell i den här konfigurationen ska du kontrollera att du kör som administratör. 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>Del 1 – Logga in och ställ in variabler
I det här avsnittet ska du logga in och deklarera värdena som används i den här konfigurationen. De deklarerade värdena används i exempelskripten. Om du vill ändrar du värdena så att de återspeglar din egen miljö. Eller så använder du de deklarerade värdena och går igenom stegen som en övning.

1. Logga in på ditt Azure-konto i PowerShell-konsolen. Den här cmdleten uppmanar dig att ange inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.
   
        Login-AzureRmAccount 
2. Hämta en lista över dina Azure-prenumerationer.
   
        Get-AzureRmSubscription
3. Ange den prenumeration som du vill använda. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs.
   
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

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>Del 2 – Konfigurera ett VNet
1. Skapa en resursgrupp.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Skapa det virtuella nätverket. Den angivna DNS-servern måste vara en DNS-server som kan matcha namn för de resurser som du ansluter till. I det här exemplet använder vi en offentlig IP-adress. Använd dina egna värden.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Ange variablerna för det virtuella nätverket som du skapade.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Begär en dynamiskt tilldelad offentlig IP-adress. Den här IP-adressen krävs för att gatewayen ska fungera korrekt. Senare ska du ansluta gatewayen till gateway-IP-konfigurationen.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>Del 3 – certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du kan exportera offentliga certifikatdata (inte den privata nyckeln) som en Base-64-kodad X.509.cer-fil från antingen ett rotcertifikat som genererats av en företagscertifikatlösning, eller genom att använda ett självsignerat rotcertifikat. Du kan sedan importera offentliga certifikatdata från rotcertifikatet till Azure. Dessutom måste du generera ett klientcertifikat från rotcertifikatet för klienter. Varje klient som vill ansluta till det virtuella nätverket med en P2S-anslutning måste ha ett klientcertifikat, som har genererats från rotcertifikatet, installerat.

### <a name="a-namecera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. Hämta .cer-filen för rotcertifikatet
Du måste hämta offentliga data för det rotcertifikat som du vill använda.

* Om du använder ett företagscertifikatsystem hämtar du CER-filen för det rotcertifikat som du vill använda. 
* Om du inte använder en företagscertifikatlösning, måste du generera ett självsignerat rotcertifikat. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

1. Om du vill hämta en .cer-fil från ett certifikat öppnar du **certmgr.msc** och letar upp rotcertifikatet. Högerklicka på det självsignerade rotcertifikatet, klicka på **alla uppgifter** och klicka sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Nej, exportera inte den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** Klicka sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet.

### <a name="a-namegeneratea2-generate-the-client-certificate"></a><a name="generate"></a>2. Generera klientcertifikatet
Nu ska du generera klientcertifikaten. Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla i stället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera. Klientcertifikat installeras på varje klientdator senare i den här övningen.

* Om du använder en lösning för företagscertifikat, genererar du ett klientcertifikat med det allmänna namnvärdesformatet 'name@yourdomain.com', istället för NetBIOS-formatet ”DOMÄN\användardnamn”. 
* Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Exportera klientcertifikat
Ett klientcertifikat krävs för autentisering. När du har genererat klientcertifikatet exporterar du det. Klientcertifikatet du exporterar installeras senare på varje klientdator.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.

### <a name="a-nameuploada4-upload-the-root-certificate-cer-file"></a><a name="upload"></a>4. Ladda upp .cer-filen med rotcertifikatet
Deklarera variabeln för certifikatnamnet och ersätt värdet med ditt eget:

        $P2SRootCertName = "Mycertificatename.cer"

Lägg till offentliga certifikatdata från rotcertifikatet till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket. 

Ersätt sökvägen till filen med din egen och kör cmdlet:arna.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>Del 4 – Skapa VPN-gatewayen
Konfigurera och skapa VNet-gatewayen för ditt VNet. *-GatewayType* måste vara **Vpn** och *-VpnType* måste vara **RouteBased**. Det här steget kan ta upp till 45 minuter.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Del 5 – Ladda ned VPN-klientkonfigurationspaketet
Klienter som ansluter till Azure via P2S måste ha både ett klientcertifikat och ett VPN-klientkonfigurationspaket installerat. VPN-klientkonfigurationspaket är tillgängliga för Windows-klienter. VPN-klientpaketet innehåller information för att konfigurera VPN-klientprogramvaran som är inbyggd i Windows och är specifikt för det VPN-nätverk som du vill ansluta till. Paketet installerar inte någon ytterligare programvara. Mer information finns i avsnittet [Vanliga frågor och svar om VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Efter att gatewayen har skapats, kan du hämta klientpaketet för konfiguration. Det här exemplet hämtar paketet för 64-bitars klienter. Om du vill hämta 32-bitars klienten ersätter du Amd64 med x86. Du kan också hämta VPN-klienten med hjälp av Azure-portalen.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. PowerShell-cmdleten returnerar en URL-länk. Det här är ett exempel på hur URL:en som returneras ser ut:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Kopiera och klistra in länken som returneras i en webbläsaren för att ladda ned paketet. Installera sedan paketet på klientdatorn. Om ett SmartScreen-fönster öppnas klickar du på **Mer info** och sedan **Kör ändå ** för att du ska kunna installera paketet.
4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär som detta exempel: 
   
    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>Del 6 – Installera klientcertifikatet
Varje klientdator måste ha ett klientcertifikat för att kunna autentisera. När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Kopiera .pfx-filen till klientdatorn.
2. Dubbelklicka på .pfx-filen för att installera den. Ändra inte installationsplatsen.

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>Del 7 – Ansluta till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
   
    ![VPN-klientanslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. Nu bör anslutningen upprättas.
   
    ![Anslutning upprättad](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connection established")

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>Del 8 – Verifiera anslutningen
1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att IP-adressen som du fick är en av adresserna i klientadresspoolen för VPN för punkt-till-plats som du angav i konfigurationen. Resultaten bör likna följande:
   
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

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Så här lägger du till eller tar bort ett betrott rotcertifikat
Certifikat används för att autentisera VPN-klienter för punkt-till-plats-VPN. Följande steg beskriver hur du lägger till och tar bort rotcertifikat. När du lägger till en Base64-kodad X.509-fil (.cer) till Azure uppmanar du Azure att lita på rotcertifikatet som filen representerar. 

Du kan lägga till eller ta bort betrodda rotcertifikat med hjälp av PowerShell eller på Azure-portalen. Om du vill använda Azure-portalen går du till **din VNet-gateway > Inställningar > Punkt-till-plats-konfiguration > Rotcertifikat**. Följande steg beskriver hur du utför dessa åtgärder med hjälp av PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat
Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Du lägger till ett rotcertifikat genom att följa stegen nedan.

1. Skapa och förbered det nya rotcertifikatet som du ska lägga till i Azure. Exportera den offentliga nyckeln som en Base64-kodad X.509-fil (.CER) och öppna den i en textredigerare. Kopiera bara avsnittet som visas nedan. 
   
    Kopiera värdena, som du ser i följande exempel:
   
    ![certifikat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. Ange certifikatnamnet och viktig information som en variabel. Ersätt informationen med din egen enligt följande exempel:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Lägg till det nya rotcertifikatet. Du kan bara lägga till ett certifikat i taget.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Du kan kontrollera att det nya certifikatet har lagts till korrekt genom att använda följande cmdlet.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Så här tar du bort ett betrott rotcertifikat
Du kan ta bort betrodda rotcertifikat från Azure. När du tar bort ett betrott certifikat kan klientcertifikaten som genererades från rotcertifikatet inte längre ansluta till Azure via punkt-till-plats. Om du vill att klienter ska kunna ansluta måste du installera ett nytt klientcertifikat som genereras från ett certifikat som är betrott i Azure.

1. Du kan ta bort ett betrott rotcertifikat genom att modifiera följande exempel:
   
    Deklarera variablerna.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Ta bort certifikatet.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Använd följande cmdlet för att kontrollera att certifikatet har tagits bort. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Så här hanterar du listan med återkallade klientcertifikat
Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Om du tar bort CER-filen för ett rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats av det återkallade rotcertifikatet. Om du vill återkalla ett specifikt klientcertifikat, inte roten, kan du göra det. På så sätt är de andra certifikaten som har genererats från rotcertifikatet fortfarande giltiga.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="revoke-a-client-certificate"></a>Återkalla ett klientcertifikat
1. Hämta tumavtrycket för det klientcertifikat som du vill återkalla.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. Lägg till tumavtrycket i listan över återkallade tumavtryck.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Kontrollera att tumavtrycket har lagts till i listan över återkallade certifikat. Du måste lägga till ett tumavtryck i taget.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Återställa ett klientcertifikat
Du kan återställa ett klientcertifikat genom att ta bort tumavtrycket från listan över återkallade klientcertifikat.

1. Ta bort tumavtrycket från listan över tumavtryck för återkallade klientcertifikat.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Kontrollera att tumavtrycket har tagits bort från listan över återkallade certifikat.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Dec16_HO1-->


