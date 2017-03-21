---
title: "Ansluta en dator till ett virtuellt Azure-nätverk med hjälp av punkt-till-plats: klassisk portal | Microsoft Docs"
description: "Anslut säkert till ett klassiskt virtuellt Azure-nätverk genom att skapa en VPN-gateway-anslutning från punkt-till-plats med den klassiska portalen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ffd5690853a7344c7122940ad9676d1903ba2d4a
ms.lasthandoff: 03/09/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal-classic"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med den klassiska portalen (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-point-to-site-create.md)
> 
> 

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). En punkt-till-plats-anslutning är användbar när du vill ansluta till ditt virtuella nätverk från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn.

Den här artikeln beskriver steg för steg hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning i den klassiska distributionsmodellen på den klassiska portalen. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.



### <a name="deployment-models-and-methods-for-p2s-connections"></a>Distributionsmodeller och metoder för P2S-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de två distributionsmodellerna och tillgängliga distributionsmetoder för P2S-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Grundläggande arbetsflöde
![Punkt-till-plats-diagram](./media/vpn-gateway-point-to-site-create/p2sclassic.png "punkt-till-plats")

Följande steg visar hur du skapar en säker punkt-till-plats-anslutning till ett virtuellt nätverk. Konfigurationen för en punkt-till-plats-anslutning är uppdelad i fyra delar. Det är viktigt att du följer konfigurationsordningen i vart och ett av dessa avsnitt. Hoppa inte över några steg.

* **Avsnitt 1** – Skapa ett virtuellt nätverk (VNet) och en VPN-gateway.
* **Avsnitt 2** – Skapa certifikaten som används för autentisering och ladda upp dem.
* **Avsnitt 3** – Exportera och installera klientcertifikaten.
* **Avsnitt 4** – Konfigurera VPN-klienten.



## <a name="vnetvpn"></a>Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway

Kontrollera att du har en Azure-prenumeration innan du börjar. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Steg 1: Skapa ett virtuellt nätverk
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com). I de här stegen används den klassiska portalen, inte Azure-portalen. För närvarande kan du inte skapa en P2S-anslutning med hjälp av Azure-portalen.
2. I det nedre vänstra hörnet på skärmen klickar du på **Nytt**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.
3. På sidan **Virtuell nätverksinformation** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
   
   * **Namn**: Ge ett namn till det virtuella nätverket. Till exempel ”VNet1”. Det här är det namn som du refererar till när du distribuerar virtuella datorer till det här virtuella nätverket.
   * **Plats**: Platsen är direkt relaterad till den fysiska platsen (region) där du vill att resurserna (VM) ska finnas. Om du vill att de virtuella datorerna som du distribuerar till det här virtuella nätverket ska placeras fysiskt i östra USA väljer du den platsen. Du kan inte ändra den region som är associerad med det virtuella nätverket när du har skapat det.
4. På sidan **DNS-servrar och VPN-anslutning** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
   
   * **DNS-servrar**: Ange DNS-servernamnet och IP-adressen, eller välj en tidigare registrerad DNS-server i snabbmenyn. Du skapar inte en DNS-server med den här inställningen. Den gör att du kan välja vilka DNS-servrar du vill använda för namnmatchning för det virtuella nätverket. Om du vill använda Azures standardtjänst för namnmatchning lämnar du avsnittet tomt.
   * **Konfigurera punkt-till-plats-VPN**: Markera kryssrutan.
5. På sidan **Punkt-till-plats-anslutning** anger du det IP-adressintervall som dina VPN-klienter ska få sina IP-adresser från när de ansluter. Det finns några regler för adressintervallen som du kan ange. Det är viktigt att du kontrollerar att det intervall som du anger inte överlappar något av de intervall som finns i ditt lokala nätverk.
6. Ange följande information och klicka sedan på pilen Nästa.
   
   * **Adressutrymme**: Ta med inledande IP-adress och CIDR (antal adresser).
   * **Lägg till adressutrymme**: Lägg bara till adressutrymme om det krävs för utformningen av nätverket.
7. På sidan **Adressutrymmen för virtuellt nätverk** anger du det adressintervall som du vill använda i det virtuella nätverket. Detta är de dynamiska IP-adresser (DIPS) som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.<br><br>Det är särskilt viktigt att välja ett intervall som inte överlappar med något av de intervall som används i det lokala nätverket. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.
8. Ange följande information och klicka sedan på bockmarkeringen för att börja skapa det virtuella nätverket.
   
   * **Adressutrymme**: Lägg till det interna IP-adressintervall som du vill använda för det här virtuella nätverket, inklusive inledande IP och antal. Det är viktigt att välja ett intervall som inte överlappar med något intervall som används i det lokala nätverket. 
   * **Lägg till undernät**: Fler undernät krävs inte, men du kanske vill skapa ett separat undernät för virtuella datorer med statiska DIPS. Du kan också vilja ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.
   * **Lägg till gateway-undernät**: Gateway-undernätet måste anges för en punkt-till-plats-VPN. Klicka för att lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen.
9. När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen. När det virtuella nätverket har skapats kan du skapa en gateway för dynamisk routning.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Del 2: Skapa en gateway för dynamisk routning
Gateway-typen måste vara konfigurerad som dynamisk. Gateways för statisk routning fungerar inte med den här funktionen.

1. På sidan **Nätverk** på den klassiska Azure-portalen klickar du på det virtuella nätverk som du skapade. Gå sedan till sidan **Instrumentpanel**.
2. Klicka på **Skapa gateway** längst ned på sidan **Instrumentpanel**. Ett meddelande visas som frågar om du vill **skapa en gateway för det virtuella nätverket ”VNet1”**. Klicka på **Ja** för att börja skapa gatewayen. Det tar upp till 45 minuter att skapa gatewayen.

## <a name="generate"></a>Avsnitt 2 – Skapa och ladda upp certifikat
Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN:er. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil. Du kan sedan överföra offentliga certifikatdata från rotcertifikatet till Azure.

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt certifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

I det här avsnittet ska du göra följande:

* Hämta CER-filen för ett rotcertifikat. Detta kan vara ett självsignerat rotcertifikat eller så kan du använda ditt företagscertifikatsystem.
* Ladda upp CER-filen till Azure.
* Generera klientcertifikat.

### <a name="root"></a>Del 1: Hämta cer.-filen för rotcertifikatet


Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Hämta .cer-filen för det rotcertifikat som du vill använda.


Om du inte använder en företagscertifikatlösning måste du generera ett självsignerat rotcertifikat. Du kan använda PowerShell om du vill skapa ett självsignerat rotcertifikat som innehåller de fält som behövs för P2S-autentisering. Avsnittet [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat rotcertifikat för punkt-till-plats-anslutningar med PowerShell) vägleder dig genom stegen för att skapa ett självsignerat rotcertifikat.

> [!NOTE]
> Tidigare var makecert den metod som rekommenderades för att skapa självsignerade rotcertifikat och generera klientcertifikat för punkt-till-plats-anslutningar. Du kan nu använda PowerShell för att skapa dessa certifikat. En fördel med att använda PowerShell är möjligheten att skapa SHA-2-certifikat. I [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Skapa ett självsignerat rotcertifikat för punkt-till-plats-anslutningar med PowerShell) finns information om nödvändiga värden.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Så här exporterar du den offentliga nyckeln för ett självsignerat rotcertifikat.

Punkt-till-plats-anslutningar kräver att den offentliga nyckeln (.cer) överförs till Azure. Följande steg hjälper dig att exportera .cer-filen för det självsignerade rotcertifikatet.

1. Om du vill hämta en .cer-fil från certifikatet kan du öppna **certmgr.msc**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas.
2. Klicka på **Nästa** i guiden. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.
3. På sidan **Filformat för export** väljer du **Base&64;-kodad X.509 (. CER).** och klickar sedan på **Nästa**. 
4. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
5. Klicka på **Slutför** för att exportera certifikatet. Meddelandet **Exporten lyckades**. Stäng guiden genom att klicka på **OK**.

### <a name="upload"></a>Del 2: Ladda upp certifikatets .cer-fil till den klassiska Azure-portalen

Lägg till ett betrott certifikat i Azure. När du lägger till en Base64-kodad X.509-fil (.cer) till Azure uppmanar du Azure att lita på rotcertifikatet som filen representerar. Du kan överföra filer för upp till 20 rotcertifikat. Du överför inte den privata nyckeln för rotcertifikatet till Azure. När .cer-filen har överförts använder Azure den för att autentisera klienter som ansluter till det virtuella nätverket.

1. På sidan **Certifikat** i den klassiska Azure-portalen för det virtuella nätverket, klickar du på **Överför ett rotcertifikat**.
2. På sidan **Överför certifikat** bläddrar du efter .cer-rotcertifikatet och klickar sedan på bockmarkeringen.

### <a name="createclientcert"></a>Del 3: Generera ett klientcertifikat
Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla istället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

####<a name="enterprise-certificate"></a>Företagscertifikat
- Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
- Se till att det klientcertifikat som du utfärdar baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

####<a name="self-signed-root-root-certificate"></a>Självsignerat rotcertifikat 
Om du använder ett självsignerat rotcertifikat kan du läsa [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Generera ett klientcertifikat med PowerShell) för att få anvisningar om att generera ett klientcertifikat som är kompatibelt med punkt-till-plats-anslutningar.


## <a name="installclientcert"></a>Avsnitt 3 – Exportera och installera klientcertifikatet

När du har genererat ett klientcertifikat måste du exportera certifikatet som en .pfx-fil och sedan installera det på klientdatorn. Varje klientdator måste ha ett klientcertifikat för autentisering. Du kan automatisera installationen av klientcertifikatet eller installera certifikatet manuellt. Följande steg beskriver hur du exporterar och installerar klientcertifikatet manuellt.

### <a name="export-the-client-certificate"></a>Exportera klientcertifikat

1. Du kan exportera ett klientcertifikat genom att öppna **certmgr.msc**. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**. **Guiden Exportera certifikat** öppnas.
2. I guiden klickar du på **Nästa** och väljer **Ja, exportera den privata nyckeln**. Klicka sedan på **Nästa**.
3. På sidan **Filformat för export** kan du låta standardalternativen vara markerade. Klicka sedan på **Nästa**. 
4. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.
5. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.
6. Klicka på **Slutför** för att exportera certifikatet.

### <a name="install-the-client-certificate"></a>Installera klientcertifikatet

När du installerar klientcertifikatet, behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Leta upp och kopiera *.pfx*-filen till klientdatorn. Installera *.pfx*-filen genom att dubbelklicka på den på klientdatorn. Låt **Lagringsplats** stå som **Aktuell användare** och klicka sedan på **Nästa**.
2. Gör inga ändringar på sidan för den **Fil** som ska importeras. Klicka på **Nästa**.
3. På sidan **Skydd av privat nyckel** anger du lösenordet för certifikatet, om du har använt ett sådant, eller bekräftar att säkerhetsobjektet som installerar certifikatet är korrekt, och klickar sedan på **Nästa**.
4. På sidan **Certifikatarkiv** låter du standardplatsen stå kvar och klickar sedan på **Nästa**.
5. Klicka på **Slutför**. På sidan **Säkerhetsvarning** för certifikatinstallationen klickar du på **Ja**. Eftersom du har genererat certifikatet kan du klicka på ”Ja” utan att oroa dig. Certifikatet har nu importerats.

## <a name="vpnclientconfig"></a>Avsnitt 4 – Konfigurera din VPN-klient
För att ansluta till det virtuella nätverket måste du också konfigurera en VPN-klient. Klienten kräver både ett klientcertifikat och rätt konfigurationspaket för VPN-klienten för att kunna ansluta. Utför följande steg i rätt ordning för att konfigurera VPN-klienten.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Del 1: Skapa ett konfigurationspaket för VPN-klienten
1. På den klassiska Azure-portalen går du till snabbmenyn i det högra hörnet på sidan **Instrumentpanel** för det virtuella nätverket. VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara. Inställningarna är specifika för det virtuella nätverk som du vill ansluta till. En lista över klientoperativsystem som stöds finns i avsnittet [Vanliga frågor och svar om punkt-till-plats](#faq) i slutet av den här artikeln.<br><br>Välj det paket som motsvarar klientoperativsystemet som det kommer att installeras i:
   
   * För 32-bitars klienter, välj **Hämta 32-bitars VPN-klientpaketet**.
   * För 64-bitars klienter, välj **Hämta 64-bitars VPN-klientpaketet**.
2. Det tar några minuter att skapa klientpaketet. När paketet har skapats kan du ladda ned filen. Den *.exe*-fil som du laddar ned kan lagras på ett säkert sätt i den lokala datorn.
3. När du skapar och hämtar VPN-klientpaketet från den klassiska Azure-portalen, kan du installera klientpaketet på den klientdator som du vill ansluta till ditt virtuella nätverk. Om du planerar att installera VPN-klientpaketet på flera klientdatorer, bör du kontrollera att de också har ett installerat klientcertifikat.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Del 2: Installera VPN-konfigurationspaketet på klienten
1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket och dubbelklicka på EXE-filen. 
2. När paketet har installerats kan du starta VPN-anslutningen. Konfigurationspaketet är inte signerat av Microsoft. Om du vill kan du signera paketet med hjälp av organisationens signeringstjänst, eller göra det själv med hjälp av [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Du kan även använda paketet utan signering. Dock visas en varning när du installerar paketet om det inte är signerat.
3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär så här: 
   
    ![VPN-klient](./media/vpn-gateway-point-to-site-create/vpn.png "VPN-klient")

### <a name="part-3-connect-to-azure"></a>Del 3: Ansluta till Azure
1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 
2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
   
    ![VPN-klient 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN-klientanslutning")
3. Nu bör anslutningen upprättas.
   
    ![VPN-klient 3](./media/vpn-gateway-point-to-site-create/connected.png "VPN-klientanslutning 2")

> [!NOTE]
> Om du använder ett certifikat som har utfärdats med en certifikatutfärdarlösning för företag och har problem med autentiseringen kontrollerar du autentiseringsordningen på klientcertifikatet. Du kan kontrollera listan med autentiseringsordningen genom att dubbelklicka på klientcertifikatet och gå till **Information > Förbättrad nyckelanvändning**. Kontrollera att ”Klientautentisering” är den första posten i listan. Annars måste du utfärda ett klientcertifikat baserat på mallen Användare där Klientautentisering är den första posten i listan. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Del 4: Verifiera VPN-anslutningen
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

## <a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).


