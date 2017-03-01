---
title: "Ansluta en dator till ett virtuellt Azure-nätverk med hjälp av punkt-till-plats: Portal | Microsoft Docs"
description: "Anslut säkert till Azure Virtual Network genom att skapa en punkt-till-plats-VPN-gateway-anslutning med Resource Manager och Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cf72197aba2c6e6c7a51f96d1161cf1fbe88a0c5
ms.openlocfilehash: 61ef9523739ebd8bd105c6faccf42405c7c62567


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

Den här artikeln beskriver steg för steg hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning via Azure Portal. De här stegen gäller för distributionsmodellen i Resource Manager.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

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
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

## <a name="a-namecreatevnetapart-1---create-a-virtual-network"></a><a name="createvnet"></a>Del 1 – Skapa ett virtuellt nätverk
Om du skapar den här konfigurationen för att öva dig kan du använda [exempelvärdena](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-nameaddressapart-2---specify-address-space-and-subnets"></a><a name="address"></a>Del 2 – Ange adressutrymmen och undernät
Du kan lägga till ytterligare adressutrymme och undernät till ditt VNet när det har skapats.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubnetapart-3---add-a-gateway-subnet"></a><a name="gatewaysubnet"></a>Del 3 – Lägga till ett gatewayundernät

Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Gatewaytjänsterna använder de IP-adresser som finns angivna i gatewayundernätet. Om möjligt är det bäst att skapa ett gateway-undernät med CIDR-block /28 eller /27 för att tillhandahålla tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationskrav.

Skärmbilderna i det här avsnittet är avsedda som referensexempel. Se till att använda GatewaySubnet-adressintervallet som motsvarar de värden som krävs för konfigurationen.

###<a name="to-create-a-gateway-subnet"></a>Så här skapar du ett gateway-undernät

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsapart-4---specify-a-dns-server-optional"></a><a name="dns"></a>Del 4 – Ange en DNS-server (valfritt)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-5---create-a-virtual-network-gateway"></a><a name="creategw"></a>Del 5 – Skapa en virtuell nätverksgateway
Punkt-till-plats-anslutningar kräver följande inställningar:

* Gateway-typ: VPN
* VPN-typ: Routningsbaserad

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-6---generate-certificates"></a><a name="generatecert"></a>Del 6 – Generera certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du kan exportera offentliga certifikatdata (inte den privata nyckeln) som en Base-64-kodad X.509.cer-fil från antingen ett rotcertifikat som genererats av en företagscertifikatlösning, eller genom att använda ett självsignerat rotcertifikat. Du kan sedan importera offentliga certifikatdata från rotcertifikatet till Azure. Dessutom måste du generera ett klientcertifikat från rotcertifikatet för klienter. Varje klient som vill ansluta till det virtuella nätverket med en P2S-anslutning måste ha ett klientcertifikat, som har genererats från rotcertifikatet, installerat.

### <a name="a-namegetcerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>Steg 1 – Hämta CER-filen för rotcertifikatet

Du behöver hämta .cer-filen för rotcertifikatet. Du kan antingen använda ett rotcertifikat från en företagslösning eller också kan du [skapa ett självsignerat rotcertifikat med hjälp av makecert](vpn-gateway-certificates-point-to-site.md). Det går att använda PowerShell för att skapa självsignerade certifikat, men certifikatet som genereras med PowerShell innehåller inte fälten som krävs för P2S-anslutningar.

1. Om du vill hämta en .cer-fil från ett certifikat öppnar du **certmgr.msc** och letar upp rotcertifikatet. Högerklicka på det självsignerade rotcertifikatet, klicka på **alla uppgifter** och klicka sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Nej, exportera inte den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base&64;-kodad X.509 (. CER).** Klicka sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet.

### <a name="a-namegenerateclientcertastep-2---generate-a-client-certificate"></a><a name="generateclientcert"></a>Steg 2 – Generera ett klientcertifikat
Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta till det virtuella nätverket eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla i stället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

####<a name="enterprise-certificate"></a>Företagscertifikat
- Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet 'name@yourdomain.com', istället för formatet ”domännamn\användarnamn”.
- Se till att det klientcertifikat som du utfärdar baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

####<a name="self-signed-certificate"></a>Självsignerat certifikat 
Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>Steg 3 – Exportera klientcertifikatet
Ett klientcertifikat krävs för autentisering. När du har genererat klientcertifikatet exporterar du det. Klientcertifikatet du exporterar installeras senare på varje klientdator.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.

## <a name="a-nameaddresspoolapart-7---add-the-client-address-pool"></a><a name="addresspool"></a>Del 7 – Lägga till klientadresspoolen
1. När den virtuella nätverksgatewayen har skapats navigerar du till avsnittet **Inställningar** på bladet för den virtuella nätverksgatewayen. I avsnittet **Inställningar** klickar du på **Punkt-till-plats-konfiguration** för att öppna bladet **Konfiguration**.
   
    ![Punkt-till-plats-blad](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **Adresspool** är poolen med IP-adresser som klienter som ansluter sig får en IP-adress från. Lägg till adresspoolen och klicka sedan på **Spara**.
   
    ![Klientadresspool](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="a-nameuploadfileapart-8---upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>Del 8 – Ladda upp CER-filen med rotcertifikatet
När gatewayen har skapats, kan du överföra .cer-filen för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. Certifikat läggs till på bladet **Punkt-till-plats-konfiguration** i avsnittet **Rotcertifikat**.  
2. Kontrollera att du har exporterat rotcertifikatet som en Base64-kodad X.509-fil (.cer). Du behöver exportera i det här formatet så att du kan öppna certifikatet med textredigerare.
3. Öppna certifikatet med en textredigerare, till exempel Anteckningar. Kopiera enbart följande avsnitt som en kontinuerlig rad:
   
    ![Certifikatdata](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)

    > [!NOTE]
    > När du kopierar certifikatdata ska du se till att kopiera texten som en kontinuerlig rad utan vagnreturer och radmatningar. Du kan behöva ändra vyn i textredigeraren till ”Visa symbol/Visa alla tecken” så att vagnreturer och radmatningar visas.                                                                                                                                                                            
    >
    >

4. Klistra in certifikatdata i fältet **Offentliga certifikatdata**. Ge certifikatet ett **Namn** och klicka sedan på **Spara**. Du kan lägga till upp till 20 betrodda rotcertifikat.
   
    ![Certifikatuppladdning](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="a-nameclientconfigapart-9---download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Del 9 – Ladda ned och installera VPN-klientkonfigurationspaketet
Klienter som ansluter till Azure via P2S måste ha både ett klientcertifikat och ett VPN-klientkonfigurationspaket installerat. VPN-klientkonfigurationspaket är tillgängliga för Windows-klienter. 

VPN-klientpaketet innehåller information om konfigurationen av VPN-klientprogrammet som är inbyggt i Windows. Konfigurationen är specifik för det VPN-nätverk som du vill ansluta till. Paketet installerar inte någon ytterligare programvara.

1. På bladet **Punkt-till-plats-konfiguration** klickar du på **Hämta VPN-klient** för att öppna bladet **Hämta VPN-klient**.
   
    ![Hämtning av VPN-klient 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Välj rätt paket för din klient och klicka sedan på **Hämta**. För 64-bitarsklienter väljer du **AMD64**. För 32-bitarsklienter väljer du **x86**.

    ![Hämtning av VPN-klient 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/client.png)
3. Installera paketet på klientdatorn. Om ett SmartScreen-fönster öppnas klickar du på **Mer info** och sedan på **Kör ändå ** för att kunna installera paketet.
4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär som detta exempel: 
   
    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="a-nameinstallclientcertapart-10---install-the-client-certificate"></a><a name="installclientcert"></a>Del 10 – Installera klientcertifikatet
Varje klientdator måste ha ett klientcertifikat för autentisering. När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Kopiera .pfx-filen till klientdatorn.
2. Dubbelklicka på .pfx-filen för att installera den. Ändra inte installationsplatsen.

## <a name="a-nameconnectapart-11---connect-to-azure"></a><a name="connect"></a>Del 11 – Ansluta till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
   
    ![VPN-klientanslutning till Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. Nu bör anslutningen upprättas.
   
    ![VPN-klienten har anslutit till Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan. 
>
>

## <a name="a-nameverifyapart-12---verify-your-connection"></a><a name="verify"></a>Del 12 – Verifiera anslutningen
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

Du kan hantera listan över återkallade klientcertifikat på bladet **Punkt-till-plats-konfiguration**. Det här är bladet som du använde för att [ladda upp ett betrott rotcertifikat](#uploadfile). Lägg till certifikatets namn och tumavtrycket och spara sedan.

## <a name="a-namefaqapoint-to-site-faq"></a><a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Feb17_HO3-->


