---
title: Nätverksöverväganden
description: Lär dig mer om ASE-nätverkstrafiken och hur du ställer in nätverkssäkerhetsgrupper och användardefinierade vägar med din ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4aec7fa78292f224952dd2ae929d2b8bfd97ab9b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477686"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Nätverksöverväganden för en App Service-miljö #

## <a name="overview"></a>Översikt ##

 Azure [App Service Environment][Intro] är en distribution av Azure App Service till ett undernät i ditt virtuella Azure-nätverk (VNet). Det finns två distributionstyper för en App Service-miljö (ASE):

- **Extern ASE:** Exponerar de ASE-värdbaserade apparna på en IP-adress som är tillgänglig på Internet. Mer information finns i [Skapa en extern ASE][MakeExternalASE].
- **ILB ASE**: Exponerar de ASE-värdbaserade apparna på en IP-adress i ditt virtuella nätverk. Den interna slutpunkten är en intern belastningsutjämnare (ILB), vilket är anledningen till att den kallas en ILB ASE. Mer information finns i [Skapa och använda en ILB ASE][MakeILBASE].

Alla ASEs, externa och ILB, har en offentlig VIP som används för inkommande ledning trafik och som från-adress när du ringer samtal från ASE till Internet. Samtalen från en ASE som går till internet lämnar VNet via VIP tilldelats för ASE. Den offentliga IP av denna VIP är källan IP för alla samtal från ASE som går till Internet. Om apparna i DIN ASE ringer till resurser i ditt virtuella nätverk eller via ett VPN är käll-IP en av IP-adresserna i undernätet som används av din ASE. Eftersom ASE finns i det virtuella nätverket kan den också komma åt resurser inom det virtuella nätverket utan någon ytterligare konfiguration. Om det virtuella nätverket är anslutet till ditt lokala nätverk har appar i DITT ASE också åtkomst till resurser där utan ytterligare konfiguration.

![Extern ASE][1] 

Om du har en extern ASE är den offentliga VIP:en också den slutpunkt som dina ASE-appar löser för:

* HTTP/S 
* FTP/S
* Webbdistribution
* Fjärrfelsökning

![ILB ASE][2]

Om du har en ILB ASE är adressen till ILB-adressen slutpunkten för HTTP/S, FTP/S, webbdistribution och fjärrfelsökning.

## <a name="ase-subnet-size"></a>ASE-undernätsstorlek ##

Storleken på det undernät som används för att vara värd för ett ASE kan inte ändras när ASE har distribuerats.  ASE använder en adress för varje infrastrukturroll samt för varje instans av en isolerad apptjänstplan.  Dessutom finns det fem adresser som används av Azure Networking för varje undernät som skapas.  En ASE utan App Service-planer alls kommer att använda 12 adresser innan du skapar en app.  Om det är en ILB ASE, då det kommer att använda 13 adresser innan du skapar en app i den ASE. När du skalar ut din ASE läggs infrastrukturroller till var multipel av 15 och 20 i apptjänstplaninstanserna.

   > [!NOTE]
   > Inget annat kan vara i undernätet än ASE. Var noga med att välja ett adressutrymme som möjliggör framtida tillväxt. Du kan inte ändra den här inställningen senare. Vi rekommenderar en `/24` storlek på med 256 adresser.

När du skalar upp eller ned läggs nya roller av lämplig storlek till och sedan migreras dina arbetsbelastningar från den aktuella storleken till målstorleken. De ursprungliga virtuella datorerna tas bort först efter att arbetsbelastningarna har migrerats. Om du hade en ASE med 100 ASP-instanser skulle det finnas en period där du behöver fördubbla antalet virtuella datorer.  Det är av denna anledning som vi rekommenderar användning av en "/24" för att tillgodose eventuella ändringar du kan behöva.  

## <a name="ase-dependencies"></a>ASE-beroenden ##

### <a name="ase-inbound-dependencies"></a>ASE inkommande beroenden ###

Bara för att ASE ska fungera kräver ASE att följande portar är öppna:

| Användning | Från | Till |
|-----|------|----|
| Hantering | Hanteringsadresser för apptjänst | ASE undernät: 454, 455 |
|  ASE intern kommunikation | ASE-undernät: Alla portar | ASE-undernät: Alla portar
|  Tillåt ingående Azure-belastningsutjämningsutjämning | Azure-lastbalanserare | ASE undernät: 16001

Det finns 2 andra portar som kan visas som öppna på en portskanning, 7654 och 1221. De svarar med en IP-adress och inget mer. De kan blockeras om så önskas. 

Den inkommande hanteringstrafiken ger kommando och kontroll över ASE utöver systemövervakning. Källadresserna för den här trafiken visas i [ASE-hanteringsadressdokumentet.][ASEManagement] Nätverkssäkerhetskonfigurationen måste tillåta åtkomst från ASE-hanteringsadresserna på portarna 454 och 455. Om du blockerar åtkomst från dessa adresser blir din ASE ohälsosam och blir sedan avstängd. TCP-trafiken som kommer in på portarna 454 och 455 måste gå tillbaka ut från samma VIP eller så har du ett asymmetriskt routningsproblem. 

I ASE-undernätet finns det många portar som används för intern komponentkommunikation och de kan ändras. Detta kräver att alla portar i ASE-undernätet ska vara tillgängliga från ASE-undernätet. 

För kommunikationen mellan Azure-belastningsutjämnaren och ASE-undernätet är de minsta portarna som måste vara öppna 454, 455 och 16001. 16001-porten används för att hålla vid liv i trafiken mellan belastningsutjämnaren och ASE. Om du använder en ILB ASE, då kan du låsa trafiken ner till bara 454, 455, 16001 portar.  Om du använder en extern ASE måste du ta hänsyn till de vanliga appåtkomstportarna.  

De andra portar du behöver oroa dig för är programportarna:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Felsökning av Fjärrfelsökning i Visual Studio  |  4020, 4022, 4024 |
|  Webb distribuera tjänst | 8172 |

Om du blockerar programportarna kan din ASE fortfarande fungera, men det kanske inte är din app.  Om du använder apptilldelade IP-adresser med en extern ASE måste du tillåta trafik från IP-adresser som tilldelats dina appar till ASE-undernätet på portarna som visas på sidan ASE-portalen > IP-adresser.

### <a name="ase-outbound-dependencies"></a>ASE-utgående beroenden ###

För utgående åtkomst beror en ASE på flera externa system. Många av dessa systemberoenden definieras med DNS-namn och mappas inte till en fast uppsättning IP-adresser. Ase kräver därför utgående åtkomst från ASE-undernätet till alla externa IPs över en mängd olika portar. 

ASE kommunicerar ut till internetanpassade adresser på följande portar:

| Användningar | Portar |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-uppdateringar, Linux-beroenden, Azure-tjänster | 80/443 |
| Azure SQL | 1433 | 
| Övervakning | 12000 |

De utgående beroendena visas i dokumentet som beskriver [Låsning av utgående trafik i App Service Environment](./firewall-integration.md). Om ASE förlorar åtkomst till sina beroenden slutar den att fungera. När det händer tillräckligt länge, är ASE avbrytas. 

### <a name="customer-dns"></a>Kundens DNS ###

Om det virtuella nätverket är konfigurerat med en kunddefinierad DNS-server använder klientarbetsbelastningarna det. ASE använder Azure DNS för hanteringsändamål. Om det virtuella nätverket är konfigurerat med en kundvald DNS-server måste DNS-servern kunna nås från undernätet som innehåller ASE.

Om du vill testa DNS-upplösningen från webbappen kan du använda konsolens *kommandonamnslösenord*. Gå till felsökningsfönstret på din scm-webbplats för din app eller gå till appen i portalen och välj konsol. Från skalprompten kan du utfärda *kommandonamnslösenren* tillsammans med det DNS-namn som du vill slå upp. Resultatet du får tillbaka är detsamma som vad din app skulle få samtidigt som du gör samma sökning. Om du använder nslookup gör du en sökning med Azure DNS i stället.

Om du ändrar DNS-inställningen för det virtuella nätverk som din ASE befinner sig i måste du starta om ASE:et. För att undvika att starta om ASE rekommenderar vi starkt att du konfigurerar DNS-inställningarna för ditt virtuella nätverk innan du skapar DIN ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portalberoenden ##

Förutom ASE funktionella beroenden, det finns några extra objekt relaterade till portalen erfarenhet. Vissa funktioner i Azure-portalen är beroende av direkt åtkomst till _SCM-webbplatsen_. För varje app i Azure App Service finns det två webbadresser. Den första webbadressen är att komma åt din app. Den andra webbadressen är att komma åt SCM webbplats, som också kallas _Kudu konsolen_. Funktioner som använder SCM webbplats inkluderar:

-   Webbjobb
-   Functions
-   Logga strömning
-   Kudu
-   Tillägg
-   Process Explorer
-   Konsol

När du använder en ILB ASE är SCM-platsen inte tillgänglig utanför det virtuella nätverket. Vissa funktioner fungerar inte från appportalen eftersom de kräver åtkomst till SCM-webbplatsen för en app. Du kan ansluta till SCM-platsen direkt i stället för att använda portalen. 

Om din ILB ASE är domännamnet *contoso.appserviceenvironment.net* och ditt appnamn är *testapp*nås appen på *testapp.contoso.appserviceenvironment.net*. SCM webbplats som går med den nås vid *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>ASE IP-adresser ##

En ASE har några IP-adresser att vara medveten om. De är:

- **Offentlig inkommande IP-adress:** Används för apptrafik i en extern ASE och hanteringstrafik i både en extern ASE och en ILB ASE.
- **Utgående offentlig IP:** Används som "från" IP för utgående anslutningar från ASE som lämnar det virtuella nätverket, som inte dirigeras ned för en VPN.
- **ILB IP-adress:** ILB IP-adressen finns bara i en ILB ASE.
- **Apptilldelade IP-baserade SSL-adresser:** Endast möjligt med en extern ASE och när IP-baserad SSL är konfigurerad.

Alla dessa IP-adresser visas i Azure-portalen från ASE-användargränssnittet. Om du har en ILB ASE visas IP för ILB.

   > [!NOTE]
   > Dessa IP-adresser ändras inte så länge din ASE förblir igång.  Om DIN ASE stängs av och återställs ändras adresserna som används av ASE. Den normala orsaken till att en ASE pausas är om du blockerar åtkomst till inkommande hantering eller blockerar åtkomst till ett ASE-beroende. 

![IP-adresser][3]

### <a name="app-assigned-ip-addresses"></a>Apptilldelade IP-adresser ###

Med en extern ASE kan du tilldela IP-adresser till enskilda appar. Du kan inte göra det med en ILB ASE. Mer information om hur du konfigurerar appen så att den har en egen IP-adress finns i [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](../configure-ssl-bindings.md).

När en app har en egen IP-baserad SSL-adress reserverar ASE två portar för att mappa till den IP-adressen. En port är för HTTP-trafik och den andra porten är för HTTPS. Dessa portar visas i ASE-användargränssnittet i avsnittet IP-adresser. Trafiken måste kunna nå dessa portar från VIP eller apparna är otillgängliga. Det här kravet är viktigt att komma ihåg när du konfigurerar NSG(Network Security Groups).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper ##

[Nätverkssäkerhetsgrupper][NSGs] ger möjlighet att styra nätverksåtkomsten inom ett virtuella nätverk. När du använder portalen finns det en implicit neka regel med lägsta prioritet för att neka allt. Vad du bygger är din tillåta regler.

I en ASE har du inte tillgång till de virtuella datorer som används för att vara värd för SJÄLVA ASE. De finns i en Microsoft-hanterad prenumeration. Om du vill begränsa åtkomsten till apparna på ASE anger du NSG:er i ASE-undernätet. På så sätt bör du vara uppmärksam på ASE-beroendena. Om du blockerar eventuella beroenden slutar ASE att fungera.

NSG kan konfigureras via Azure-portalen eller via PowerShell. Informationen här visar Azure-portalen. Du skapar och hanterar NSG:er i portalen som en resurs på den högsta nivån under **Nätverk.**

De obligatoriska posterna i en NSG, för att en ASE ska fungera, är att tillåta trafik:

**Inkommande**
* från IP-tjänsttaggen AppServiceManagement på portar 454 455
* från belastningsutjämnaren på port 16001
* från ASE-undernätet till ASE-undernätet på alla portar

**Utgående**
* till alla IPs på port 123
* till alla IPs på portar 80, 443
* till IP-tjänsttaggen AzureSQL på portar 1433
* till alla IPs på port 12000
* till ASE-undernätet på alla portar

DNS-porten behöver inte läggas till eftersom trafik till DNS inte påverkas av NSG-regler. Dessa portar innehåller inte de portar som dina appar kräver för att de ska kunna användas. De normala appåtkomstportarna är:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Felsökning av Fjärrfelsökning i Visual Studio  |  4020, 4022, 4024 |
|  Webb distribuera tjänst | 8172 |

När de inkommande och utgående kraven beaktas bör de nationella nationella användargrupperna se ut ungefär som de nationella nationella grupper som visas i det här exemplet. 

![Ingående säkerhetsregler][4]

En standardregel gör att INTERNET-adresser i det virtuella nätverket kan prata med ASE-undernätet. En annan standardregel gör det möjligt för belastningsutjämnaren, även känd som den offentliga VIP, att kommunicera med ASE. Om du vill visa standardreglerna väljer du **Standardregler** bredvid ikonen **Lägg** till. Om du lägger en neka allt annat regel före standardreglerna, förhindrar du trafik mellan VIP och ASE. Om du vill förhindra att trafik kommer inifrån det virtuella nätverket lägger du till en egen regel för att tillåta inkommande. Använd en källa som är lika med **Any** AzureLoadBalancer **\*** med målet Any och ett portintervall med . Eftersom NSG-regeln tillämpas på ASE-undernätet behöver du inte vara specifik i målet.

Om du har tilldelat en IP-adress till din app kontrollerar du att du håller portarna öppna. Om du vill visa portarna väljer du > **IP-adresser** **för App Service Environment**.  

Alla objekt som visas i följande utgående regler behövs, förutom det sista objektet. De möjliggör nätverksåtkomst till ASE-beroenden som noterades tidigare i den här artikeln. Om du blockerar någon av dem slutar DIN ASE att fungera. Det sista objektet i listan gör det möjligt för ASE att kommunicera med andra resurser i ditt virtuella nätverk.

![Säkerhetsregler för utgående trafik][5]

När NSG:erna har definierats tilldelar du dem till undernätet som ASE är aktiverat. Om du inte kommer ihåg ASE-nätverket eller undernätet kan du se det från ASE-portalsidan. Om du vill tilldela NSG till undernätet går du till undernätsgränssnittet och väljer NSG.

## <a name="routes"></a>Vägar ##

Påtvingad tunnelning är när du ställer in rutter i ditt virtuella nätverk så att den utgående trafiken inte går direkt till internet utan någon annanstans som en ExpressRoute-gateway eller en virtuell installation.  Om du behöver konfigurera ASE på ett sådant sätt läser du dokumentet om [du konfigurerar apptjänstmiljön med tvingande tunnel.][forcedtunnel]  Det här dokumentet kommer att berätta vilka alternativ som finns tillgängliga för att arbeta med ExpressRoute och påtvingad tunnel.

När du skapar en ASE i portalen skapar vi också en uppsättning flödestabeller i undernätet som skapas med ASE.  Dessa vägar säger helt enkelt att skicka utgående trafik direkt till Internet.  
Så här skapar du samma vägar manuellt:

1. Gå till Azure Portal. Välj > **Nätverksvägtabeller**. **Networking**

2. Skapa en ny flödestabell i samma region som ditt virtuella nätverk.

3. Välj **Rutter** > **Lägg till**i vägtabellgränssnittet.

4. Ange **nästa hopptyp** till **Internet** och **adressprefixet** till **0.0.0.0/0**. Välj **Spara**.

    Du ser då något i stil med följande:

    ![Funktionella rutter][6]

5. När du har skapat den nya flödestabellen går du till det undernät som innehåller DIN ASE. Välj din rutttabell i listan i portalen. När du har sparat ändringen bör du sedan se NSGs och rutter som anges med ditt undernät.

    ![NSG och rutter][7]

## <a name="service-endpoints"></a>Tjänstslutpunkter ##

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstslutpunkter i dokumentationen [Tjänstslutpunkter för virtuellt nätverk][serviceendpoints]. 

När du aktiverar tjänstens slutpunkter för en resurs, finns det vägar som skapats med högre prioritet än andra vägar. Om du använder tjänstslutpunkter på en Azure-tjänst, med en påtvingad tunnel ASE, kommer trafiken till dessa tjänster inte att tvingas tunnel. 

När tjänstens slutpunkter är aktiverade på ett undernät med en Azure SQL-instans, måste alla Azure SQL-instanser som är anslutna från undernätet ha aktiverat tjänstens slutpunkter. Om du vill ha åtkomst till flera Azure SQL-instanser från samma undernät kan du inte aktivera tjänstens slutpunkter på en Azure SQL-instans och inte på en annan. Ingen annan Azure-tjänst fungerar som Azure SQL med avseende på tjänstslutpunkter. När du aktiverar tjänstens slutpunkter med Azure Storage kan du låsa åtkomsten till resursen från undernätet, men du kan ändå använda andra Azure Storage-konton även om de inte har aktiverat tjänstens slutpunkter.  

![Tjänstslutpunkter][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
