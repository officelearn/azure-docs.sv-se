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
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bc542cfbca3a27aec98b75e2b7ed05472419c3a7
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med Azure-portalen (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn.

Den här artikeln beskriver steg för steg hur du skapar ett VNet med en punkt-till-plats-anslutning i den klassiska distributionsmodellen med Azure Portal. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.


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
* **Adressutrymme: 192.168.0.0/16**<br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk.
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



## <a name="vnetvpn"></a>Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway

Kontrollera att du har en Azure-prenumeration innan du börjar. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>Del 1: Skapa ett virtuellt nätverk
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

### <a name="gateway"></a>Del 2: Skapa gateway-undernät och en dynamisk routningsgateway
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

## <a name="generatecerts"></a>Avsnitt 2 – Skapa certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil. Du kan sedan överföra offentliga certifikatdata från rotcertifikatet till Azure.

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt klientcertifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

### <a name="cer"></a>Del 1: Hämta den offentliga nyckeln (.cer) för rotcertifikatet

####<a name="enterprise-certificate"></a>Företagscertifikat
 
Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Hämta .cer-filen för det rotcertifikat som du vill använda.

####<a name="self-signed-root-certificate"></a>Självsignerat rotcertifikat

Om du inte använder en företagscertifikatlösning måste du generera ett självsignerat rotcertifikat. Du kan använda PowerShell om du vill skapa ett självsignerat certifikat som innehåller de fält som behövs för P2S-autentisering. Avsnittet [Create a self-signed certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat certifikat för punkt-till-plats-anslutningar med PowerShell) vägleder dig genom stegen för att skapa ett självsignerat rotcertifikat.

> [!NOTE]
> Tidigare var makecert den metod som rekommenderades för att skapa självsignerade rotcertifikat och generera klientcertifikat för punkt-till-plats-anslutningar. Du kan nu använda PowerShell för att skapa dessa certifikat. En fördel med att använda PowerShell är möjligheten att skapa SHA-2-certifikat. I [Create a self-signed certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat certifikat för punkt-till-plats-anslutningar med PowerShell) finns information om nödvändiga värden.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Så här exporterar du den offentliga nyckeln för ett självsignerat rotcertifikat

Punkt-till-plats-anslutningar kräver att den offentliga nyckeln (.cer) överförs till Azure. Följande steg hjälper dig att exportera .cer-filen för det självsignerade rotcertifikatet.

1. Om du vill hämta en .cer-fil från certifikatet kan du öppna **certmgr.msc**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas.
2. Klicka på **Nästa** i guiden. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet. Meddelandet **Exporten lyckades**. Stäng guiden genom att klicka på **OK**.

### <a name="genclientcert"></a>Del 2: Generera ett klientcertifikat

Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla istället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

####<a name="enterprise-certificate"></a>Företagscertifikat
- Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
- Se till att det klientcertifikat som du utfärdar baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

####<a name="self-signed-root-certificate"></a>Självsignerat rotcertifikat 
Om du använder ett självsignerat rotcertifikat kan du läsa [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Generera ett klientcertifikat med PowerShell) för att få anvisningar om att generera ett klientcertifikat som är kompatibelt med punkt-till-plats-anslutningar.

### <a name="exportclientcert"></a>Del 3: Exportera klientcertifikatet
Om du skapar ett klientcertifikat från ett självsignerat rotcertifikat med hjälp av [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert)-instruktionerna installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det.

1. Du kan exportera ett klientcertifikat genom att öppna **certmgr.msc**. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Ja, exportera den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Klicka sedan på **Nästa**.
4. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.
5. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
6. Klicka på **Slutför** för att exportera certifikatet.

## <a name="upload"></a>Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet
När gatewayen har skapats, kan du överföra .cer-filen för ett betrott rotcertifikat till Azure. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

    ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

    ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På **Certifikat**-bladet, klickar du på **Överför** för att öppna **Överför certifikat**-bladet.<br>

    ![Överför certifikat-bladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klicka på mappbilden och bläddra efter .cer-filen. Välj filen och klicka sedan på **Ok**. Uppdatera sidan för att se överförda certifikat på **certifikat**-bladet.

    ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Avsnitt 4 – Generera VPN-klientens konfigurationspaket
För att ansluta till det virtuella nätverket måste du också konfigurera en VPN-klient. Klientdatorn kräver både ett klientcertifikat och rätt konfigurationspaket för VPN-klienten för att kunna ansluta.

VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara. Inställningarna är specifika för det virtuella nätverk som du vill ansluta till. En lista över klientoperativsystem som stöds finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Generera konfigurationspaketet för VPN-klienten
1. I Azure Porta, på bladet **Översikt** för ditt VNet, i **VPN-anslutningar**, klickar du på klientbilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.
2. Högst upp på bladet **Punkt-till-plats VPN-anslutning**, klickar du på hämtningspaketet som motsvarar operativsystemet för den klient där det ska installeras:

   * För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
   * För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

     ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Du kommer att se ett meddelande om att Azure genererar konfigurationspaketet för VPN-klienten för det virtuella nätverket. Efter några minuter så genereras paketet och du får ett meddelande på din lokala dator att paketet har hämtats. Spara konfigurationspaketfilen. Du kommer att installera det här på varje klientdator som ska ansluta till den virtuella nätverket med P2S.

## <a name="clientconfiguration"></a>Avsnitt 5 – Konfigurera klientdatorn
### <a name="part-1-install-an-exported-client-certificate"></a>Del 1: Installera ett exporterat klientcertifikatet

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Leta upp och kopiera *.pfx*-filen till klientdatorn. Installera *.pfx*-filen genom att dubbelklicka på den på klientdatorn. Låt **Lagringsplats** stå som **Aktuell användare** och klicka sedan på **Nästa**.
2. Gör inga ändringar på sidan för den **Fil** som ska importeras. Klicka på **Nästa**.
3. På sidan **Skydd av privat nyckel** anger du lösenordet för certifikatet, om du har använt ett sådant, eller bekräftar att säkerhetsobjektet som installerar certifikatet är korrekt, och klickar sedan på **Nästa**.
4. På sidan **Certifikatarkiv** låter du standardplatsen stå kvar och klickar sedan på **Nästa**.
5. Klicka på **Slutför**. På sidan **Säkerhetsvarning** för certifikatinstallationen klickar du på **Ja**. Eftersom du har genererat certifikatet kan du klicka på ”Ja” utan att oroa dig. Certifikatet har nu importerats.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Del 2: Installera konfigurationpaketet för VPN-klienten
Du kan använda samma konfigurationspaketet för VPN-klienten på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten.

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket. 
2. Installera paketet på klientdatorn genom att dubbelklicka på EXE-filen. Konfigurationspaketet är inte signerat eftersom du har skapat det. Det innebär att varningsmeddelande kan visas. Om ett Windows SmartScreen-fönster öppnas klickar du på **Mer info** (till vänster) och sedan på **Kör ändå** för att kunna installera paketet.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Den visar namnet på det virtuella nätverk som anslutningen kommer att upprättas till och kommer att se ut ungefär såhär:

    ![VPN-klient](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>Avsnitt 6 – Anslut till Azure
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

## <a name="add"></a>Lägg till eller ta bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett betrott certifikat kan klientcertifikaten som genererades från rotcertifikatet inte längre ansluta till Azure via punkt-till-plats. Om du vill att klienter ska kunna ansluta måste du installera ett nytt klientcertifikat som genereras från ett certifikat som är betrott i Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Anvisningar finns i [Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Så här tar du bort ett betrott rotcertifikat


1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

    ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

    ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På bladet **Certifikat** klickar du på knappen med tre punkter bredvid det certifikat som du vill ta bort och sedan på **Ta bort**.

     ![Ta bort rotcertifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Återkalla ett klientcertifikat
Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Detta skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="to-revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [How to: Retrieve the Thumbprint of a Certificate](https://msdn.microsoft.com/library/ms734695.aspx) (Gör så här: Hämta tumavtrycket för ett certifikat).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
3. Navigera till bladet **Klassiskt virtuellt nätverksnamn > Punkt-till-plats-VPN-anslutning > Certifikat** och klicka sedan på **Lista över återkallade certifikat** för att öppna bladet med listan över återkallade certifikat. 
4. På bladet **Lista över återkallade certifikat** klickar du på **+ Lägg till certifikat** att öppna bladet **Lägg till certifikat i listan över återkallade certifikat**.
5. På bladet **Lägg till certifikat i listan över återkallade certifikat** klistrar du in certifikattumavtrycket som en kontinuerlig textrad, utan blanksteg. Klicka på **OK** längst ned på bladet.
6. När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet är inte längre giltigt.


## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

