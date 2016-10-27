<properties
   pageTitle="Konfigurera en VPN-gatewayanslutning från punkt till plats till Azure Virtual Network med hjälp av den klassiska portalen | Microsoft Azure"
   description="Anslut säkert till Azure Virtual Network genom att skapa en VPN-gatewayanslutning från punkt till plats."
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


# Konfigurera en punkt-till-plats-anslutning till ett VNet med hjälp av den klassiska portalen

> [AZURE.SELECTOR]
- [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Klassisk – den klassiska portalen](vpn-gateway-point-to-site-create.md)

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. En P2S-anslutning är användbar när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk.

Den här artikeln beskriver steg för steg hur du skapar ett VNet med en punkt-till-plats-anslutning med **den klassiska distributionsmodellen** på den **klassiska portalen**.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Planering och design](vpn-gateway-plan-design.md).


### Distributionsmodeller och metoder för P2S-anslutningar

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Punkt-till-plats-diagram](./media/vpn-gateway-point-to-site-create/p2sclassic.png "point-to-site")

## Om att skapa en punkt-till-plats-anslutning
 
Följande steg visar hur du skapar en säker punkt-till-plats-anslutning till ett virtuellt nätverk. 

Konfigurationen för en punkt-till-plats-anslutning är uppdelad i fyra delar. Det är viktigt att du följer konfigurationsordningen i vart och ett av dessa avsnitt. Hoppa inte över några steg.

- **Avsnitt 1** – Skapa ett virtuellt nätverk (VNet) och en VPN-gateway.
- **Avsnitt 2** – Skapa certifikaten som används för autentisering och ladda upp dem.
- **Avsnitt 3** – Exportera och installera klientcertifikaten.
- **Avsnitt 4** – Konfigurera VPN-klienten.

## <a name="vnetvpn"></a>Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway

### Steg 1: Skapa ett virtuellt nätverk

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/). I de här stegen används den klassiska portalen, inte Azure-portalen. För närvarande kan du inte skapa en P2S-anslutning med hjälp av Azure-portalen.

2. I det nedre vänstra hörnet på skärmen klickar du på **Nytt**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.

3. På sidan **Virtuell nätverksinformation** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
    - **Namn**: Ge ett namn till det virtuella nätverket. Till exempel ”VNet1”. Det här är det namn som du refererar till när du distribuerar virtuella datorer till det här virtuella nätverket.
    - **Plats**: Platsen är direkt relaterad till den fysiska platsen (region) där du vill att resurserna (VM) ska finnas. Om du vill att de virtuella datorerna som du distribuerar till det här virtuella nätverket ska placeras fysiskt i östra USA väljer du den platsen. Du kan inte ändra den region som är associerad med det virtuella nätverket när du har skapat det.

4. På sidan **DNS-servrar och VPN-anslutning** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
    - **DNS-servrar**: Ange DNS-servernamnet och IP-adressen, eller välj en tidigare registrerad DNS-server i snabbmenyn. Du skapar inte en DNS-server med den här inställningen. Den gör att du kan välja vilka DNS-servrar du vill använda för namnmatchning för det virtuella nätverket. Om du vill använda Azures standardtjänst för namnmatchning lämnar du avsnittet tomt.
    - **Konfigurera punkt-till-plats-VPN**: Markera kryssrutan.

5. På sidan **Punkt-till-plats-anslutning** anger du det IP-adressintervall som dina VPN-klienter ska få sina IP-adresser från när de ansluter. Det finns några regler för adressintervallen som du kan ange. Det är viktigt att du kontrollerar att det intervall som du anger inte överlappar något av de intervall som finns i ditt lokala nätverk.

6. Ange följande information och klicka sedan på pilen Nästa.
 - **Adressutrymme**: Ta med inledande IP-adress och CIDR (antal adresser).
 - **Lägg till adressutrymme**: Lägg bara till adressutrymme om det krävs för utformningen av nätverket.

7. På sidan **Adressutrymmen för virtuellt nätverk** anger du det adressintervall som du vill använda i det virtuella nätverket. Detta är de dynamiska IP-adresser (DIPS) som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.<br><br>Det är särskilt viktigt att välja ett intervall som inte överlappar med något av de intervall som används i det lokala nätverket. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.

8. Ange följande information och klicka sedan på bockmarkeringen för att börja skapa det virtuella nätverket.
 - **Adressutrymme**: Lägg till det interna IP-adressintervall som du vill använda för det här virtuella nätverket, inklusive inledande IP och antal. Det är viktigt att välja ett intervall som inte överlappar med något intervall som används i det lokala nätverket. 
 - **Lägg till undernät**: Fler undernät krävs inte, men du kanske vill skapa ett separat undernät för virtuella datorer med statiska DIPS. Du kan också vilja ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.
 - **Lägg till gateway-undernät**: Gateway-undernätet måste anges för en punkt-till-plats-VPN. Klicka för att lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen.

9. När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen. När det virtuella nätverket har skapats kan du skapa en gateway för dynamisk routning.

### Del 2: Skapa en gateway för dynamisk routning

Gateway-typen måste vara konfigurerad som dynamisk. Gateways för statisk routning fungerar inte med den här funktionen.

1. På sidan **Nätverk** på den klassiska Azure-portalen klickar du på det virtuella nätverk som du skapade. Gå sedan till sidan **Instrumentpanel**.

2. Klicka på **Skapa gateway** längst ned på sidan **Instrumentpanel**. Ett meddelande visas som frågar om du vill **skapa en gateway för det virtuella nätverket ”VNet1”**. Klicka på **Ja** för att börja skapa gatewayen. Det tar ungefär 15 minuter att skapa gatewayen.

## <a name="generate"></a>Avsnitt 2 – Skapa och överföra certifikat

Certifikat används för att autentisera VPN-klienter för punkt-till-plats-VPN. Du kan använda ett rotcertifikat som genererats av en företagscertifikatlösning eller använda ett självsignerat certifikat. Du kan överföra upp till 20 rotcertifikat till Azure. När CER-filen har överförts kan Azure använda informationen som den innehåller för att autentisera klienter som har ett installerat klientcertifikat. Klientcertifikatet måste genereras från samma certifikat som representeras av CER-filen.

I det här avsnittet ska du göra följande:

- Hämta CER-filen för ett rotcertifikat. Detta kan vara ett självsignerat certifikat eller så kan du använda ditt företagscertifikatsystem.
- Ladda upp CER-filen till Azure.
- Generera klientcertifikat.

### <a name="root"></a>Del 1: Hämta CER-filen för rotcertifikatet

Om du använder ett företagscertifikatsystem hämtar du CER-filen för det rotcertifikat som du vill använda. I [Del 3](#createclientcert) ska du generera klientcertifikaten från rotcertifikatet.

Om du inte använder en företagscertifikatlösning, måste du generera ett självsignerat rotcertifikat. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md). Artikeln beskriver steg för steg hur du använder makecert för att generera ett självsignerat certifikat och exportera CER-filen.

### <a name="upload"></a>Del 2: Överför rotcertifikatets .cer-fil till den klassiska Azure-portalen

Lägg till ett betrott certifikat i Azure. När du lägger till en Base64-kodad X.509-fil (.cer) till Azure uppmanar du Azure att lita på rotcertifikatet som filen representerar.

1. På sidan **Certifikat** i den klassiska Azure-portalen för det virtuella nätverket, klickar du på **Överför ett rotcertifikat**.

2. På sidan **Överför certifikat** bläddrar du efter .cer-rotcertifikatet och klickar sedan på bockmarkeringen.

### <a name="createclientcert"></a>Del 3: Generera ett klientcertifikat

Nu ska du generera klientcertifikaten. Du kan antingen generera ett unikt certifikat för varje klient som ska ansluta eller använda samma certifikat på flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat om det behövs. Om alla i stället använder samma klientcertifikat och du upptäcker att du behöver återkalla certifikatet för en klient så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

- Om du använder en företagscertifikatlösning genererar du ett klientcertifikat med det vanliga namnvärdeformatet 'namn@dindomän.com' i stället för med NetBIOS-formatet 'DOMÄN\användarnamn'. 

- Information om hur du genererar ett klientcertifikat om du använder ett självsignerat certifikat finns i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

## <a name="installclientcert"></a>Avsnitt 3 – Exportera och installera klientcertifikatet

Installera ett klientcertifikat på varje dator som du vill ansluta till det virtuella nätverket. Ett klientcertifikat krävs för autentisering. Du kan automatisera installationen av klientcertifikatet eller installera manuellt. Följande steg beskriver hur du exporterar och installerar klientcertifikatet manuellt.

1. Du kan exportera ett klientcertifikat med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera klientcertifikatet med den privata nyckeln. Det här är en *PFX*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.
3. Kopiera *.pfx*-filen till klientdatorn. Installera *PFX*-filen genom att dubbelklicka på den på klientdatorn. Ange lösenordet när det efterfrågas. Ändra inte installationsplatsen.

## <a name="vpnclientconfig"></a>Avsnitt 4 – Konfigurera din VPN-klient

För att ansluta till det virtuella nätverket måste du också konfigurera en VPN-klient. Klienten kräver både ett klientcertifikat och rätt VPN-klientkonfiguration för att kunna ansluta. Utför följande steg i rätt ordning för att konfigurera VPN-klienten.

### Del 1: Skapa ett konfigurationspaket för VPN-klienten

1. På den klassiska Azure-portalen går du till snabbmenyn i det högra hörnet på sidan **Instrumentpanel** för det virtuella nätverket. En lista över klientoperativsystem som stöds finns i avsnittet [Punkt-till-plats-anslutningar](vpn-gateway-vpn-faq.md#point-to-site-connections) i Vanliga frågor och svar om VPN Gateway. VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara. Inställningarna är specifika för det virtuella nätverk som du vill ansluta till.<br><br>Välj det paket som motsvarar klientoperativsystemet som det kommer att installeras i:
 - För 32-bitars klienter, välj **Hämta 32-bitars VPN-klientpaketet**.
 - För 64-bitars klienter, välj **Hämta 64-bitars VPN-klientpaketet**.

2. Det tar några minuter att skapa klientpaketet. När paketet har skapats kan du ladda ned filen. Den *.exe*-fil som du laddar ned kan lagras på ett säkert sätt i den lokala datorn.

3. När du skapar och hämtar VPN-klientpaketet från den klassiska Azure-portalen, kan du installera klientpaketet på den klientdator som du vill ansluta till ditt virtuella nätverk. Om du planerar att installera VPN-klientpaketet på flera klientdatorer, bör du kontrollera att de också har ett installerat klientcertifikat.

### Del 2: Installera VPN-konfigurationspaketet på klienten

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket och dubbelklicka på EXE-filen. 

2. När paketet har installerats kan du starta VPN-anslutningen. Konfigurationspaketet är inte signerat av Microsoft. Om du vill kan du signera paketet med hjälp av organisationens signeringstjänst, eller göra det själv med hjälp av [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Du kan även använda paketet utan signering. Dock visas en varning när du installerar paketet om det inte är signerat.

3. På klientdatorn går du till **Nätverksinställningar** och klickar på **VPN**. Anslutningen visas i listan. Namnet på det virtuella nätverket som anslutningen kommer att upprättas till visas och ser ut ungefär så här: 

    ![VPN-klient](./media/vpn-gateway-point-to-site-create/vpn.png "VPN client")


### Del 3: Ansluta till Azure

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Klicka på **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. I så fall klickar du på **Fortsätt** för att använda utökade privilegier. 

2. På statussidan **Anslutning** klickar du på **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.

    ![VPN-klient 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN client connection")

3. Nu bör anslutningen upprättas.

    ![VPN-klient 3](./media/vpn-gateway-point-to-site-create/connected.png "VPN client connection 2")

### Del 4: Verifiera VPN-anslutningen

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

Mer information om virtuella nätverk finns på sidan [Dokumentation för virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/).



<!--HONumber=Oct16_HO3-->


