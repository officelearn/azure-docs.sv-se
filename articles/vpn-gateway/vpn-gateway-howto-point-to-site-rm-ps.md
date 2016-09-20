<properties 
   pageTitle="Konfigurera en punkt-till-plats-VPN-anslutning till ett virtuellt nätverk med hjälp av Resource Manager-distributionsmodellen | Microsoft Azure"
   description="Anslut säkert till ditt virtuella Azure-nätverk genom att skapa en VPN-anslutning från punkt till plats."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc" />

# Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av PowerShell

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal – Klassisk](vpn-gateway-point-to-site-create.md)

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

Den här artikeln beskriver steg för steg hur du skapar ett VNet med en punkt-till-plats-anslutning i **Resource Manager-distributionsmodellen**. Stegen kräver PowerShell. För närvarande kan du inte skapa den här lösningen från slutpunkt-till-slutpunkt på Azure-portalen.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Planering och design](vpn-gateway-plan-design.md).



**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Distributionsmodeller och verktyg för punkt-till-plats-anslutningar**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")


## Om den här konfigurationen

I det här scenariot ska du skapa ett virtuellt nätverk med en punkt-till-plats-anslutning. Anvisningarna beskriver också hur du genererar certifikat, som krävs för den här konfigurationen. En P2S-anslutning består av följande objekt: ett VNet med en VPN-gateway, en CER-fil för ett rotcertifikat (offentlig nyckel), ett klientcertifikat och VPN-konfigurationen på klienten. 

Vi använder följande värden för den här konfigurationen. Vi anger variablerna i avsnitt [1](#declare) i artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö. 

- Namn: **VNet1**, med adressutrymmena **192.168.0.0/16** och **10.254.0.0/16**. Observera att du kan använda mer än ett adressutrymme för ett VNet.
- Undernätsnamn: **FrontEnd**, med **192.168.1.0/24**
- Undernätsnamn: **BackEnd**, med **10.254.1.0/24**
- Undernätsnamn: **GatewaySubnet**, med **192.168.200.0/24**. Undernätsnamnet *GatewaySubnet* är obligatoriskt för att gatewayen ska fungera. 
- VPN-klientadresspool: **172.16.201.0/24**. VPN-klienter som ansluter till VNet med den här punkt-till-plats-anslutningen får en IP-adress från den här poolen.
- Prenumeration: Kontrollera att du har rätt prenumeration om du har mer än en.
- Resursgrupp: **TestRG**
- Plats: **East US**
- DNS-server: **IP-adressen** för den DNS-server som du vill använda för namnmatchning.
- GW-namn: **GW**
- Namn på offentlig IP-adress: **GWIP**
- VpnType: **RouteBased**


## Innan du börjar

- Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Installera PowerShell-cmdlets för Azure Resource Manager (1.0.2 eller senare). Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).

## <a name="declare"></a>Del 1 – Logga in och ange variabler

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
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $P2SRootCertName = "ARMP2SRootCert.cer"


## Del 2 – Konfigurera ett VNet 


1. Skapa en resursgrupp.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som deklarerades ovan.

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

## Del 3 – Lägga till betrodda certifikat

Azure autentiserar klienter som vill ansluta via P2S med hjälp av certifikat. Du kan importera CER-filen (den offentliga nyckeln) för ett rotcertifikat till Azure. När du lägger till en Base64-kodad X.509-fil (.cer) till Azure uppmanar du Azure att lita på rotcertifikatet som filen representerar.

Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Om du inte använder en CA-företagslösning kan du skapa ett självsignerat rotcertifikat. En metod för att skapa ett självsignerat certifikat är makecert. Anvisningar för hur du skapar ett självsignerat rotcertifikat med *makecert* finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

Oavsett hur du har fått certifikatet laddar du upp CER-filen för certifikatet till Azure och genererar även klientcertifikat som ska installeras på de klienter som vill ansluta. Du installerar inte det självsignerade certifikatet direkt på klienten. Du kan generera klientcertifikat senare, i avsnittet [Klientkonfiguration](#cc) i den här artikeln.
        
Du kan lägga till upp till 20 betrodda certifikat i Azure. Du hämtar den offentliga nyckeln genom att exportera certifikatet som en Base64-kodad X.509-fil (.cer). Ett sätt att exportera till cer. är att öppna **certmger.msc** och leta upp certifikatet i Personal/Certificates. Högerklicka på och exportera utan den privata nyckeln som ”Base64-kodad X.509 (.CER)”. Anteckna sökvägen där du exporterade till CER-filen. Det här är ett exempel på hur du hämtar Base64-strängrepresentationen av certifikatet. 

Lägg till det betrodda certifikatet till Azure. Du måste använda din egen CER-sökväg i det här steget. Var särskilt uppmärksam på variabeln $P2SRootCertName = "ARMP2SRootCert.cer" som du angav i del 1 i den här artikeln. Om ditt certifikat har ett annat namn ändrar du variabeln.
    
        $filePathForCert = "pasteYourCerFilePathHere"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## Del 4 – Skapa VPN-gatewayen

Konfigurera och skapa VNet-gatewayen för ditt VNet. *-GatewayType* måste vara **Vpn** och *-VpnType* måste vara **RouteBased**. Det här steget kan ta upp till 45 minuter.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Del 5 – Ladda ned VPN-klientkonfigurationspaketet

Klienter som ansluter till Azure via P2S måste ha både ett klientcertifikat och ett VPN-klientkonfigurationspaket installerat. VPN-klientkonfigurationspaket är tillgängliga för Windows-klienter. VPN-klientpaketet innehåller information för att konfigurera VPN-klientprogramvaran som är inbyggd i Windows och är specifikt för det VPN-nätverk som du vill ansluta till. Paketet installerar inte någon ytterligare programvara. Mer information finns i avsnittet [Vanliga frågor och svar om VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. När gatewayen har skapats använder du följande exempel för att hämta klientkonfigurationspaketet:

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. PowerShell-cmdleten returnerar en URL-länk. Det här är ett exempel på hur URL:en som returneras ser ut:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Kopiera och klistra in länken som returneras i en webbläsaren för att ladda ned paketet. Installera sedan paketet på klientdatorn.

4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär så här: 

    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="cc"></a>Del 6 – Installera klientcertifikatet
    
Generera och installera klientcertifikaten (*.pfx) som skapas från rotcertifikatet på klientdatorerna. Du kan använda valfri installationsmetod.

Om du använder ett självsignerat rotcertifikat och är osäker på hur du skapar ett klientcertifikat kan du läsa [den här artikeln](vpn-gateway-certificates-point-to-site.md). Om du arbetar med en företagslösning utfärdar du klientcertifikaten med det vanliga namnvärdeformatet 'namn@dindomän.com' i stället för med NetBIOS-formatet 'domännamn\användarnamn'.

Du kan installera ett klientcertifikat direkt på en dator genom att dubbelklicka på PFX-filen.

## Del 7 – Ansluta till Azure

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 

2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

    ![VPN-klient 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")

3. Nu bör anslutningen upprättas.

    ![VPN-klient 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## Del 8 – Verifiera anslutningen

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

## Så här lägger du till eller tar bort ett betrott rotcertifikat

Certifikat används för att autentisera VPN-klienter för punkt-till-plats-VPN. Följande steg beskriver hur du lägger till och tar bort rotcertifikat. När du lägger till en Base64-kodad X.509-fil (.cer) till Azure uppmanar du Azure att lita på rotcertifikatet som filen representerar. 

Du kan lägga till eller ta bort betrodda rotcertifikat med hjälp av PowerShell eller på Azure-portalen. Om du vill använda Azure-portalen går du till **din VNet-gateway > Inställningar > Punkt-till-plats-konfiguration > Rotcertifikat**. Stegen nedan beskriver hur du utför dessa åtgärder med hjälp av PowerShell. 

### Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Du lägger till ett rotcertifikat genom att följa stegen nedan.

1. Skapa och förbered det nya rotcertifikatet som du ska lägga till i Azure. Exportera den offentliga nyckeln som en Base64-kodad X.509-fil (.CER) och öppna den i en textredigerare. Kopiera bara avsnittet som visas nedan. 
 
    Kopiera värdena, som du ser i följande exempel.

    ![certifikat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
    
2. I exemplet nedan anger du certifikatnamnet och viktig information som en variabel. Ersätt informationen med din egen information.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Lägg till det nya rotcertifikatet. Du kan bara lägga till ett certifikat i taget.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Du kan kontrollera att det nya certifikatet har lagts till korrekt genom att använda följande cmdlet.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

### Så här tar du bort ett betrott rotcertifikat

Du kan ta bort betrodda rotcertifikat från Azure. När du tar bort ett betrott certifikat kan klientcertifikaten som genererades från rotcertifikatet inte längre ansluta till Azure via punkt-till-plats. Om du vill att klienter ska kunna ansluta måste du installera ett nytt klientcertifikat som genereras från ett certifikat som är betrott i Azure.

1. Du kan ta bort ett betrott rotcertifikat genom att modifiera följande exempel:

    Deklarera variablerna.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Ta bort certifikatet.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Använd följande cmdlet för att kontrollera att certifikatet har tagits bort. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

## Så här hanterar du listan över återkallade klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Om du tar bort CER-filen för ett rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats av det återkallade rotcertifikatet. Om du vill återkalla ett specifikt klientcertifikat, inte roten, kan du göra det. På så sätt är de andra certifikaten som har genererats från rotcertifikatet fortfarande giltiga. Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### Återkalla ett klientcertifikat

1. Hämta tumavtrycket för det klientcertifikat som du vill återkalla.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Lägg till tumavtrycket i listan över återkallade tumavtryck.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Kontrollera att tumavtrycket har lagts till i listan över återkallade certifikat. Du måste lägga till ett tumavtryck i taget.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Återställa ett klientcertifikat

Du kan återställa ett klientcertifikat genom att ta bort tumavtrycket från listan över återkallade klientcertifikat.

1.  Ta bort tumavtrycket från listan över tumavtryck för återkallade klientcertifikat.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Kontrollera att tumavtrycket har tagits bort från listan över återkallade certifikat.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Nästa steg

Du kan lägga till en virtuell dator i ditt virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) för anvisningar.





<!--HONumber=sep16_HO1-->


