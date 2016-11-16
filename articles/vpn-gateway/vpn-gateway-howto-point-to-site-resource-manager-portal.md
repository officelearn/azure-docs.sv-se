---
title: "Konfigurera en punkt-till-plats-VPN-gateway-anslutning till ett virtuellt nätverk med hjälp av Resource Manager-distributionsmodellen och Azure Portal| Microsoft Docs"
description: "Anslut säkert till Azure Virtual Network genom att skapa en punkt-till-plats-VPN-gateway-anslutning med Resource Manager och Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aeebed0c733b9fbac964cdc532ff9d364683609b


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Planering och design](vpn-gateway-plan-design.md).

Den här artikeln beskriver steg för steg hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning i Resource Manager-distributionsmodellen med Azure Portal.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Exempelvärden
* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16**<br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk.
* **Undernätsnamn: FrontEnd**
* **Adressintervall för undernätet: 192.168.1.0/24**
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp: TestRG**
* **Plats: Östra USA**
* **GatewaySubnet: 192.168.200.0/24**
* **Gateway-namn på virtuellt nätverk: VNet1GW**
* **Gateway-typ: VPN**
* **VPN-typ: Routningsbaserad**
* **Offentlig IP-adress: VNet1GWpip**
* **Anslutningstyp: Punkt-till-plats**
* **Klientadresspool: 172.16.201.0/24**<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från klientadresspoolen.

## <a name="before-beginning"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Del 1 – Skapa ett virtuellt nätverk
Om du skapar den här konfigurationen för att öva dig kan du använda [exempelvärdena](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

### <a name="2-add-additional-address-space-and-subnets"></a>2. Lägg till ytterligare adressutrymmen och undernät
Du kan lägga till ytterligare adressutrymme och undernät till ditt VNet när det har skapats.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

### <a name="3-create-a-gateway-subnet"></a>3. Skapa ett gateway-undernät
Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Om möjligt är det bäst att skapa ett gateway-undernät med CIDR-block av /28 eller /27 för att tillhandahålla tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationskrav.

Skärmbilderna i det här avsnittet är avsedda som referensexempel. Se till att använda GatewaySubnet-adressintervallet som motsvarar de värden som krävs för konfigurationen.

#### <a name="to-create-a-gateway-subnet"></a>Så här skapar du ett gateway-undernät
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="a-namednsa4-specify-a-dns-server-optional"></a><a name="dns"></a>4. Ange en DNS-server (valfritt)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-2-create-a-virtual-network-gateway"></a><a name="creategw"></a>Del 2 – Skapa en virtuell nätverksgateway
Punkt-till-plats-anslutningar kräver följande inställningar:

* Gateway-typ: VPN
* VPN-typ: Routningsbaserad

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-3-generate-certificates"></a><a name="generatecert"></a>Del 3 – Generera certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du kan exportera offentliga certifikatdata (inte den privata nyckeln) som en Base-64-kodad X.509.cer-fil från antingen ett rotcertifikat som genererats av en företagscertifikatlösning, eller genom att använda ett självsignerat rotcertifikat. Du kan sedan importera offentliga certifikatdata från rotcertifikatet till Azure. Dessutom måste du generera ett klientcertifikat från rotcertifikatet för klienter. Varje klient som vill ansluta till det virtuella nätverket med en P2S-anslutning måste ha ett klientcertifikat, som har genererats från rotcertifikatet, installerat.

### <a name="a-namegetcera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Hämta cer.-filen för rotcertifikatet
Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Om du inte använder en CA-företagslösning kan du skapa ett självsignerat rotcertifikat. En metod för att skapa ett självsignerat certifikat är makecert.

* Om du använder ett företagscertifikatsystem hämtar du CER-filen för det rotcertifikat som du vill använda. 
* Om du inte använder en företagscertifikatlösning, måste du generera ett självsignerat rotcertifikat. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

1. Om du vill hämta en .cer-fil från ett certifikat öppnar du **certmgr.msc** och letar upp rotcertifikatet. Högerklicka på det självsignerade rotcertifikatet, klicka på **alla uppgifter** och klicka sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Nej, exportera inte den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** Klicka sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet.

### <a name="a-namegenerateclientcerta2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Generera ett klientcertifikat
Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla istället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

* Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet 'name@yourdomain.com', istället för formatet ”domännamn\användarnamn”. 
* Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Exportera klientcertifikat
Ett klientcertifikat krävs för autentisering. När du har genererat klientcertifikatet exporterar du det. Klientcertifikatet du exporterar installeras senare på varje klientdator.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.

## <a name="a-nameaddresspoolapart-4-add-the-client-address-pool"></a><a name="addresspool"></a>Del 4 – Lägg till klientadresspoolen
1. När den virtuella nätverksgatewayen har skapats navigerar du till avsnittet **Inställningar** på bladet för den virtuella nätverksgatewayen. I avsnittet **Inställningar** klickar du på **Punkt-till-plats-konfiguration** för att öppna bladet **Konfiguration**.
   
    ![bladet punkt-till-plats](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "point to site blade")
2. **Adresspool** är poolen med IP-adresser som klienter som ansluter sig får en IP-adress från. Lägg till adresspoolen och klicka sedan på **Spara**.
   
    ![klientadresspool](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "client address pool")

## <a name="a-nameuploadfileapart-5-upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>Del 5 – Ladda upp .cer-filen med rotcertifikatet
När gatewayen har skapats, kan du överföra .cer-filen för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. Navigera till bladet **Punkt-till-plats-konfiguration**. Du lägger till .cer-filerna i avsnittet **Rotcertifikat** på det här bladet.
   
    ![bladet punkt-till-plats](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "point to site blade")
2. Kontrollera att du har exporterat rotcertifikatet som en Base64-kodad X.509-fil (.cer). Du behöver exportera i det här formatet så att du kan öppna certifikatet med textredigerare.
3. Öppna certifikatet med en textredigerare, till exempel Anteckningar. Kopiera endast följande avsnitt:
   
    ![certifikatdata](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "certificate data")
4. Klistra in certifikatdata i avsnittet **Offentliga certifikatdata** i portalen. Placera namnet på certifikatet i utrymmet **Namn** och klicka sedan på **Spara**. Du kan lägga till upp till 20 betrodda rotcertifikat.
   
    ![certifikatuppladdning](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "certificate upload")

## <a name="a-nameclientconfigapart-6-download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Del 6 – Ladda ned och installera VPN-klientkonfigurationspaketet
Klienter som ansluter till Azure via P2S måste ha både ett klientcertifikat och ett VPN-klientkonfigurationspaket installerat. VPN-klientkonfigurationspaket är tillgängliga för Windows-klienter. 

VPN-klientpaketet innehåller information om konfigurationen av VPN-klientprogrammet som är inbyggt i Windows. Konfigurationen är specifik för det VPN-nätverk som du vill ansluta till. Paketet installerar inte någon ytterligare programvara. Mer information finns i avsnittet [Vanliga frågor och svar om VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. På bladet **Punkt-till-plats-konfiguration** klickar du på **Hämta VPN-klient** för att öppna bladet **Hämta VPN-klient**.
   
    ![Hämtning av VPN-klient](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "VPN client download")
2. Välj rätt paket för din klient och klicka sedan på **Hämta**. För 64-bitarsklienter väljer du **AMD64**. För 32-bitarsklienter väljer du **x86**.
3. Installera paketet på klientdatorn. Om ett SmartScreen-fönster öppnas klickar du på **Mer info** och sedan **Kör ändå ** för att kunna installera paketet.
4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär som detta exempel: 
   
    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "VPN client")

## <a name="a-nameinstallclientcertapart-7-install-the-client-certificate"></a><a name="installclientcert"></a>Del 7 – Installera klientcertifikatet
Varje klientdator måste ha ett klientcertifikat för att kunna autentisera. När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Kopiera .pfx-filen till klientdatorn.
2. Dubbelklicka på .pfx-filen för att installera den. Ändra inte installationsplatsen.

## <a name="a-nameconnectapart-8-connect-to-azure"></a><a name="connect"></a>Del 8 – Anslut till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
   
    ![VPN-klient 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. Nu bör anslutningen upprättas.
   
    ![VPN-klient 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## <a name="a-nameverifyapart-9-verify-your-connection"></a><a name="verify"></a>Del 9 – Verifiera anslutningen
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

## <a name="a-nameaddato-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Så här lägger du till eller tar bort betrodda rotcertifikat
Du kan ta bort betrodda rotcertifikat från Azure. När du tar bort ett betrott certifikat kan klientcertifikaten som genererades från rotcertifikatet inte längre ansluta till Azure via punkt-till-plats. Om du vill att klienter ska kunna ansluta måste du installera ett nytt klientcertifikat som genereras från ett certifikat som är betrott i Azure.

Du kan hantera listan över återkallade klientcertifikat på bladet **Punkt-till-plats-konfiguration**. Det här är bladet som du använde för att [ladda upp ett betrott rotcertifikat](#uploadfile).

## <a name="a-namerevokeclientato-manage-the-list-of-revoked-client-certificates"></a><a name="revokeclient"></a>Så här hanterar du listan med återkallade klientcertifikat
Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Om du tar bort CER-filen för ett rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats av det återkallade rotcertifikatet. Om du vill återkalla ett specifikt klientcertifikat, inte roten, kan du göra det. På så sätt är de andra certifikaten som har genererats från rotcertifikatet fortfarande giltiga. 

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

Du kan hantera listan över återkallade klientcertifikat på bladet **Punkt-till-plats-konfiguration**. Det här är bladet som du använde för att [ladda upp ett betrott rotcertifikat](#uploadfile).

## <a name="next-steps"></a>Nästa steg
Du kan lägga till en virtuell dator i ditt virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) för anvisningar.




<!--HONumber=Nov16_HO2-->


