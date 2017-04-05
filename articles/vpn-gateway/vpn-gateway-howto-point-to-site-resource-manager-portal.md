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
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6703df0f64534ed638e570342eef7fbda2a74d2e
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn.

Den här artikeln beskriver steg för steg hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning via Azure Portal. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="example"></a>Exempelvärden
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

## <a name="createvnet"></a>Del 1 – Skapa ett virtuellt nätverk
Kontrollera att du har en Azure-prenumeration innan du börjar. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial). Om du skapar den här konfigurationen för att öva dig kan du använda [exempelvärdena](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>Del 2 – Ange adressutrymmen och undernät
Du kan lägga till ytterligare adressutrymme och undernät till ditt VNet när det har skapats.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>Del 3 – Lägga till ett gatewayundernät

Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Gatewaytjänsterna använder de IP-adresser som finns angivna i gatewayundernätet. Om möjligt är det bäst att skapa ett gateway-undernät med CIDR-block /28 eller /27 för att tillhandahålla tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationskrav.

Skärmbilderna i det här avsnittet är avsedda som referensexempel. Se till att använda GatewaySubnet-adressintervallet som motsvarar de värden som krävs för konfigurationen.

###<a name="to-create-a-gateway-subnet"></a>Så här skapar du ett gateway-undernät

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>Del 4 – Ange en DNS-server (valfritt)

DNS krävs inte för en punkt-till-plats-anslutning. Om du vill använda namnmatchning för resurser som distribueras till ditt virtuella nätverk bör du dock ange en DNS-server. Med den här inställningen kan du ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket. Den skapar inte någon DNS-server.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Del 5 – Skapa en virtuell nätverksgateway
Punkt-till-plats-anslutningar kräver följande inställningar:

* Gateway-typ: VPN
* VPN-typ: Routningsbaserad

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Del 6 – Generera certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil. Du kan sedan överföra offentliga certifikatdata från rotcertifikatet till Azure.

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt klientcertifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

### <a name="getcer"></a>Steg 1 – Hämta CER-filen för rotcertifikatet

####<a name="enterprise-certificate"></a>Företagscertifikat
 
Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Hämta .cer-filen för det rotcertifikat som du vill använda.

####<a name="self-signed-root-certificate"></a>Självsignerat rotcertifikat

Om du inte använder en företagscertifikatlösning måste du generera ett självsignerat rotcertifikat. Du kan använda PowerShell om du vill skapa ett självsignerat rotcertifikat som innehåller de fält som behövs för P2S-autentisering. Avsnittet [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat rotcertifikat för punkt-till-plats-anslutningar med PowerShell) vägleder dig genom stegen för att skapa ett självsignerat rotcertifikat.

> [!NOTE]
> Tidigare var makecert den metod som rekommenderades för att skapa självsignerade rotcertifikat och generera klientcertifikat för punkt-till-plats-anslutningar. Du kan nu använda PowerShell för att skapa dessa certifikat. En fördel med att använda PowerShell är möjligheten att skapa SHA-2-certifikat. I [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat rotcertifikat för punkt-till-plats-anslutningar med PowerShell) finns information om nödvändiga värden.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Så här exporterar du den offentliga nyckeln för ett självsignerat rotcertifikat

Punkt-till-plats-anslutningar kräver att den offentliga nyckeln (.cer) överförs till Azure. Följande steg hjälper dig att exportera .cer-filen för det självsignerade rotcertifikatet.

1. Om du vill hämta en .cer-fil från certifikatet kan du öppna **certmgr.msc**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas.
2. Klicka på **Nästa** i guiden. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet. Meddelandet **Exporten lyckades**. Stäng guiden genom att klicka på **OK**.

### <a name="generateclientcert"></a>Steg 2 – Generera ett klientcertifikat
Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta till det virtuella nätverket eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla i stället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

####<a name="enterprise-certificate"></a>Företagscertifikat
- Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
- Se till att det klientcertifikat som du utfärdar baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

####<a name="self-signed-root-certificate"></a>Självsignerat rotcertifikat 
Om du använder ett självsignerat rotcertifikat kan du läsa [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Generera ett klientcertifikat med PowerShell) för att få anvisningar om att generera ett klientcertifikat som är kompatibelt med punkt-till-plats-anslutningar.


### <a name="exportclientcert"></a>Steg 3 – Exportera klientcertifikatet
Om du skapar ett klientcertifikat från ett självsignerat rotcertifikat med hjälp av [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert)-instruktionerna installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det.

1. Du kan exportera ett klientcertifikat genom att öppna **certmgr.msc**. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Ja, exportera den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Klicka sedan på **Nästa**. 
4. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.
5. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
6. Klicka på **Slutför** för att exportera certifikatet.   

## <a name="addresspool"></a>Del 7 – Lägga till klientadresspoolen
1. När den virtuella nätverksgatewayen har skapats navigerar du till avsnittet **Inställningar** på bladet för den virtuella nätverksgatewayen. I avsnittet **Inställningar** klickar du på **Punkt-till-plats-konfiguration** för att öppna bladet **Konfiguration**.
   
    ![Punkt-till-plats-blad](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **Adresspool** är poolen med IP-adresser som klienter som ansluter sig får en IP-adress från. Lägg till adresspoolen och klicka sedan på **Spara**.
   
    ![Klientadresspool](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>Del 8 – Ladda upp CER-filen med rotcertifikatet
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

## <a name="clientconfig"></a>Del 9 – Ladda ned och installera VPN-klientkonfigurationspaketet
Klienter som ansluter till Azure via P2S måste ha både ett klientcertifikat och ett VPN-klientkonfigurationspaket installerat. VPN-klientkonfigurationspaket är tillgängliga för Windows-klienter. 

VPN-klientpaketet innehåller information om konfigurationen av VPN-klientprogrammet som är inbyggt i Windows. Konfigurationen är specifik för det VPN-nätverk som du vill ansluta till. Paketet installerar inte någon ytterligare programvara.

Du kan använda samma konfigurationspaketet för VPN-klienten på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten.

### <a name="step-1---download-the-client-configuration-package"></a>Del 1 – Hämta klientkonfigurationspaketet

1. På bladet **Punkt-till-plats-konfiguration** klickar du på **Hämta VPN-klient** för att öppna bladet **Hämta VPN-klient**. Det tar en minut eller två att skapa paketet.
   
    ![Hämtning av VPN-klient 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Välj rätt paket för din klient och klicka sedan på **Hämta**. Spara konfigurationspaketfilen. Du kommer att installera det här på varje klientdator som ska ansluta till den virtuella nätverket.

    ![Hämtning av VPN-klient 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>Del 2 – Installera klientkonfigurationspaketet

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket. 
2. Installera paketet på klientdatorn genom att dubbelklicka på EXE-filen. Konfigurationspaketet är inte signerat eftersom du har skapat det. Det innebär att varningsmeddelande kan visas. Om ett Windows SmartScreen-fönster öppnas klickar du på **Mer info** (till vänster) och sedan på **Kör ändå** för att kunna installera paketet.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Den visar namnet på det virtuella nätverk som anslutningen kommer att upprättas till och kommer att se ut ungefär såhär:
3. Installera paketet på klientdatorn. Om ett Windows SmartScreen-fönster öppnas klickar du på **Mer info** (till vänster) och sedan på **Kör ändå** för att kunna installera paketet.
4. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär som detta exempel: 
   
    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="installclientcert"></a>Del 10 – Installera ett exporterat klientcertifikatet

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. 

1. Leta upp och kopiera *.pfx*-filen till klientdatorn. Installera *.pfx*-filen genom att dubbelklicka på den på klientdatorn. Låt **Lagringsplats** stå som **Aktuell användare** och klicka sedan på **Nästa**.
2. Gör inga ändringar på sidan för den **Fil** som ska importeras. Klicka på **Nästa**.
3. På sidan **Skydd av privat nyckel** anger du lösenordet för certifikatet, om du har använt ett sådant, eller bekräftar att säkerhetsobjektet som installerar certifikatet är korrekt, och klickar sedan på **Nästa**.
4. På sidan **Certifikatarkiv** låter du standardplatsen stå kvar och klickar sedan på **Nästa**.
5. Klicka på **Slutför**. På sidan **Säkerhetsvarning** för certifikatinstallationen klickar du på **Ja**. Eftersom du har genererat certifikatet kan du klicka på ”Ja” utan att oroa dig. Certifikatet har nu importerats.

## <a name="connect"></a>Del 11 – Ansluta till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
   
    ![VPN-klientanslutning till Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. Nu bör anslutningen upprättas.
   
    ![VPN-klienten har anslutit till Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan. 
>
>

## <a name="verify"></a>Del 12 – Verifiera anslutningen
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

## <a name="add"></a>Lägg till eller ta bort betrodda rotcertifikat
Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett betrott certifikat kan klientcertifikaten som genererades från rotcertifikatet inte längre ansluta till Azure via punkt-till-plats. Om du vill att klienter ska kunna ansluta måste du installera ett nytt klientcertifikat som genereras från ett certifikat som är betrott i Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Mer information finns i avsnittet [Ladda upp ett betrott rotcertifikat](#uploadfile) i den här artikeln.

### <a name="to-remove-a-trusted-root-certificate"></a>Så här tar du bort ett betrott rotcertifikat

1. Om du vill ta bort ett betrott rotcertifikat navigerar du till bladet **Punkt-till-plats-konfiguration** för din virtuella nätverksgateway.
2. I avsnittet **Rotcertifikat** på bladet letar du upp det certifikat som du vill ta bort.
3. Klicka på ellipsen bredvid certifikatet och klicka sedan på Ta bort.

## <a name="revokeclient"></a>Återkalla ett klientcertifikat
Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Detta skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="to-revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
3. Navigera till den virtuella nätverksgatewayens blad **Punkt-till-plats-konfiguration**. Det här är bladet som du använde för att [ladda upp ett betrott rotcertifikat](#uploadfile).
4. I avsnittet **Återkallade certifikat** anger du ett eget namn på certifikatet (det inte behöver vara certifikatets CN-namn).
5. Kopiera och klistra in tumavtryckssträngen i fältet **Tumavtryck** fält.
6. Tumavtrycket valideras och läggs till automatiskt i listan över återkallade certifikat. Ett meddelande om att listan uppdateras visas på skärmen. 
7. När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet är inte längre giltigt.

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).



