<properties
   pageTitle="Konfigurera en punkt-till-plats VPN-gateway-anslutning till ett virtuellt Azure nätverk med Azure Portal | Microsoft Azure"
   description="Anslut säkert till Azure Virtual Network genom att skapa en VPN-gateway-anslutning från punkt-till-plats med Azure Portal."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="cherylmc"/>


# Konfigurera en punkt-till-plats-anslutning till ett VNet med Azure Portal

> [AZURE.SELECTOR]
- [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Klassisk – den klassiska portalen](vpn-gateway-point-to-site-create.md)

Den här artikeln beskriver steg för steg hur du skapar ett VNet med en punkt-till-plats-anslutning i den klassiska distributionsmodellen med Azure Portal. Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill fjärransluta till ditt VNet, exempelvis från hemmet eller en konferens. Eller om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Om VPN-gateway](vpn-gateway-about-vpngateways.md#point-to-site).


### Distributionsmodeller och metoder för P2S-anslutningar

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Följande tabell visar de två distributionsmodeller och tillgängliga distributionsverktyg för varje distributionsmodell. När det finns en artikel tillgänglig så länkas den till.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## Grundläggande arbetsflöde 

![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")


Följande avsnitt visar stegen för att skapa en säker punkt-till-plats-anslutning till ett virtuellt nätverk. 

1. Skapa ett virtuellt nätverk och en VPN-gateway
2. Generera certifikat
3. Överför .cer-filen
4. Skapa VPN-klientens konfigurationspaket
5. Konfigurera klientdatorn
6. Anslut till Azure

### Exempelinställningar

Du kan använda följande exempelinställningar:

- **Namn: VNet1**
- **Adressutrymme: 192.168.0.0/16**
- **Undernätsnamn: FrontEnd**
- **Adressintervall för undernätet: 192.168.1.0/24**
- **Prenumeration:** Verifiera att du har rätt prenumeration ifall du har fler än en.
- **Resursgrupp: TestRG**
- **Plats: Östra USA**
- **Anslutningstyp: Punkt-till-plats**
- **Adressutryme för klienten: 172.16.201.0/24**. VPN-klienter som ansluter till VNet med den här punkt-till-plats-anslutningen får en IP-adress från den angivna poolen.
- **GatewaySubnet: 192.168.200.0/24**. Gateway-undernätet måste ha namnet "GatewaySubnet".
- **Storlek:** Markera den gateway SKU som du vill använda.
- **Routningstyp: dynamisk**

## <a name="vnetvpn"></a>Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway

### Steg 1: Skapa ett virtuellt nätverk

Följ stegen nedan för att skapa ett VNet med Azure Portal. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna.

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.

2. Klicka på **Ny**. I fältet **Sök marknadsplats** skriver du "Virtuella nätverk". Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.

    ![Sök efter virtuella nätverksblad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Search for virtual network blade")

3. Nästan längst ned på bladet virtuellt nätverk, från listan **Välj en distributionsmodell**, väljer du **Klassisk** och klickar sedan på **Skapa**.

    ![Välj distributionsmodell](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Select deployment model")

4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. På det här bladet kommer du att lägga till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

    ![Skapa ett virtuellt nätverksblad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Create virtual network blade")

5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.

6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](resource-group-overview.md#resource-groups).

7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till det här VNet:et kommer att finnas.

8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.
    
    ![Fäst vid instrumentpanelen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Pin to dashboard")


9. När du klickar på Skapa, kommer du att se en ikon på din instrumentpanel som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

    ![Skapa en virtuell nätverksikon](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creating virtual network tile")

10. När du har skapat din virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda. Den här inställningen skapar ingen ny DNS-server. Se till att lägga till en DNS-server som dina resurser kan kommunicera med.

När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen.

### Del 2: Skapa gateway-undernät och en dynamisk routningsgateway

I det här steget kommer du att skapa ett gateway-undernät och en dynamisk routningsgateway. I Azure-portalen för den klassiska distributionsmodellen, kan du skapa gateway-undernätet och gatewayen genom samma konfigurationsblad.

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.

2. På bladet för ditt virtuella nätverk, på **Översikt**-bladet, i avsnittet VPN-anslutningar, klickar du på **Gateway**.

    ![Klicka här för att skapa en gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Click here to create a gateway")


3. På **Ny VPN-anslutning**-bladet, väljer du **punkt-till-plats**.

    ![P2S-anslutningstyper](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S connection type")

4. Lägg till IP-adressutrymme för **klientens adressutrymme**. Detta är intervallet från vilket VPN-klienterna får en IP-adress när de ansluter. Ta bort det automatiskt ifyllda intervallet och lägg till ditt eget.

    ![Adressutryme för klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Client address space")

5. Välj kryssutan **Skapa gateway omedelbart**.

    ![Skapa gatewayen omedelbart](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Create gateway immediately")

6. Klicka på **Valfri gatewaykonfiguration**, för att öppna **Gatewaykonfiguration**-bladet.

    ![Klicka på valfri gatewaykonfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Click optional gateway configuration")

7. Klicka på **Konfigurera obligatoriska inställningar för undernät**, för att lägga till **Gateway-undernätet**. Även om det är möjligt att skapa ett gateway-subnät som är så litet som /29 så rekommenderar vi att du skapar ett större subnät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden.

    >[AZURE.IMPORTANT] När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp (NSG) till det här undernätet så kan din VPN-gateway sluta fungera som förväntat. Mer information om nätverkssäkerhetsgrupper finns i [Vad är en nätverkssäkerhetsgrupp?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Lägg till GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Add GatewaySubnet")

8. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. I portalen är standard-SKU:n **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![gateway-storlek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Välj **Routningstyp** för din gateway. P2S konfigurationer kräver en **Dynamisk** routningstyp. Klicka på **Ok** när du har konfigurerat det här bladet.

    ![Konfigurera routningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configure routing type")

10. På bladet **Ny VPN-anslutning**, klickar du på **Ok** längst ned på bladet för att börja skapa din virtuella nätverksgateway. Det här steget kan ta upp till 45 minuter. 


## <a name="generatecerts"></a>Avsnitt 2 – Generera certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du kan använda .cer-filen från antingen ett rotcertifikat som genererats av en företagscertifikatlösning, eller använda dog av ett självsignerat rotcertifikat. I det här avsnittet får du .cer-filen för rotcertifikatet och ett klientcertifikat som genererats från rotcertifikatet.

### <a name="root"></a>Del 1: Hämta CER-filen för rotcertifikatet

- Om du använder ett företagscertifikatsystem hämtar du CER-filen för det rotcertifikat som du vill använda. 

- Om du inte använder en företagscertifikatlösning, måste du generera ett självsignerat rotcertifikat. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md). Artikeln beskriver steg för steg hur du använder makecert för att generera ett självsignerat certifikat och exportera CER-filen.

### Del 2: Generera ett klientcertifikat

Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla istället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

- Om du använder en företagscertifikatlösning genererar du ett klientcertifikat med det vanliga namnvärdeformatet namn@dindomän.com istället för med formatet domännamn\användarnamn. 

- Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

### Del 3: Exportera klientcertifikatet

Installera ett klientcertifikat på varje dator som du vill ansluta till det virtuella nätverket. Ett klientcertifikat krävs för autentisering. Du kan automatisera installationen av klientcertifikatet eller installera det manuellt. Följande steg beskriver hur du exporterar och installerar klientcertifikatet manuellt.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.

## <a name="upload"></a>Avsnitt 3 – Överför .cer-filen

När gatewayen har skapats, kan du överföra .cer-filen för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för certifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

    ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")

2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

    ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. På **Certifikat**-bladet, klickar du på **Överför** för att öppna **Överför certifikat**-bladet.<br>

    ![Överför certifikat-bladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Klicka på mappbilden och bläddra efter .cer-filen. Välj filen och klicka sedan på **Ok**. Uppdatera sidan för att se överförda certifikat på **certifikat**-bladet.

    ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Avsnitt 4 – Generera VPN-klientens konfigurationspaket

För att ansluta till det virtuella nätverket måste du också konfigurera en VPN-klient. Klientdatorn kräver både ett klientcertifikat och rätt konfigurationspaket för VPN-klienten för att kunna ansluta.

VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara. Inställningarna är specifika för det virtuella nätverk som du vill ansluta till. En lista över klientoperativsystem som stöds finns i avsnittet [Punkt-till-plats-anslutningar](vpn-gateway-vpn-faq.md#point-to-site-connections) i Vanliga frågor och svar om VPN Gateway. 

### Generera konfigurationspaketet för VPN-klienten

1. I Azure Porta, på bladet **Översikt** för ditt VNet, i **VPN-anslutningar**, klickar du på klientbilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.
2. Högst upp på bladet **Punkt-till-plats VPN-anslutning**, klickar du på hämtningspaketet som motsvarar operativsystemet för den klient där det ska installeras:

 - För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
 - För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

    ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Du kommer att se ett meddelande om att Azure genererar konfigurationspaketet för VPN-klienten för det virtuella nätverket. Efter några minuter så genereras paketet och du får ett meddelande på din lokala dator att paketet har hämtats. Spara konfigurationspaketfilen. Du kommer att installera det här på varje klientdator som ska ansluta till den virtuella nätverket med P2S.


## Avsnitt 5 – Konfigurera klientdatorn

### Del 1: Installera klientcertifikatet

Varje klientdator måste ha ett klientcertifikat för att kunna autentisera. När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Kopiera .pfx-filen till klientdatorn.
2. Dubbelklicka på .pfx-filen för att installera den. 

### Del 2: Installera konfigurationpaketet för VPN-klienten

Du kan använda samma konfigurationspaketet för VPN-klienten på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten.

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket och dubbelklicka på EXE-filen. 

2. När paketet har installerats kan du starta VPN-anslutningen. Konfigurationspaketet är inte signerat av Microsoft. Om du vill kan du signera paketet med hjälp av organisationens signeringstjänst, eller göra det själv med hjälp av [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Du kan även använda paketet utan signering. Dock visas en varning när du installerar paketet om det inte är signerat.

3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Den visar namnet på det virtuella nätverk som anslutningen kommer att upprättas till och kommer att se ut ungefär såhär: 

    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## Avsnitt 6 – Anslut till Azure

### Anslut till ditt VNet

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 

2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

    ![Anslut](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN client connection")

3. Nu bör anslutningen upprättas.

    ![Anslutningen upprättad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connection established")

### Verifiera VPN-anslutningen

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

## Nästa steg

Du kan lägga till virtuella datorer till ditt virtuella nätverk. Se [Så här skapar du en anpassad virtuell dator](../virtual-machines/virtual-machines-windows-classic-createportal.md).


<!--HONumber=Oct16_HO3-->


