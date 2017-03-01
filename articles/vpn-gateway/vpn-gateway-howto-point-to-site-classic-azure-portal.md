---
title: "Ansluta en dator till ett virtuellt Azure-nätverk med hjälp av punkt-till-plats: Azure Portal: klassisk | Microsoft Docs"
description: "Anslut säkert till ett klassiskt virtuellt Azure-nätverk genom att skapa en VPN-gateway-anslutning från punkt-till-plats med Azure-portalen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cf72197aba2c6e6c7a51f96d1161cf1fbe88a0c5
ms.openlocfilehash: eb56c702224ab4503051cbee0c3678a7e8833d2b


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

Den här artikeln beskriver steg för steg hur du skapar ett VNet med en punkt-till-plats-anslutning i den klassiska distributionsmodellen med Azure Portal.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Följande avsnitt visar stegen för att skapa en säker punkt-till-plats-anslutning till ett virtuellt nätverk.

1. Skapa ett virtuellt nätverk och en VPN-gateway
2. Generera certifikat
3. Överför .cer-filen
4. Skapa VPN-klientens konfigurationspaket
5. Konfigurera klientdatorn
6. Anslut till Azure

### <a name="example-settings"></a>Exempelinställningar
Du kan använda följande exempelinställningar:

* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16**
* **Undernätsnamn: FrontEnd**
* **Adressintervall för undernätet: 192.168.1.0/24**
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp: TestRG**
* **Plats: Östra USA**
* **Anslutningstyp: Punkt-till-plats**
* **Adressutryme för klienten: 172.16.201.0/24**. VPN-klienter som ansluter till VNet med den här punkt-till-plats-anslutningen får en IP-adress från den angivna poolen.
* **GatewaySubnet: 192.168.200.0/24**. Gateway-undernätet måste ha namnet ”GatewaySubnet”.
* **Storlek:** Markera den gateway SKU som du vill använda.
* **Routningstyp: dynamisk**

## <a name="a-namevnetvpnasection-1---create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Del 1: Skapa ett virtuellt nätverk
Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna. Följ stegen nedan för att skapa ett VNet med Azure Portal:

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Ny**. Skriv ”Virtuella nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.

    ![Sök efter virtuella nätverksblad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Nästan längst ned på bladet virtuellt nätverk, från listan **Välj en distributionsmodell**, väljer du **Klassisk** och klickar sedan på **Skapa**.

    ![Välj distributionsmodell](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. På det här bladet kommer du att lägga till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

    ![Skapa ett virtuellt nätverksblad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till det här VNet:et kommer att finnas.
8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.

    ![Fäst vid instrumentpanelen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. När du klickar på Skapa, kommer du att se en ikon på din instrumentpanel som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

    ![Skapa en virtuell nätverksikon](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. När du har skapat din virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda. Den här inställningen skapar ingen ny DNS-server. Se till att lägga till en DNS-server som dina resurser kan kommunicera med.

När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen.

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>Del 2: Skapa gateway-undernät och en dynamisk routningsgateway
I det här steget kommer du att skapa ett gateway-undernät och en dynamisk routningsgateway. I Azure-portalen för den klassiska distributionsmodellen, kan du skapa gateway-undernätet och gatewayen genom samma konfigurationsblad.

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.
2. På bladet för ditt virtuella nätverk, på **Översikt**-bladet, i avsnittet VPN-anslutningar, klickar du på **Gateway**.

    ![Klicka för att skapa en gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. På **Ny VPN-anslutning**-bladet, väljer du **punkt-till-plats**.

    ![Punkt-till-plats-anslutningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Lägg till IP-adressutrymme för **klientens adressutrymme**. Detta är intervallet från vilket VPN-klienterna får en IP-adress när de ansluter. Ta bort det automatiskt ifyllda intervallet och lägg till ditt eget.

    ![Adressutryme för klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Välj kryssutan **Skapa gateway omedelbart**.

    ![Skapa gatewayen omedelbart](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Klicka på **Valfri gatewaykonfiguration**, för att öppna **Gatewaykonfiguration**-bladet.

    ![Klicka på valfri gatewaykonfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Klicka på **Konfigurera obligatoriska inställningar för undernät**, för att lägga till **Gateway-undernätet**. Även om det är möjligt att skapa ett gateway-subnät som är så litet som /29 så rekommenderar vi att du skapar ett större subnät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden.

   > [!IMPORTANT]
   > När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp (NSG) till det här undernätet så kan din VPN-gateway sluta fungera som förväntat. Mer information om nätverkssäkerhetsgrupper finns i [Vad är en nätverkssäkerhetsgrupp?](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![Lägg till GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. I portalen är standard-SKU:n **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![gateway-storlek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Välj **Routningstyp** för din gateway. P2S konfigurationer kräver en **Dynamisk** routningstyp. Klicka på **Ok** när du har konfigurerat det här bladet.

    ![Konfigurera routningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. På bladet **Ny VPN-anslutning**, klickar du på **Ok** längst ned på bladet för att börja skapa din virtuella nätverksgateway. Det här steget kan ta upp till 45 minuter.

## <a name="a-namegeneratecertsasection-2---generate-certificates"></a><a name="generatecerts"></a>Avsnitt 2 – Generera certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du kan exportera offentliga certifikatdata (inte den privata nyckeln) som en Base-64-kodad X.509.cer-fil från antingen ett rotcertifikat som genererats av en företagscertifikatlösning, eller genom att använda ett självsignerat rotcertifikat. Du kan sedan importera offentliga certifikatdata från rotcertifikatet till Azure. Dessutom måste du generera ett klientcertifikat från rotcertifikatet för klienter. Varje klient som vill ansluta till det virtuella nätverket med en P2S-anslutning måste ha ett klientcertifikat installerat som har genererats från rotcertifikatet.

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Del 1: Hämta cer.-filen för rotcertifikatet
Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Om du inte använder en CA-företagslösning kan du skapa ett självsignerat rotcertifikat. Den rekommenderade metoden för att skapa ett självsignerat certifikat för P2S-anslutningar är [makecert](vpn-gateway-certificates-point-to-site.md). Det går att använda PowerShell för att skapa självsignerade certifikat, men certifikatet som genereras med PowerShell innehåller inte fälten som krävs för P2S-anslutningar.

* Om du använder en företagscertifikatlösning hämtar du CER-filen för det rotcertifikat som du vill använda.
* Om du inte använder en företagscertifikatlösning måste du [generera ett självsignerat rotcertifikat med hjälp av makecert](vpn-gateway-certificates-point-to-site.md).


1. Om du vill hämta en .cer-fil från ett certifikat öppnar du **certmgr.msc** och letar upp rotcertifikatet. Högerklicka på det självsignerade rotcertifikatet, klicka på **alla uppgifter** och klicka sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Nej, exportera inte den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base&64;-kodad X.509 (. CER).** Klicka sedan på **Nästa**.
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet.

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>Del 2: Generera ett klientcertifikat
Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla istället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

####<a name="enterprise-certificate"></a>Företagscertifikat
- Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet 'name@yourdomain.com', istället för formatet ”domännamn\användarnamn”.
- Se till att det klientcertifikat som du utfärdar baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

####<a name="self-signed-certificate"></a>Självsignerat certifikat 
Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>Del 3: Exportera klientcertifikatet
Installera ett klientcertifikat på varje dator som du vill ansluta till det virtuella nätverket. Ett klientcertifikat krävs för autentisering. Du kan automatisera installationen av klientcertifikatet eller installera det manuellt. Följande steg beskriver hur du exporterar och installerar klientcertifikatet manuellt.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.

## <a name="a-nameuploadasection-3---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet
När gatewayen har skapats, kan du överföra .cer-filen för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

    ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

    ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På **Certifikat**-bladet, klickar du på **Överför** för att öppna **Överför certifikat**-bladet.<br>

    ![Överför certifikat-bladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klicka på mappbilden och bläddra efter .cer-filen. Välj filen och klicka sedan på **Ok**. Uppdatera sidan för att se överförda certifikat på **certifikat**-bladet.

    ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="a-namevpnclientconfigasection-4---generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>Avsnitt 4 – Generera VPN-klientens konfigurationspaket
För att ansluta till det virtuella nätverket måste du också konfigurera en VPN-klient. Klientdatorn kräver både ett klientcertifikat och rätt konfigurationspaket för VPN-klienten för att kunna ansluta.

VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara. Inställningarna är specifika för det virtuella nätverk som du vill ansluta till. En lista över klientoperativsystem som stöds finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Generera konfigurationspaketet för VPN-klienten
1. I Azure Porta, på bladet **Översikt** för ditt VNet, i **VPN-anslutningar**, klickar du på klientbilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.
2. Högst upp på bladet **Punkt-till-plats VPN-anslutning**, klickar du på hämtningspaketet som motsvarar operativsystemet för den klient där det ska installeras:

   * För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
   * För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

     ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Du kommer att se ett meddelande om att Azure genererar konfigurationspaketet för VPN-klienten för det virtuella nätverket. Efter några minuter så genereras paketet och du får ett meddelande på din lokala dator att paketet har hämtats. Spara konfigurationspaketfilen. Du kommer att installera det här på varje klientdator som ska ansluta till den virtuella nätverket med P2S.

## <a name="a-nameclientconfigurationasection-5---configure-the-client-computer"></a><a name="clientconfiguration"></a>Avsnitt 5 – Konfigurera klientdatorn
### <a name="part-1-install-the-client-certificate"></a>Del 1: Installera klientcertifikatet
Varje klientdator måste ha ett klientcertifikat för att kunna autentisera. När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Kopiera .pfx-filen till klientdatorn.
2. Dubbelklicka på .pfx-filen för att installera den. Ändra inte installationsplatsen.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Del 2: Installera konfigurationpaketet för VPN-klienten
Du kan använda samma konfigurationspaketet för VPN-klienten på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten.

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket och dubbelklicka på EXE-filen.
2. När paketet har installerats kan du starta VPN-anslutningen. Konfigurationspaketet är inte signerat av Microsoft. Om du vill kan du signera paketet med hjälp av organisationens signeringstjänst, eller göra det själv med hjälp av [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Du kan även använda paketet utan signering. Dock visas en varning när du installerar paketet om det inte är signerat.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Den visar namnet på det virtuella nätverk som anslutningen kommer att upprättas till och kommer att se ut ungefär såhär:

    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="a-nameconnectasection-6---connect-to-azure"></a><a name="connect"></a>Avsnitt 6 – Anslut till Azure
### <a name="connect-to-your-vnet"></a>Anslut till ditt VNet
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier.
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

    ![VPN-klientanslutning](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Nu bör anslutningen upprättas.

    ![Anslutningen upprättad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan. 
>
>

### <a name="verify-the-vpn-connection"></a>Verifiera VPN-anslutningen
1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att den IP-adress som du har fått är en av adresserna inom adressintervallet för punkt-till-plats-anslutningen som du angav när du skapade ditt VNet. Resultaten bör likna följande:

Exempel:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="a-namefaqapoint-to-site-faq"></a><a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).



<!--HONumber=Feb17_HO3-->


