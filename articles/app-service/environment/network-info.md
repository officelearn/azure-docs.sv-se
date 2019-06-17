---
title: Nätverksöverväganden med App Service Environment - Azure
description: 'Förklarar ASE-nätverkstrafik och hur du ställer in NSG: er och udr: er med din ASE'
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3f80f3c6be747cf84aa9d8b2c386c0568a7511ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069384"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Nätverksöverväganden för App Service Environment #

## <a name="overview"></a>Översikt ##

 Azure [App Service Environment] [ Intro] är en distribution av Azure App Service till ett undernät i Azure-nätverk (VNet). Det finns två distributionstyper för en App Service environment (ASE):

- **Extern ASE**: Exponerar apparna ASE som värd på en internet-tillgänglig IP-adress. Mer information finns i [skapar en extern ASE][MakeExternalASE].
- **ILB ASE**: Exponerar apparna ASE som värd på en IP-adress i det virtuella nätverket. Den interna slutpunkten är en intern belastningsutjämnare (ILB), vilket är anledningen till det kallas en ILB ASE. Mer information finns i [skapa och använda en ILB ASE][MakeILBASE].

Alla ase-miljöer, externa och interna Belastningsutjämnaren, har en offentlig VIP som används för inkommande hanteringstrafik och som den från-adress vid anrop från ase: N till internet. Anrop från en ASE som går till internet lämna det virtuella nätverket igenom den VIP som tilldelats för ASE. Offentliga IP-Adressen för den här VIP är IP-källan för alla anrop från ase: N som går till internet. Om appar i din ASE göra anrop till resurser i ditt virtuella nätverk eller i en VPN-anslutning, är en av IP-adresser i undernätet som används av din ASE käll-IP. Eftersom ASE är inom det virtuella nätverket, kan det också komma åt resurser i det virtuella nätverket utan någon ytterligare konfiguration. Om det virtuella nätverket är anslutet till ditt lokala nätverk, appar i din ASE även att ha åtkomst till resurser det utan ytterligare konfiguration.

![Extern ASE][1] 

Om du har en extern ASE är också det offentliga VIP slutpunkten att dina appar i ASE matcha för:

* HTTP/S 
* FTP/S
* Webbdistribution
* Fjärrfelsökning

![ILB ASE][2]

Om du har en ILB ASE är adressen för ILB-adressen slutpunkten för HTTP/S, FTP/S, webbdistribution och fjärrfelsökning.

## <a name="ase-subnet-size"></a>Storleken för ASE-undernätet ##

Kan inte ändras storleken på det undernät som används som värd för en ASE när ASE har distribuerats.  ASE använder en adress för varje infrastrukturrollen samt för varje instans för isolerad App Service-plan.  Det finns dessutom fem adresser som används av Azure Networking för varje undernät som har skapats.  En ASE med inga App Service-planer använder alls 12 adresser innan du skapar en app.  Om det är en ILB ASE, kommer den använda 13-adresser innan du skapar en app i denna ASE. När du skalar ut din ASE läggs infrastrukturroller varje multipel av 15 och 20 i din App Service-planinstanser.

   > [!NOTE]
   > Inget annat kan finnas i undernätet men ASE. Var noga med att välja ett adressutrymme som tillåts för framtida tillväxt. Du kan inte ändra den här inställningen senare. Vi rekommenderar en storlek på `/24` med 256-adresser.

När du skalar upp eller ned, nya roller av rätt storlek har lagts till och sedan dina arbetsbelastningar migreras från den aktuella storleken till målstorleken. Ursprungligt virtuella datorer bort bara när arbetsbelastningarna som har migrerats. Om du har en ASE med 100 ASP-instanser, skulle det vara en punkt där du behöver dubbelt så många virtuella datorer.  Det är därför som vi rekommenderar användning av en ”/ 24” för alla ändringar som du kan behöva.  

## <a name="ase-dependencies"></a>ASE-beroenden ##

### <a name="ase-inbound-dependencies"></a>ASE inkommande beroenden ###

Ase: N kräver följande portar vara öppna för ASE ska fungera:

| Användning | Från | Till |
|-----|------|----|
| Hantering | Hanteringsadresser för App Service | ASE-undernät: 454, 455 |
|  ASE intern kommunikation | ASE-undernät: Alla portar | ASE-undernät: Alla portar
|  Tillåt Azure-belastningsutjämnare inkommande | Azure-lastbalanserare | ASE-undernät: 16001

Det finns 2 portar som kan visa vara öppna på en port-sökning, 7654 och 1221. De svara med en IP-adress och inget mer. De kan blockeras om du vill. 

Inkommande hanteringstrafik ger kontroll av ASE förutom systemövervakning. Käll-adresserna för den här trafiken finns i den [ASE Management adresser] [ ASEManagement] dokumentet. Nätverkssäkerhetskonfigurationen måste tillåta åtkomst från hanteringsadresserna ASE på port 454 och 455. Om du blockerar åtkomst från dessa adresser kan din ASE blir ohälsosamt och sedan blir pausas. TCP-trafik som kommer in på port 454 och 455 måste gå tillbaka ut från samma VIP eller du har ett problem med asymmetrisk routning. 

Det finns många portar som används för komponentkommunikation i intern i ASE-undernät och de kan ändra. Detta kräver att alla portar i ASE-undernät är tillgänglig från ASE-undernät. 

För kommunikationen mellan Azure-belastningsutjämnaren och på ASE-undernätet är de portar som måste vara öppna minst 454 och 455 16001. 16001 port används för keep alive trafik mellan belastningsutjämnare och ASE. Om du använder en ILB ASE, så du kan låsa trafik till bara 454, 455, 16001 portar.  Om du använder en extern ASE, måste du ta hänsyn till åtkomstportar vanlig app.  

De portar som du behöver avse själv med är programportar:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio fjärrfelsökning  |  4020, 4022, 4024 |
|  Distribuera webbtjänsten | 8172 |

Om du blockerar programportar din ASE kan fungera, men appen kan inte.  Om du använder appen tilldelade IP-adresser med en extern ASE, behöver du som tillåter trafik från IP-adresser tilldelade till dina appar till ASE-undernät på de portar som visas i ASE-portalen > sidan IP-adresser.

### <a name="ase-outbound-dependencies"></a>Utgående ASE-beroenden ###

En ASE beror på flera externa system för utgående åtkomst. Många av dessa systemberoenden definieras med DNS-namn och mappas inte till en fast uppsättning IP-adresser. Därför kräver ASE utgående åtkomst från ASE-undernät till alla externa IP-adresser i olika portar. 

ASE kommunicerar ut att komma åt internet-adresser på följande portar:

| Användningsområden | Portar |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, uppdateringar för Windows, Linux beroenden, Azure-tjänster | 80/443 |
| Azure SQL | 1433 | 
| Övervakning | 12000 |

Utgående beroenden listas i dokument som beskriver [låsa utgående trafik för App Service Environment](./firewall-integration.md). Om ASE förlorar åtkomst till dess beroenden, slutar fungera. När det sker tillräckligt länge inaktiveras ASE. 

### <a name="customer-dns"></a>Kunden DNS ###

Om det virtuella nätverket konfigureras med en kunddefinierad DNS-server kan använda den för klienternas arbetsbelastningar. ASE använder Azure DNS för hanteringsändamål. Om det virtuella nätverket har konfigurerats med en kund har valt DNS-server, måste DNS-server kunna nås från det undernät som innehåller ASE.

Om du vill testa DNS-matchningen från ditt program, kan du använda kommandot konsolen *nameresolver*. Gå till felsökningsfönstret i scm-webbplatsen för din app eller gå till appen i portalen och välj konsolen. Från shell-prompten kan du utfärda kommandot *nameresolver* tillsammans med DNS-namn som du vill söka efter. Du kommer tillbaka resultatet är samma som din app skulle få när du gjorde samma sökning. Om du använder nslookup, gör du en sökning med Azure DNS i stället.

Om du ändrar DNS-inställningen för det virtuella nätverk som din ASE är i kommer du behöva starta om din ASE. Om du vill undvika att starta om din ASE, rekommenderar vi starkt att du konfigurerar DNS-inställningarna för ditt virtuella nätverk innan du skapar din ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portalen beroenden ##

Förutom funktionella beroenden ASE finns några extra objekt som är relaterade till en Portal. Några av funktionerna i Azure-portalen är beroende av direkt åtkomst till _SCM-webbplatsen_. Det finns två URL: er för varje app i Azure App Service. Första Webbdressen är att få åtkomst till din app. URL som andra är att få åtkomst till SCM-webbplatsen, som också kallas den _Kudu-konsolen_. Funktioner som använder SCM-webbplatsen:

-   Webbjobb
-   Functions
-   Loggströmning
-   Kudu
-   Tillägg
-   Processutforskaren
-   Konsolen

När du använder en ILB ASE är SCM-webbplatsen inte tillgängligt utanför det virtuella nätverket. Vissa funktioner fungerar inte från app-portalen eftersom de kräver åtkomst till SCM-webbplatsen för en app. Du kan ansluta till SCM-webbplatsen direkt i stället för med hjälp av portalen. 

Om din ILB ASE har domännamnet *contoso.appserviceenvironnment.net* och appnamnet på din är *testapp*, appen har nåtts på *testapp.contoso.appserviceenvironment.net*. SCM-webbplatsen som medföljer den uppnås vid *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>ASE-IP-adresser ##

En ASE har några IP-adresser känna till. De är:

- **Offentliga inkommande IP-adressen**: Används för trafik i en extern ASE och hanteringstrafik i både en extern ASE och en ILB ASE.
- **Utgående offentliga IP-Adressen**: Används som ”från”-IP för utgående anslutningar från ase: N som lämnar det virtuella nätverket, vilket inte dirigeras ned en VPN-anslutning.
- **ILB-IP-adress**: ILB-IP-adressen finns bara i en ILB ASE.
- **App-tilldelad IP-baserad SSL-adresser**: Bara kan utföras med en extern ASE och när IP-baserad SSL har konfigurerats.

Dessa IP-adresser är synliga i Azure-portalen från ASE UI. Om du har en ILB ASE, visas den IP-Adressen för den interna Belastningsutjämnaren.

   > [!NOTE]
   > Dessa IP-adresser ändras inte så länge din ASE är igång.  Om din ASE blir har pausats och återställas, ändras de adresser som används av din ASE. Den normala orsaken till en ase-miljö ska bli uppehåll är om du blockerar inkommande åtkomst eller blockera åtkomst till en ASE-beroende. 

![IP-adresser][3]

### <a name="app-assigned-ip-addresses"></a>App-tilldelad IP-adresser ###

Du kan tilldela IP-adresser till enskilda appar med en extern ASE. Du kan inte göra det med en ILB ASE. Läs mer om hur du konfigurerar din app om du vill ha sin egen IP-adress, [binda ett befintligt anpassat SSL-certifikat till Azure App Service](../app-service-web-tutorial-custom-ssl.md).

När en app har sin egen IP-baserad SSL-adress, reserverar ASE två portar för att mappa till IP-adress. En port är för HTTP-trafik, och den andra porten för HTTPS. De portarna som listas i ASE UI i avsnittet IP-adresser. Trafik måste kunna nå dessa portar från VIP eller apparna är otillgängligt. Det här kravet är viktigt att komma ihåg när du konfigurerar Nätverkssäkerhetsgrupper (NSG).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper ##

[Nätverkssäkerhetsgrupper] [ NSGs] ger möjlighet att styra åtkomsten till nätverket inom ett virtuellt nätverk. När du använder portalen finns en implicit neka-regeln med lägsta prioritet att neka allt. Du skapar är din tillåter regler.

I en ASE har du inte åtkomst till de virtuella datorerna som används som värd för ASE själva. De är i en Microsoft-hanterad prenumeration. Om du vill begränsa åtkomsten till appar på ASE ange NSG: er på ASE-undernät. På så sätt kan uppmärksamma noggrann i ASE-beroenden. Om du blockerar eventuella beroenden slutar ASE fungera.

NSG: er kan konfigureras via Azure portal eller via PowerShell. Informationen här visar Azure-portalen. Du skapar och hanterar NSG: er i portalen som en resurs på toppnivå under **nätverk**.

Det finns några poster i en Nätverkssäkerhetsgrupp för en ASE ska fungera som tillåter trafik:

**Inkommande**
* från IP-Adressen servicetagg AppServiceManagement på portar 454,455
* från belastningsutjämnaren på port 16001
* från ASE-undernät till ASE-undernät på alla portar

**Utgående**
* till alla IP-adresser på porten 123
* till alla IP-adresser på portarna 80, 443
* till IP-Adressen servicetagg AzureSQL på port 1433
* till alla IP-adresser på port 12000
* ASE-undernätet på alla portar

DNS-port behöver inte enligt trafik till DNS inte påverkas av NSG-regler. De här portarna omfattar inte de portar som dina appar kräver för att använda. Åtkomstportar vanlig app är:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio fjärrfelsökning  |  4020, 4022, 4024 |
|  Distribuera webbtjänsten | 8172 |

När kraven på inkommande och utgående beaktas, bör NSG: erna likna NSG: er som visas i det här exemplet. 

![Ingående säkerhetsregler][4]

En standardregel kan IP-adresser i det virtuella nätverket kan kommunicera med ASE-undernät. En annan standardregel som gör det möjligt för belastningsutjämnaren, även känt som det offentliga VIP, att kommunicera med ASE. Om du vill se standardreglerna **standardregler** bredvid den **Lägg till** ikon. Om du placerar en neka allt annat regeln innan standardreglerna du förhindra att trafik mellan VIP och ASE. Lägga till egna regel som tillåter inkommande för att förhindra att trafik som kommer från i det virtuella nätverket. Använda en källa som är lika med AzureLoadBalancer med valfri målplats **alla** och ett portintervall för **\*** . Eftersom NSG-regel används på ASE-undernät, behöver du inte vara specifikt i målet.

Om du har tilldelat en IP-adress till din app, kontrollera att du behåller portarna öppna. Om du vill se portarna som **App Service Environment** > **IP-adresser**.  

Alla objekt som visas i följande regler för utgående behövs, förutom det sista objektet. De gör att nätverksåtkomsten till ASE-beroenden som noterades tidigare i den här artikeln. Om du blockerar någon av dem kan slutar din ASE fungera. Det sista objektet i listan kan din ASE att kommunicera med andra resurser i ditt virtuella nätverk.

![Utgående säkerhetsregler][5]

När dina NSG: er har definierats kan du tilldela dem till det undernät som ASE är på. Om du inte kommer ihåg ASE VNet eller undernät kan se du den i ASE-portalsidan. Tilldela undernätets NSG: N genom att gå till undernätet Användargränssnittet och välj NSG: N.

## <a name="routes"></a>Vägar ##

Tvingad tunneltrafik är när du ställer in vägar i ditt virtuella nätverk så att den utgående trafiken inte gå direkt till internet men någon annanstans som en ExpressRoute-gateway eller en virtuell installation.  Om du vill konfigurera din ASE i ett sådant kan läsa dokumentet på [konfigurera App Service Environment med Tvingad tunneltrafik][forcedtunnel].  Det här dokumentet om alternativ som fungerar med ExpressRoute och Tvingad tunneltrafik.

När du skapar en ASE i portalen skapa vi också en uppsättning routningstabeller i det undernät som har skapats med ASE.  Dessa vägar anta att bara skicka utgående trafik direkt till internet.  
Följ dessa steg om du vill skapa samma vägar manuellt:

1. Gå till Azure Portal. Välj **nätverk** > **routningstabeller**.

2. Skapa en ny routningstabell i samma region som ditt virtuella nätverk.

3. Från din routningstabellen-Gränssnittet väljer **vägar** > **Lägg till**.

4. Ange den **nästa hopptyp** till **Internet** och **adressprefix** till **0.0.0.0/0**. Välj **Spara**.

    Då visas något som liknar följande:

    ![Funktionella vägar][6]

5. När du har skapat den nya routningstabellen går du till det undernät som innehåller din ASE. Välj din routningstabellen i listan i portalen. När du har sparat ändringen visas sedan NSG: er och vägar som anges med ditt undernät.

    ![NSG: er och flöden][7]

## <a name="service-endpoints"></a>Serviceslutpunkter ##

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstslutpunkter i dokumentationen [Tjänstslutpunkter för virtuellt nätverk][serviceendpoints]. 

När du aktiverar tjänstens slutpunkter för en resurs, finns det vägar som skapats med högre prioritet än andra vägar. Om du använder tjänstslutpunkter på valfri Azure-tjänst, med Tvingad tunneltrafik för ASE, trafiken till de tjänsterna som inte ska tvingas tunneltrafik. 

När tjänstens slutpunkter är aktiverade på ett undernät med en Azure SQL-instans, måste alla Azure SQL-instanser som är anslutna från undernätet ha aktiverat tjänstens slutpunkter. Om du vill ha åtkomst till flera Azure SQL-instanser från samma undernät kan du inte aktivera tjänstens slutpunkter på en Azure SQL-instans och inte på en annan. Inga andra Azure-tjänster fungerar som Azure SQL med avseende på Tjänsteslutpunkter. När du aktiverar tjänstens slutpunkter med Azure Storage kan du låsa åtkomsten till resursen från undernätet, men du kan ändå använda andra Azure Storage-konton även om de inte har aktiverat tjänstens slutpunkter.  

![Serviceslutpunkter][8]

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
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
