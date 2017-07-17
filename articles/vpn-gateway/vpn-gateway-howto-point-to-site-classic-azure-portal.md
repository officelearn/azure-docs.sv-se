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
ms.date: 06/27/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f048e344026b0fd930569c949b23a42c3c30fffe
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017


---
# Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med Azure-portalen (klassisk)
<a id="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic" class="xliff"></a>

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln visar dig hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning med den klassiska distributionsmodellen i Azure Portal. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S VPN-anslutningen initieras från klientdatorn med hjälp av den inbyggda Windows VPN-klienten. Anslutande klienter använder certifikat för autentisering. 


![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

P2S-anslutningar kräver följande:

* En dynamisk VPN-gateway.
* Den offentliga nyckeln (CER-fil) för ett rotcertifikat som överförts till Azure. Detta anses vara ett betrott certifikat och används för autentisering.
* Ett klientcertifikat genereras från rotcertifikatet och installerad på varje klientdator som ska ansluta. Det här certifikatet används för klientautentisering.
* Ett konfigurationspaket för VPN-klienter måste skapas och installeras på varje klientdator som ansluter. Klientkonfigurationspaketet konfigurerar den interna VPN-klienten som redan finns i operativsystemet med den information som krävs för att ansluta till det virtuella nätverket.

### Exempelinställningar
<a id="example-settings" class="xliff"></a>

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

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
4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. På det här bladet lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

  ![Skapa ett virtuellt nätverksblad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Om du skapar en ny resursgrupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.

  ![Fäst vid instrumentpanelen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. När du klickar på Skapa, kommer du att se en ikon på din instrumentpanel som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

  ![Skapa en virtuell nätverksikon](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen.
11. Lägg till en DNS-server (valfritt). När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för namnmatchning. Den DNS-server som du anger måste vara en som kan matcha namn för resurserna i ditt virtuella nätverk.<br>Om du vill lägga till en DNS-server öppnar du inställningarna för det virtuella nätverket, klickar på DNS-servrar och lägger till IP-adressen för den DNS-server du vill använda. Konfigurationspaketet för klienten som du genererar i ett senare skede innehåller IP-adresserna för DNS-servrarna som du anger i den här inställningen. Om du behöver uppdatera listan över DNS-servrar i framtiden kan du generera och installera nya konfigurationspaket för VPN-klienter som reflekterar den uppdaterade listan.

### <a name="gateway"></a>Del 2: Skapa gateway-undernät och en dynamisk routningsgateway

I det här steget skapar du ett gateway-undernät och en dynamisk routningsgateway. I Azure-portalen för den klassiska distributionsmodellen, kan du skapa gateway-undernätet och gatewayen genom samma konfigurationsblad.

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.
2. På bladet för ditt virtuella nätverk, på **Översikt**-bladet, i avsnittet VPN-anslutningar, klickar du på **Gateway**.

  ![Klicka för att skapa en gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. På **Ny VPN-anslutning**-bladet, väljer du **punkt-till-plats**.

  ![Punkt-till-plats-anslutningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Lägg till IP-adressutrymme för **klientens adressutrymme**. Detta är intervallet från vilket VPN-klienterna får en IP-adress när de ansluter. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Du kan ta bort det automatiskt ifyllda intervallet och sedan lägga till det intervall med privata IP-adresser som du vill använda.

  ![Adressutryme för klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Välj kryssutan **Skapa gateway omedelbart**.

  ![Skapa gatewayen omedelbart](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Klicka på **Valfri gatewaykonfiguration**, för att öppna **Gatewaykonfiguration**-bladet.

  ![Klicka på valfri gatewaykonfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Klicka på **Konfigurera obligatoriska inställningar för undernät**, för att lägga till **Gateway-undernätet**. Även om det är möjligt att skapa ett gateway-subnät som är så litet som /29 så rekommenderar vi att du skapar ett större subnät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden. När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp (NSG) till det här undernätet så kan din VPN-gateway sluta fungera som förväntat.

  ![Lägg till GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Välj gateway-**storlek**. Storleken är SKU-gatewayen för det virtuella nätverkets gateway. I portalen är standard-SKU:n **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Gateway-storlek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Välj **Routningstyp** för din gateway. P2S konfigurationer kräver en **Dynamisk** routningstyp. Klicka på **Ok** när du har konfigurerat det här bladet.

  ![Konfigurera routningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. På bladet **Ny VPN-anslutning**, klickar du på **Ok** längst ned på bladet för att börja skapa din virtuella nätverksgateway. Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken gateway-sku du väljer.

## <a name="generatecerts"></a>Avsnitt 2 – Skapa certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara ”betrodd”. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare/Personlig. Certifikatet används för att autentisera klienten när den upprättar en anslutning till det virtuella nätverket. 

Om du använder självsignerade certifikat, måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du arbetare självsignerade rotcertifikat och skapar klientcertifikat från det självsignerade rotcertifikatet. Annars kommer dina certifikat inte vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="cer"></a>Del 1: Hämta den offentliga nyckeln (.cer) för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Del 2: Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet

När gatewayen har skapats kan du överföra CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När en CER-fil har överförts kan Azure använda den för att autentisera klienter som har ett installerat klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan överföra ytterligare betrodda rotcertifikatfiler (upp till 20 stycken) senare om det behövs.  

1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

  ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

  ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På **Certifikat**-bladet, klickar du på **Överför** för att öppna **Överför certifikat**-bladet.<br>

    ![Överför certifikat-bladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klicka på mappbilden och bläddra efter .cer-filen. Välj filen och klicka sedan på **Ok**. Uppdatera sidan för att se överförda certifikat på **certifikat**-bladet.

  ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Avsnitt 4 – Konfigurera klienten

För att ansluta till ett virtuellt nätverk med hjälp av ett VPN för punkt-till-plats måste varje klient installera ett paket för konfiguration av den interna Windows VPN-klienten. Konfigurationspaketet konfigurerar den inbyggda VPN-klienten för Windows med inställningarna som behövs för att ansluta till det virtuella nätverket, och om du har angett en DNS-server för ditt virtuella nätverk innehåller den DNS-serverns IP-adress som klienten använder för namnmatchning. Om du ändrar den angivna DNS-servern senare, efter att du har genererat konfigurationspaketet för klienten, bör du generera ett nytt konfigurationpaket för klienten som du kan använda för att installera på klientdatorerna.

Du kan använda samma VPN-klientkonfigurationspaket på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten. En lista över klientoperativsystem som stöds finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### Del 1 – Skapa och installera VPN-klientkonfigurationspaketet
<a id="part-1-generate-and-install-the-vpn-client-configuration-package" class="xliff"></a>

1. I Azure Porta, på bladet **Översikt** för ditt VNet, i **VPN-anslutningar**, klickar du på klientbilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.
2. Högst upp på bladet **Punkt-till-plats VPN-anslutning**, klickar du på hämtningspaketet som motsvarar operativsystemet för den klient där det ska installeras:

  * För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
  * För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

  ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. När paketet har skapats kan du ladda ned och installera det på klientdatorn. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.

### Del 2: Installera klientcertifikatet
<a id="part-2-install-the-client-certificate" class="xliff"></a>

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. Vanligtvis behöver du bara dubbelklicka på certifikatet och installera det. Mer information finns i [Installera ett exporterat klientcertifikat](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>Avsnitt 5 – Anslut till Azure

### Anslut till ditt VNet
<a id="connect-to-your-vnet" class="xliff"></a>

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier.
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

  ![VPN-klientanslutning](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Anslutningen upprättas.

  ![Anslutningen upprättad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

Om du har problem med att ansluta kontrollerar du följande:

- Öppna **Hantera användarcertifikat** och gå till **Betrodda rotcertifikatutfärdare\Certifikat**. Kontrollera att rotcertifikatet visas i listan. Rotcertifikatet måste finnas för att autentiseringen ska fungera. När du exporterar ett klientcertifikat av typen .pfx med hjälp av standardvärdet ”Ta med om möjligt alla certifikat i certifieringssökvägen” exporteras också rotcertifikatsinformationen. När du installerar klientcertifikatet installeras också rotcertifikatet på klientdatorn. 

- Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan. 

### Verifiera VPN-anslutningen
<a id="verify-the-vpn-connection" class="xliff"></a>

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

 
 Om du har problem med att ansluta till en virtuell dator via P2S kan du använda ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket. Om dina nätverksadressutrymmen inte överlappar men du ändå inte kan ansluta till den virtuella datorn läser du [Troubleshoot Remote Desktop connections to a VM](../virtual-machines/windows/troubleshoot-rdp-connection.md) (Felsöka fjärrskrivbordsanslutningar till en virtuell dator).

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Lägg till eller ta bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kommer klienter som har ett certifikat som genereras från den roten inte att kunna autentisera, och därmed kommer de inte heller att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure.

### Lägga till ett betrott rotcertifikat
<a id="to-add-a-trusted-root-certificate" class="xliff"></a>

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Anvisningar finns i [Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet](#upload).

### Så här tar du bort ett betrott rotcertifikat
<a id="to-remove-a-trusted-root-certificate" class="xliff"></a>

1. I avsnittet **VPN-anslutningar** i bladet för din VNet, klickar du på **klienter**-bilden för att öppna bladet **punkt-till-plats-VPN-anslutning**.

  ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På bladet **punkt-till-plats-anslutning**, klickar du på **hantera certifikat** för att öppna bladet **certifikat**.<br>

  ![Certifikatbladet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På bladet **Certifikat** klickar du på knappen med tre punkter bredvid det certifikat som du vill ta bort och sedan på **Ta bort**.

  ![Ta bort rotcertifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Återkalla ett klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Detta skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### Återkalla ett klientcertifikat
<a id="to-revoke-a-client-certificate" class="xliff"></a>

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [How to: Retrieve the Thumbprint of a Certificate](https://msdn.microsoft.com/library/ms734695.aspx) (Gör så här: Hämta tumavtrycket för ett certifikat).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
3. Navigera till bladet **Klassiskt virtuellt nätverksnamn > Punkt-till-plats-VPN-anslutning > Certifikat** och klicka sedan på **Lista över återkallade certifikat** för att öppna bladet med listan över återkallade certifikat. 
4. På bladet **Lista över återkallade certifikat** klickar du på **+ Lägg till certifikat** att öppna bladet **Lägg till certifikat i listan över återkallade certifikat**.
5. På bladet **Lägg till certifikat i listan över återkallade certifikat** klistrar du in certifikattumavtrycket som en kontinuerlig textrad, utan blanksteg. Klicka på **OK** längst ned på bladet.
6. När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## Nästa steg
<a id="next-steps" class="xliff"></a>
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

