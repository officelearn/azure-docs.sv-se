---
title: 'Ansluta en dator till ett virtuellt nätverk med punkt-till-plats och certifikatautentisering: den klassiska Azure-portalen | Microsoft Docs'
description: Skapa en klassisk gatewayanslutning med en punkt-till-plats-VPN med Azure-portalen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: fe460113441933d655b183e87cceefee4dd24d24
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464335"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med certifikatautentisering: den klassiska Azure-portalen

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln visar dig hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning med den klassiska distributionsmodellen i Azure Portal. Den här konfigurationen använder certifikat för att autentisera den anslutande klienten, antingen självsignerade eller från en certifikatutfärdare. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Med en VPN-gateway med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. Punkt-till-plats-VPN-anslutningar är användbara när du vill fjärransluta till ditt VNet, exempelvis när du distansarbetar från hemmet eller en konferens. En P2S-VPN-anslutning är också en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet. En P2S-VPN-anslutning upprättas genom att du startar den från klientdatorn.

> [!IMPORTANT]
> Den klassiska distributionsmodellen stöder endast Windows VPN-klienter och använder Secure Socket Tunneling Protocol (SSTP), ett SSL-baserat VPN-protokoll. För att stödja icke-Windows VPN-klienter, måste ditt VNet skapas med hjälp av Resource Manager-distributionsmodellen. Resource Manager-distributionsmodellen stöder IKEv2 VPN, utöver SSTP. Mer information finns i [om P2S-anslutningar](point-to-site-about.md).
>
>

![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


Punkt-till-plats-anslutningar med certifikatautentisering kräver följande:

* En dynamisk VPN-gateway.
* Den offentliga nyckeln (CER-fil) för ett rotcertifikat, som överförts till Azure. Detta anses vara ett betrott certifikat och används för autentisering.
* Ett klientcertifikat genereras från rotcertifikatet och installerad på varje klientdator som ska ansluta. Det här certifikatet används för klientautentisering.
* Ett konfigurationspaket för VPN-klienter måste skapas och installeras på varje klientdator som ansluter. Klientkonfigurationspaketet konfigurerar den interna VPN-klienten som redan finns i operativsystemet med den information som krävs för att ansluta till det virtuella nätverket.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en lokal offentlig IP-adress. VPN-anslutningen upprättas via SSTP (Secure Socket Tunneling Protocol). På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard. 

Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### <a name="example-settings"></a>Exempelinställningar

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16**<br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk (se diagrammet).
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

## <a name="vnetvpn"></a>1. Skapa ett virtuellt nätverk och en VPN-gateway

Kontrollera att du har en Azure-prenumeration innan du börjar. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>Del 1: Skapa ett virtuellt nätverk

Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Glöm inte att byta ut värdena mot dina egna. Följ stegen nedan för att skapa ett VNet med Azure Portal:

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**. 
3. Nästan längst ned på sidan Virtuellt nätverk, från listan **Välj en distributionsmodell**, väljer du **Klassisk** och klickar sedan på **Skapa**.

  ![Välj distributionsmodell](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. På den här sidan lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

  ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Om du skapar en ny resursgrupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.

  ![Fäst vid instrumentpanelen](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. När du klickar på Skapa, kommer du att se en ikon på din instrumentpanel som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

  ![Skapa en virtuell nätverksikon](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. När ditt virtuella nätverk har skapats, ser du **skapat**.
11. Lägg till en DNS-server (valfritt). När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för namnmatchning. Den ip-adress för DNS-server som du anger måste vara en adress till en DNS-server som kan matcha namn för resurserna i ditt virtuella nätverk.<br>Om du vill lägga till en DNS-server öppnar du inställningarna för det virtuella nätverket, klickar på DNS-servrar och lägger till IP-adressen för den DNS-server du vill använda.

### <a name="gateway"></a>Del 2: Skapa gateway-undernät och en dynamisk routningsgateway

I det här steget skapar du ett gateway-undernät och en dynamisk routningsgateway. I Azure-portalen för den klassiska distributionsmodellen, kan du skapa gateway-undernätet och gatewayen genom samma konfigurationssidor. Gateway-undernätet används endast för gateway-tjänsterna. Distribuera aldrig något direkt till gateway-undernätet (till exempel virtuella datorer eller andra tjänster).

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.
2. På sidan för ditt virtuella nätverk, på sidan **Översikt**, i avsnittet VPN-anslutningar, klickar du på **Gateway**.

  ![Klicka för att skapa en gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. På sidan **Ny VPN-anslutning** väljer du **Punkt-till-plats**.

  ![Punkt-till-plats-anslutningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Lägg till IP-adressutrymme för **klientens adressutrymme**. Detta är intervallet från vilket VPN-klienterna får en IP-adress när de ansluter. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Du kan ta bort det automatiskt ifyllda intervallet och sedan lägga till det intervall med privata IP-adresser som du vill använda. Det här exemplet visar intervallet som fylls i automatiskt. Ta bort det om du vill lägga till ett eget valfritt värde.

  ![Adressutryme för klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Välj kryssutan **Skapa gateway omedelbart**. Klicka på **Valfri gatewaykonfiguration** för att öppna sidan **Gatewaykonfiguration**.

  ![Klicka på valfri gatewaykonfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
6. Klicka på **Konfigurera obligatoriska inställningar för undernät**, för att lägga till **Gateway-undernätet**. Även om det är möjligt att skapa ett gatewayundernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden. När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp (NSG) till det här undernätet så kan din VPN-gateway sluta fungera som förväntat.

  ![Lägg till GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Välj gateway-**storlek**. Storleken är SKU-gatewayen för det virtuella nätverkets gateway. I portalen är standard-SKU:n **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Gateway-storlek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Välj **Routningstyp** för din gateway. P2S konfigurationer kräver en **Dynamisk** routningstyp. Klicka på **OK** när du har konfigurerat den här sidan.

  ![Konfigurera routningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
9. På sidan **Ny VPN-anslutning** klickar du på **OK** längst ned på sidan för att börja skapa din virtuella nätverksgateway. Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken gateway-sku du väljer.

## <a name="generatecerts"></a>2. Skapa certifikat

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara ”betrodd”. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare/Personlig. Certifikatet används för att autentisera klienten när den upprättar en anslutning till det virtuella nätverket. 

Om du använder självsignerade certifikat, måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du arbetare självsignerade rotcertifikat och skapar klientcertifikat från det självsignerade rotcertifikatet. Annars kommer dina certifikat inte vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="cer"></a>Del 1: Hämta den offentliga nyckeln (.cer) för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Del 2: Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. Ladda upp .cer-filen med rotcertifikatet

När gatewayen har skapats kan du överföra CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När en CER-fil har överförts kan Azure använda den för att autentisera klienter som har ett installerat klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan överföra ytterligare betrodda rotcertifikatfiler (upp till 20 stycken) senare om det behövs.  

1. I avsnittet **VPN-anslutningar** på sidan för din VNet, klickar du på **klienter**-bilden för att öppna sidan **Punkt-till-plats-VPN-anslutning**.

  ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På sidan **Punkt-till-plats-anslutning** klickar du på **Hantera certifikat** för att öppna sidan **Certifikat**.<br>

  ![Sidan Certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På sidan **Certifikat** klickar du på **Överför** för att öppna sidan **Överför certifikat**.<br>

    ![Sidan Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klicka på mappbilden och bläddra efter .cer-filen. Välj filen och klicka sedan på **Ok**. Uppdatera sidan för att se överförda certifikat på sidan **Certifikat**.

  ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. Konfigurera klienten

För att ansluta till ett virtuellt nätverk med hjälp av ett VPN för punkt-till-plats måste varje klient installera ett paket för konfiguration av den interna Windows VPN-klienten. Konfigurationspaketet ställer in den interna Windows VPN-klienten med de inställningar som krävs för att ansluta till det virtuella nätverket.

Du kan använda samma VPN-klientkonfigurationspaket på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten. En lista över klientoperativsystem som stöds finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.

### <a name="generateconfigpackage"></a>Del 1 – Skapa och installera VPN-klientkonfigurationspaketet

1. I Azure Portal, på sidan **Översikt** för ditt VNet, i **VPN-anslutningar**, klickar du på klientbilden för att öppna sidan **Punkt-till-plats-VPN-anslutning**.
2. Högst upp på sidan **Punkt-till-plats VPN-anslutning**, klickar du på hämtningspaketet som motsvarar operativsystemet för den klient där det ska installeras:

  * För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
  * För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

  ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. När paketet har skapats kan du ladda ned och installera det på klientdatorn. Om du ser ett SmartScreen-popup-fönster klickar du på **Mer information** och sedan på **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.

### <a name="installclientcert"></a>Del 2: Installera klientcertifikatet

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. Vanligtvis behöver du bara dubbelklicka på certifikatet och installera det. Mer information finns i [Installera ett exporterat klientcertifikat](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>5. Anslut till Azure

### <a name="connect-to-your-vnet"></a>Anslut till ditt VNet

>[!NOTE]
>Du måste ha administratörsbehörigheter på den klientdator som du använder för att ansluta.
>
>

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier.
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

  ![VPN-klientanslutning](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Anslutningen upprättas.

  ![Anslutningen upprättad](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Felsöka P2S-anslutningar

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>Verifiera VPN-anslutningen

1. Om du vill verifiera att VPN-anslutningen är aktiv öppnar du en upphöjd kommandotolk från klientdatorn och kör *ipconfig/all*.
2. Granska resultaten. Observera att den IP-adress som du har fått är en av adresserna inom adressintervallet för punkt-till-plats-anslutningen som du angav när du skapade ditt VNet. Resultatet bör likna det här exemplet:

  ```
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
  ```

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Lägg till eller ta bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kommer klienter som har ett certifikat som genereras från den roten inte att kunna autentisera, och därmed kommer de inte heller att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure.

### <a name="addtrustedroot"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Anvisningar finns i [Avsnitt 3 – Ladda upp .cer-filen med rotcertifikatet](#upload).

### <a name="removetrustedroot"></a>Så här tar du bort ett betrott rotcertifikat

1. I avsnittet **VPN-anslutningar** på sidan för din VNet, klickar du på **klienter**-bilden för att öppna sidan **Punkt-till-plats-VPN-anslutning**.

  ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. På sidan **Punkt-till-plats-anslutning** klickar du på **Hantera certifikat** för att öppna sidan **Certifikat**.<br>

  ![Sidan Certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. På sidan **Certifikat** klickar du på knappen med tre punkter bredvid det certifikat som du vill ta bort och sedan på **Ta bort**.

  ![Ta bort rotcertifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Återkalla ett klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Detta skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="revokeclientcert"></a>Så här återkallar du ett klientcertifikat

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [How to: Retrieve the Thumbprint of a Certificate](https://msdn.microsoft.com/library/ms734695.aspx) (Gör så här: Hämta tumavtrycket för ett certifikat).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
3. Navigera till sidan **Klassiskt virtuellt nätverksnamn > Punkt-till-plats-VPN-anslutning > Certifikat** och klicka sedan på **Lista över återkallade certifikat** för att öppna sidan med listan över återkallade certifikat. 
4. På sidan **Lista över återkallade certifikat** klickar du på **+Lägg till certifikat** för att öppna sidan **Lägg till certifikat i listan över återkallade certifikat**.
5. På sidan **Lägg till certifikat i listan över återkallade certifikat** klistrar du in certifikattumavtrycket som en kontinuerlig textrad, utan blanksteg. Klicka på **OK** längst ned på sidan.
6. När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).