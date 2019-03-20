---
title: 'Ansluta en dator till ett virtuellt nätverk med punkt-till-plats och certifikatautentisering: Klassiska Azure-portalen | Microsoft Docs'
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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 74940f3b89237233acd575aa5df441163e00d178
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000938"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurera en punkt-till-plats-anslutning med hjälp av certifikatautentisering (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln beskriver hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning. Du skapar det här virtuella nätverket med den klassiska distributionsmodellen med hjälp av Azure Portal. Den här konfigurationen använder certifikat för att autentisera den anslutande klienten, antingen självsignerade eller från en certifikatutfärdare. Du kan också skapa konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell med hjälp av alternativen som beskrivs i följande artiklar:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Du använder en VPN-gateway med P2S-konfiguration (punkt-till-plats) för att skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. P2S-VPN-anslutningar är användbara när du vill ansluta till ditt virtuella nätverk från en fjärransluten plats. P2S-VPN-anslutningar är en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. En P2S-VPN-anslutning upprättas genom att du startar den från klientdatorn.

> [!IMPORTANT]
> Den klassiska distributionsmodellen stöder endast Windows VPN-klienter och använder Secure Socket Tunneling Protocol (SSTP), ett SSL-baserat VPN-protokoll. För att ge stöd för icke-Windows-baserade VPN-klienter måste du skapa ditt virtuella nätverk med Resource Manager-distributionsmodellen. Resource Manager-distributionsmodellen stöder IKEv2 VPN, utöver SSTP. Mer information finns i [om P2S-anslutningar](point-to-site-about.md).
>
>

![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Förutsättningar

Punkt-till-plats-anslutningar med certifikatautentisering kräver följande:

* En dynamisk VPN gateway.
* Den offentliga nyckeln (CER-fil) för ett rotcertifikat, som överförts till Azure. Den här nyckeln anses vara ett betrott certifikat och används för autentisering.
* Ett klientcertifikat genereras från rotcertifikatet och installerad på varje klientdator som ska ansluta. Det här certifikatet används för klientautentisering.
* Ett konfigurationspaket för VPN-klienter måste skapas och installeras på varje klientdator som ansluter. Klientkonfigurationspaketet konfigurerar den interna VPN-klienten som redan finns i operativsystemet med den information som krävs för att ansluta till det virtuella nätverket.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en lokal offentlig IP-adress. VPN-anslutningen upprättas via SSTP (Secure Socket Tunneling Protocol). På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard. 

Mer information om punkt-till-plats-anslutningar finns i [avsnittet med vanliga frågor och svar om punkt-till-plats](#point-to-site-faq).

### <a name="example-settings"></a>Exempelinställningar

Använd följande värden för att skapa en testmiljö eller för att bättre förstå exemplen i den här artikeln:

- **Skapa inställningar för virtuellt nätverk (klassisk)**
   - **Namn**: Ange *VNet1*.
   - **Adressutrymme**: Ange *192.168.0.0/16*. I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk (se diagrammet).
   - **Namn på undernät**: Ange *FrontEnd*.
   - **Adressintervall för undernätet**: Ange *192.168.1.0/24*.
   - **Prenumeration**: Välj en prenumeration i listan med tillgängliga prenumerationer.
   - **Resursgrupp**: Ange *TestRG*. Välj **Skapa ny** om resursgruppen inte redan finns.
   - **Plats**: Välj **USA, östra** i listan.

  - **VPN-anslutningsinställningar**
    - **Anslutningstyp**: Välj **Punkt-till-plats**.
    - **Adressutrymme för klienten**: Ange *172.16.201.0/24*. VPN-klienter som ansluter till VNet med den här punkt-till-plats-anslutningen får en IP-adress från den angivna poolen.

- **Inställningar för undernät i gateway-konfiguration**
   - **Namn**: Automatiskt ifyllt med *GatewaySubnet*.
   - **Adressintervall**: Ange *192.168.200.0/24*. 

- **Inställningar för gateway-konfiguration**:
   - **Storlek**: Välj den gateway-SKU som du vill använda.
   - **Routningstyp**: Välj **Dynamisk**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Skapa ett virtuellt nätverk och en VPN-gateway

Innan du börjar kontrollerar du att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Del 1: Skapa ett virtuellt nätverk

Om du inte redan har ett virtuellt nätverk (VNet) skapar du ett. Skärmbilderna anges som exempel. Glöm inte att byta ut värdena mot dina egna. Följ stegen nedan för att skapa ett VNet med Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Skapa en resurs**. Sidan **Nytt** öppnas. 

2. I fältet **Sök på Marketplace** anger du *virtuellt nätverk* och väljer **Virtuellt nätverk** i listan som returneras. Sidan **Virtuellt nätverk** öppnas.

3. Välj **Klassisk** från **Välj en distributionsmodell** och välj sedan **Skapa**. Sidan **Skapa virtuellt nätverk** öppnas.

4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. På den här sidan lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Välj den **prenumeration** som du vill använda i listrutan.

6. Välj en befintlig **resursgrupp**. Alternativt skapar du en ny resursgrupp genom att välja **Skapa ny** och ange ett namn. Om du skapar en ny resursgrupp namnger du den baserat på dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Välj en **plats** för det virtuella nätverket. Den här inställningen avgör den geografiska platsen för de resurser som du distribuerar till det här virtuella nätverket.

8. Skapa det virtuella nätverket genom att klicka på **Skapa**. På sidan **Meddelanden** visas ett meddelande som anger att en **distribution pågår**.

8. När det virtuella nätverket har skapats ändras meddelandet på sidan **Meddelanden** till **Distributionen lyckades**. Välj **Fäst på instrumentpanelen** om du vill ha snabb åtkomst till det virtuella nätverket på instrumentpanelen. 

10. Lägg till en DNS-server (valfritt). När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för namnmatchning. Den ip-adress för DNS-server som du anger måste vara en adress till en DNS-server som kan matcha namn för resurserna i ditt virtuella nätverk.

    Lägg till en DNS-server genom att välja **DNS-servrar** på sidan för det virtuella nätverket. Ange IP-adressen för DNS-servern som du vill använda och välj sedan **Spara**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Del 2: Skapa ett gateway-undernät och en gateway för dynamisk routning

I det här steget skapar du ett gateway-undernät och en gateway för dynamisk routning. På Azure Portal för den klassiska distributionsmodellen kan du skapa gateway-undernätet och gatewayen via samma konfigurationssidor. Använd endast gateway-undernätet för gateway-tjänsterna. Distribuera aldrig något direkt till gateway-undernätet (till exempel virtuella datorer eller andra tjänster).

1. På Azure Portal går du till det virtuella nätverket som du vill skapa en gateway för.

2. Välj **Översikt** på sidan för det virtuella nätverket och välj sedan **Gateway** i avsnittet **VPN-anslutningar**.

   ![Välj att skapa en gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. På sidan **Ny VPN-anslutning** väljer du **Punkt-till-plats**.

   ![Punkt-till-plats-anslutningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. För **Adressutrymme för klienten** lägger du till IP-adressintervallet som VPN-klienterna får en IP-adress från när de ansluter. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du ansluter till. Du kan skriva över det automatiskt ifyllda intervallet med det intervall med privata IP-adresser som du vill använda. I det här exemplet visas det automatiskt ifyllda intervallet. 

   ![Adressutryme för klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Välj **Skapa gateway omedelbart** och öppna sedan sidan **Gateway-konfiguration** genom att välja **Valfri gateway-konfiguration**.

   ![Välj Valfri gateway-konfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Från sidan **Gateway-konfiguration** väljer du **Undernät** för att lägga till gateway-undernätet. Det går att skapa ett gateway-undernät så litet som/29. Vi rekommenderar dock att du skapar ett större undernät som omfattar fler adresser genom att välja minst /28 eller/27. På så sätt finns det tillräckligt med adresser för eventuella konfigurationer som du kan behöva i framtiden. När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp till det här undernätet kan det ledat till att din VPN-gateway inte fungerar som förväntat. Spara inställningen genom att välja **OK**.

   ![Lägg till GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Välj gateway-**storlek**. Storleken är SKU-gatewayen för det virtuella nätverkets gateway. Standard-SKU på Azure Portal är **Standard**. Mer information om gateway-SKU:er finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Gateway-storlek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Välj **Routningstyp** för din gateway. P2S konfigurationer kräver en **Dynamisk** routningstyp. Välj **OK** när du har konfigurerat den här sidan.

   ![Konfigurera routningstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Välj **OK** längst ned på sidan **Ny VPN-anslutning** för att börja skapa din virtuella nätverksgateway. Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken gateway-SKU du väljer.
 
## <a name="generatecerts"></a>Skapa certifikat

Azure använder certifikat för att autentisera VPN-klienter för punkt-till-plats-VPN. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara *betrodd*. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare\Personlig\Certifikat. Certifikatet används för att autentisera klienten när den ansluter till det virtuella nätverket. 

Om du använder självsignerade certifikat måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du använder självsignerade rotcertifikat och genererar klientcertifikat från det självsignerade rotcertifikatet. Annars kommer dina certifikat inte att vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Hämta den offentliga nyckeln (.cer) för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Ladda upp .cer-filen med rotcertifikatet

När gatewayen har skapats laddar du upp CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure-servern. Ladda inte upp den privata nyckeln för rotcertifikatet. När du har laddat upp certifikatet använder Azure det för att autentisera klienter som har installerat ett klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan ladda upp ytterligare betrodda rotcertifikatfiler (upp till 20) senare om det behövs.  

1. Från avsnittet **VPN-anslutningar** på sidan för ditt virtuella nätverk öppnar du sidan **Punkt-till-plats-VPN-anslutning** genom att välja bilden som representerar klienterna.

   ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. På sidan **Punkt-till-plats-VPN-anslutning** väljer du **Hantera certifikat** för att öppna sidan **Certifikat**.

   ![Sidan Certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. På sidan **Certifikat** väljer du **Överför** för att öppna sidan **Överför certifikat**.

    ![Sidan Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Välj mappbilden och bläddra efter CER-filen. Markera filen och välj sedan **OK**. Det uppladdade certifikatet visas på sidan **Certifikat**.

   ![Överför certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurera klienten

För att ansluta till ett virtuellt nätverk med hjälp av ett VPN för punkt-till-plats måste varje klient installera ett paket för konfiguration av den interna Windows VPN-klienten. Konfigurationspaketet ställer in den interna Windows VPN-klienten med de inställningar som krävs för att ansluta till det virtuella nätverket.

Du kan använda samma VPN-klientkonfigurationspaket på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten. Listan över klientoperativsystem som stöds finns i [avsnittet med vanliga frågor och svar om punkt-till-plats-anslutningar](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Skapa och installera ett VPN-klientkonfigurationspaket

1. Från **VPN-anslutningar** på sidan **Översikt** för ditt virtuella nätverk på Azure Portal öppnar du sidan **Punkt-till-plats-VPN-anslutning** genom att välja bilden som representerar klienterna.

2. Från sidan **Punkt-till-plats-VPN-anslutning** väljer du nedladdningspaketet för rätt klientoperativsystem:

   * För 64-bitarsklienter, väljer du **VPN-klient (64-bitars)**.
   * För 32-bitarsklienter, väljer du **VPN-klient (32-bitars)**.

   ![Hämta konfigurationspaketet för VPN-klienten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. När paketet har genererats laddar du ned det och installerar det på klientdatorn. Om ett SmartScreen-fönster visas väljer du **Mer information** och väljer sedan **Kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.

### <a name="install-a-client-certificate"></a>Installera ett klientcertifikat

Om du vill skapa en P2S-anslutning från en annan klientdator än den som används för att generera klientcertifikaten installerar du ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. Normalt kan du installera certifikatet genom att bara dubbelklicka på det. Mer information finns i [Installera ett exporterat klientcertifikat](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Anslut till ditt VNet

>[!NOTE]
>Du måste ha administratörsbehörigheter på den klientdator som du använder för att ansluta.
>
>

1. Anslut till det virtuella nätverket genom att gå till **VPN-anslutningar** på Azure Portal från klientdatorn och leta upp den VPN-anslutning som du skapade. VPN-anslutningen har samma namn som det virtuella nätverket. Välj **Anslut**. Om ett popup-meddelande om certifikatet visas väljer du **Fortsätt** för att använda utökade privilegier.

2. På statussidan **Anslutning** väljer du **Anslut** för att initiera anslutningen. Om skärmen **Välj certifikat** visas kontrollerar du att klientcertifikatet som visas är korrekt. Om inte väljer du rätt certifikat i listrutan och väljer sedan **OK**.

3. Om anslutningen lyckas visas meddelandet **Ansluten**.


### <a name="troubleshooting-p2s-connections"></a>Felsöka P2S-anslutningar

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verifiera VPN-anslutningen

1. Kontrollera att VPN-anslutningen är aktiv. Öppna en upphöjd kommandotolk på klientdatorn och kör **ipconfig/all**.
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

## <a name="connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Lägga till eller ta bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kan klienter som har ett certifikat som genererats från det rotcertifikatet inte längre autentisera och ansluta. För att dessa klienter ska kunna autentisera och ansluta igen måste du installera ett nytt klientcertifikat som genereras från ett rotcertifikat som är betrott av Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Anvisningar finns i Ladda upp .cer-filen med rotcertifikatet.

### <a name="to-remove-a-trusted-root-certificate"></a>Så här tar du bort ett betrott rotcertifikat

1. Från avsnittet **VPN-anslutningar** på sidan för ditt virtuella nätverk öppnar du sidan **Punkt-till-plats-VPN-anslutning** genom att välja bilden som representerar klienterna.

   ![Klienter](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. På sidan **Punkt-till-plats-VPN-anslutning** väljer du **Hantera certifikat** för att öppna sidan **Certifikat**.

   ![Sidan Certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. På sidan **Certifikat** väljer du knappen med tre punkter bredvid det certifikat som du vill ta bort och väljer sedan **Ta bort**.

   ![Ta bort rotcertifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Om det behövs kan du återkalla ett klientcertifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Den här metoden skiljer sig från hur du tar bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="to-revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
3. Gå till det klassiska virtuella nätverket. Välj **Punkt-till-plats-VPN-anslutning** och välj sedan **Hantera certifikat** för att öppna sidan **Certifikat**.
4. Öppna sidan **Lista över återkallade certifikat** genom att välja **Lista över återkallade certifikat**. 
5. Öppna sidan **Lägg till certifikat i listan över återkallade certifikat** genom att välja **Lägg till certifikat**.
6. I **Tumavtryck** klistrar du in certifikattumavtrycket som en kontinuerlig textrad, utan blanksteg. Slutför genom att välja **OK**.

När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

## <a name="point-to-site-faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Nästa steg

- När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/). 

- Mer information om nätverk och virtuella Linux-datorer finns i [Azure and Linux VM network overview](../virtual-machines/linux/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

- Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
