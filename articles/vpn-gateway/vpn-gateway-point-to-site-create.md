<properties
   pageTitle="Konfigurera en VPN-anslutning från punkt till plats till ett virtuellt Azure-nätverk med hjälp av den klassiska portalen | Microsoft Azure"
   description="Anslut säkert till ditt virtuella Azure-nätverk genom att skapa en VPN-anslutning från punkt till plats. Anvisningar för VNets som har skapats med distributionsmodellen Service Management (klassisk)."
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
   ms.date="03/30/2016"
   ms.author="cherylmc"/>

# Konfigurera en VPN-anslutning från punkt till plats till ett VNet med hjälp av den klassiska portalen

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal – Klassisk](vpn-gateway-point-to-site-create.md)

Med en punkt-till-plats-konfiguration kan du skapa en säker anslutning till ditt virtuella nätverk från en klientdator separat. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Punkt-till-plats är en utmärkt lösning när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. Mer information om punkt-till-plats-anslutningar finns i [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) och [Om anslutningar mellan flera platser](vpn-gateway-cross-premises-options.md).).

Den här artikeln visar VPN Gateway-anslutningar från punkt till plats till ett virtuellt nätverk som har skapats med den **klassiska distributionsmodellen** (Service Management) och den klassiska portalen. När vi har anvisningar för Azure-portalen kommer vi att länka till den artikeln från den här sidan.

**Distributionsmodeller och verktyg för punkt-till-plats-anslutningar**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Punkt-till-plats-diagram](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")



## Om att skapa en punkt-till-plats-anslutning
 
Följande steg visar hur du skapar en säker punkt-till-plats-anslutning till ett virtuellt nätverk. Även om att konfigurera en punkt-till-plats-anslutning kräver flera steg, är det bra att ha en säker anslutning från din dator till det virtuella nätverket utan att behöva köpa och konfigurera en VPN-enhet. 

Konfigurationen för en punkt-till-plats-anslutning är uppdelad i 4 delar. Det är viktigt att du konfigurerar dem i rätt ordning, så hoppa inte över något steg.


- **Avsnitt 1** visar dig hur du skapar ett virtuellt nätverk och en VPN-gateway.
- **Avsnitt 2** hjälper dig att skapa de certifikat som används för autentisering och överföra dem.
- **Avsnitt 3** visar dig hur du exporterar och installerar dina klientcertifikat.
- **Avsnitt 4** visar dig hur du konfigurerar din VPN-klient.

## Avsnitt 1 – Skapa ett virtuellt nätverk och en VPN-gateway


### Steg 1: Skapa ett virtuellt nätverk

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/). Observera att de här stegen använder den klassiska portalen, inte Azure-portalen.

2. I det nedre vänstra hörnet på skärmen klickar du på **Nytt**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.

3. På sidan **Virtuell nätverksinformation** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
    - **Namn**: Ge ett namn till det virtuella nätverket. Till exempel ”VNetEast”. Det här är det namn som du ska referera till när du distribuerar virtuella datorer och PaaS-instanser till detta VNet.
    - **Plats**: Platsen är direkt relaterad till den fysiska platsen (region) där du vill att resurserna (VM) ska finnas. Om du vill att de virtuella datorerna som du distribuerar till det här virtuella nätverket ska placeras fysiskt i östra USA väljer du den platsen. Du kan inte ändra den region som är associerad med det virtuella nätverket när du har skapat det.

4. På sidan **DNS-servrar och VPN-anslutning** anger du följande information och klickar sedan på nästa-pilen längst ned till höger.
    - **DNS-servrar**: Ange DNS-servernamnet och IP-adressen, eller välj en tidigare registrerad DNS-server i snabbmenyn. Den här inställningen skapar inte någon DNS-server, utan låter dig ange vilka DNS-servrar du vill använda vid namnmatchning i det här virtuella nätverket. Om du vill använda Azures standardtjänst för namnmatchning lämnar du avsnittet tomt.
    - **Konfigurera punkt-till-plats-VPN**: Markera kryssrutan.

5. På sidan **Punkt-till-plats-anslutning** anger du det IP-adressintervall som dina VPN-klienter får en IP-adress från när de ansluter. Det finns några regler för adressintervallen som du kan ange. Det är viktigt att kontrollera att det intervall som du anger inte överlappar något av de intervall som finns i ditt lokala nätverk.

6. Ange följande information och klicka sedan på pilen Nästa.
 - **Adressutrymme**: Ta med inledande IP-adress och CIDR (antal adresser).
 - **Lägg till adressutrymme**: Lägg bara till adressutrymme om det krävs för utformningen av nätverket.

7. På sidan **Adressutrymmen för virtuellt nätverk** anger du det adressintervall som du vill använda i det virtuella nätverket. Detta är de dynamiska IP-adresser (DIPS) som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket. Det är särskilt viktigt att välja ett intervall som inte överlappar med något av de intervall som används i det lokala nätverket. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.

8. Ange följande information och klicka sedan på bockmarkeringen för att börja skapa det virtuella nätverket.
 - **Adressutrymme**: Lägg till det interna IP-adressintervall som du vill använda för det här virtuella nätverket, inklusive inledande IP och antal. Det är viktigt att välja ett intervall som inte överlappar med något intervall som används i det lokala nätverket. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.
 - **Lägg till undernät**: Fler undernät krävs inte, men du kanske vill skapa ett separat undernät för virtuella datorer med statiska DIPS. Du kan också vilja ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.
 - **Lägg till gateway-undernät**: Gateway-undernätet måste anges för en punkt-till-plats-VPN. Klicka för att lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen.

9. När du har skapat ditt virtuella nätverk visas **Skapad** under **Status** på nätverkssidan i den klassiska Azure-portalen. När det virtuella nätverket har skapats kan du skapa en gateway för dynamisk routning.

### Del 2: Skapa en gateway för dynamisk routning

Gateway-typen måste vara konfigurerad som dynamisk. Gateways för statisk routning fungerar inte med den här funktionen.

1. På sidan **Nätverk** i den klassiska Azure-portalen klickar du på det virtuella nätverk som du nyss skapade. Gå sedan till sidan **Instrumentpanel**.

2. Klicka på **Skapa gateway** längst ned på sidan **Instrumentpanel**. Ett meddelande visas med frågan **Vill du skapa en gateway för virtuellt nätverk ”yournetwork”?**. Klicka på **Ja** för att börja skapa gatewayen. Det tar ungefär 15 minuter att skapa gatewayen.

## Avsnitt 2 – Skapa och överföra certifikat

Certifikat används för att autentisera VPN-klienter för punkt-till-plats-VPN. Du kan använda certifikat som genereras av en företagscertifikatlösning, samt självsignerade certifikat. Du kan överföra upp till 20 rotcertifikat till Azure.

Om du vill använda ett självsignerat certifikat hjälper stegen nedan dig genom processen. Om du planerar att använda en företagscertifikatlösning är stegen i varje avsnitt olika, men du måste fortfarande utföra följande steg:

### Del 1: Identifiera eller generera ett rotcertifikat

Om du inte använder en företagscertifikatlösning, måste du generera ett självsignerat rotcertifikat. Stegen i det här avsnittet har skrivits för Windows 8. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md).

Du kan t.ex. skapa ett X.509-certifikat genom att använda Certificate Creation-verktyget (makecert.exe). Om du vill använda makecert laddar du ned och installerar [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), vilket är kostnadsfritt.

1. Gå till mappen Visual Studio Tools och starta en kommandotolk som administratör.

2. Kommandot i följande exempel skapar och installerar ett rotcertifikat i det personliga certifikatarkivet på datorn. Det skapar också en motsvarande *.cer*-fil som du senare ska överföra till den klassiska Azure-portalen.

3. Ändra till den katalog där du vill att .cer-filen ska finnas och kör följande kommando, där *RootCertificateName* är det namn som du vill använda för certifikatet. Om du kör följande exempel utan ändringar, blir resultatet ett rotcertifikat och den motsvarande filen *RootCertificateName.cer*.

>[AZURE.NOTE] Eftersom du har skapat ett rotcertifikat som genererar klientcertifikat, kan du vilja exportera certifikatet tillsammans med sin privata nyckel och spara det på en säker plats där det kan återställas.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Del 2: Överför rotcertifikatets .cer-fil till den klassiska Azure-portalen

Du måste överföra motsvarande .cer-fil för varje rotcertifikat till Azure. Du kan överföra upp till 20 certifikat.

1. När du genererade ett rotcertifikat tidigare skapade du även en *.cer*-fil. Du ska nu överföra den filen till den klassiska Azure-portalen. Observera att .cer-filen inte innehåller den privata nyckeln för rotcertifikatet. Du kan överföra upp till 20 rotcertifikat.

2. På sidan **Certifikat** i den klassiska Azure-portalen för det virtuella nätverket, klickar du på **Överför ett rotcertifikat**.

3. På sidan **Överför certifikat** bläddrar du efter .cer-rotcertifikatet och klickar sedan på bockmarkeringen.

### Del 3: Generera ett klientcertifikat

Stegen nedan visar hur du genererar ett klientcertifikat från det självsignerade rotcertifikatet. Stegen i det här avsnittet har skrivits för Windows 8. Om du har Windows 10 kan du läsa mer i [Arbeta med självsignerade rotcertifikat för punkt-till-plats-konfigurationer](vpn-gateway-certificates-point-to-site.md). Om du använder en företagscertifikatlösning följer du riktlinjerna för lösningen som du använder. 

1. Använd samma dator som du använde när du skapade det självsignerade rotcertifikatet och öppna ett kommandotolksfönster i Visual Studio som administratör.

2. Ändra katalogen till den plats där du vill spara klientcertifikatfilen. *RootCertificateName* refererar till det självsignerade rotcertifikat som du skapade. Om du kör följande exempel blir (ändra RootCertificateName till namnet på din rotcertifikat) blir resultatet ett klientcertifikat med namnet ”ClientCertificateName” i ditt personliga certifikatarkiv.

3. Skriv följande kommando:

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Alla certifikat lagras i ditt personliga certifikatarkiv på datorn. Kryssa i *certmgr* för att verifiera. Du kan använda proceduren till att skapa så många klientcertifikat som du vill. Vi rekommenderar att du skapar unika klientcertifikat för varje dator som du vill ansluta till det virtuella nätverket.

## Avsnitt 3 – Exportera och installera klientcertifikatet

Att installera ett klientcertifikat på varje dator som du vill ansluta till det virtuella nätverket är ett obligatoriskt steg. Stegen nedan hjälper dig att installera klientcertifikatet manuellt.

1. Ett klientcertifikat måste installeras på varje dator som du vill ansluta till det virtuella nätverket. Detta innebär förmodligen att du skapar flera klientcertifikat som du sedan måste exportera. Exportera klientcertifikaten med hjälp av *certmgr.msc*. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter** och sedan på **exportera**.
2. Exportera *klientcertifikatet* med den privata nyckeln. Det är en *.pfx*-fil. Var noga med att skriva ner eller komma ihåg lösenordet (nyckeln) som du anger för det här certifikatet.
3. Kopiera *.pfx*-filen till klientdatorn. På klientdatorn dubbelklickar du på *.pfx*-filen för att installera den. Ange lösenordet när det efterfrågas. Ändra inte installationsplatsen.

## Avsnitt 4 – Konfigurera din VPN-klient

Du måste också konfigurera VPN-klienten för att kunna ansluta till det virtuella nätverket. Klienten kräver både ett klientcertifikat och rätt VPN-klientkonfiguration för att kunna ansluta. Utför följande steg i rätt ordning för att konfigurera VPN-klienten.

### Del 1: Skapa ett konfigurationspaket för VPN-klienten

1. På sidan **Instrumentpanel** i den klassiska Azure-portalen för ditt virtuella nätverk, går du till snabböversiktsmenyn i höger hörn och klickar på det VPN-paket som tillhör den klient som du vill ansluta till ditt virtuella nätverk.

2. 
Följande klientoperativsystem stöds:
 - Windows 7 (32-bitars och 64-bitars)
 - Windows Server 2008 R2 (endast 64-bitars)
 - Windows 8 (32-bitars och 64-bitars)
 - Windows 8.1 (32-bitars och 64-bitars)
 - Windows Server 2012 (endast 64-bitars)
 - Windows Server 2012 R2 (endast 64-bitars)
 - Windows 10

3. Välj det paket som motsvarar klientoperativsystemet som det kommer att installeras i:
 - För 32-bitars klienter, välj **Hämta 32-bitars VPN-klientpaketet**.
 - För 64-bitars klienter, välj **Hämta 64-bitars VPN-klientpaketet**.

4. Det kan ta några minuter att skapa klientpaketet. När paketet har slutförts kommer du att kunna hämta filen. Den *.exe*-fil som du laddar ned kan lagras på ett säkert sätt i den lokala datorn.

5. När du skapar och hämtar VPN-klientpaketet från den klassiska Azure-portalen, kan du installera klientpaketet på den klientdator som du vill ansluta till ditt virtuella nätverk. Om du planerar att installera VPN-klientpaketet på flera klientdatorer, bör du kontrollera att de också har ett installerat klientcertifikat. VPN-klientpaketet innehåller konfigurationsinformation för att konfigurera VPN-klientprogrammet som är inbyggt i Windows. Paketet installerar inte någon ytterligare programvara.

### Del 2: Installera VPN-konfigurationspaketet på klienten och starta anslutningen

1. Kopiera konfigurationsfilen lokalt till den dator som du vill ansluta till det virtuella nätverket och dubbelklicka på .exe-filen. När paketet har installerats kan du starta VPN-anslutningen.
Observera att konfigurationspaketet inte är signerat av Microsoft. Om du vill kan du signera paketet med hjälp av organisationens signeringstjänst, eller göra det själv med hjälp av [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Du kan även använda paketet utan signering. Dock visas en varning när du installerar paketet om att det inte är signerat.
2. Gå till VPN-anslutningar på klientdatorn och leta upp VPN-anslutningen som du nyss skapade. Den får samma namn som det virtuella nätverket. Klicka på **Anslut**.
3. Ett popup-meddelande visas som används för att skapa ett självsignerat certifikat för gateway-slutpunkten. Klicka på **Fortsätt** för att använda utökade privilegier.
4. På statussidan **Anslutning** klickar du på **Anslut** för att starta anslutningen.
5. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om det inte är det använder du pilen i listrutan för att välja rätt certifikat. Klicka sedan på **OK**.
6. Du är nu ansluten till det virtuella nätverket och har full åtkomst till tjänsten och den virtuella dator som finns i det virtuella nätverket.

### Del 3: Verifiera VPN-anslutningen

1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att den IP-adress som du har fått är en av adresserna inom adressintervallet för punkt-till-plats-anslutningen som du angav när du skapade ditt VNet. Resultaten bör likna följande:

Exempel:



    PPP adapter VNetEast:
        Connection-specific DNS Suffix .:
        Description.....................: VNetEast
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



<!--HONumber=jun16_HO2-->


