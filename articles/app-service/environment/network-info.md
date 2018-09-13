---
title: Nätverksöverväganden med en Azure App Service-miljö
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
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: ef2288e2f756db6529f1ec5f7b3a49067b2998aa
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648915"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Nätverksöverväganden för App Service Environment #

## <a name="overview"></a>Översikt ##

 Azure [App Service Environment] [ Intro] är en distribution av Azure App Service till ett undernät i Azure-nätverk (VNet). Det finns två distributionstyper för en App Service environment (ASE):

- **Extern ASE**: exponerar apparna ASE som värd på en internet-tillgänglig IP-adress. Mer information finns i [skapar en extern ASE][MakeExternalASE].
- **ILB ASE**: exponerar apparna ASE som värd på en IP-adress i det virtuella nätverket. Den interna slutpunkten är en intern belastningsutjämnare (ILB), vilket är anledningen till det kallas en ILB ASE. Mer information finns i [skapa och använda en ILB ASE][MakeILBASE].

Det finns två versioner av App Service Environment: ASEv1 och ASEv2. Information om ASEv1 finns i [introduktion till App Service Environment v1][ASEv1Intro]. ASEv1 kan distribueras i en klassisk eller Resource Manager-VNet. ASEv2 kan endast distribueras till ett Resource Manager-VNet.

Alla anrop från en ASE som går till internet lämna det virtuella nätverket via en VIP för ASE. Offentliga IP-Adressen för den här VIP är IP-källan för alla anrop från ase: N som går till internet. Om appar i din ASE göra anrop till resurser i ditt virtuella nätverk eller i en VPN-anslutning, är en av IP-adresser i undernätet som används av din ASE käll-IP. Eftersom ASE är inom det virtuella nätverket, kan det också komma åt resurser i det virtuella nätverket utan någon ytterligare konfiguration. Om det virtuella nätverket är anslutet till ditt lokala nätverk, appar i din ASE även att ha åtkomst till resurser det utan ytterligare konfiguration.

![Extern ASE][1] 

Om du har en extern ASE är också det offentliga VIP slutpunkten att dina appar i ASE matcha för:

* HTTP/S. 
* FTP/S. 
* Webbdistribution.
* Fjärrfelsökning.

![ILB ASE][2]

Om du har en ILB ASE är adressen för den interna Belastningsutjämnaren slutpunkten för HTTP/S, FTP/S, webbdistribution och fjärrfelsökning.

Åtkomstportar vanlig app är:

| Användning | Från | Till |
|----------|---------|-------------|
|  HTTP/HTTPS  | Konfigureras av användaren |  80, 443 |
|  FTP/FTPS    | Konfigureras av användaren |  21, 990, 10001-10020 |
|  Visual Studio fjärrfelsökning  |  Konfigureras av användaren |  4016, 4018, 4020, 4022 |

Detta gäller om du är på en extern ASE eller på en ILB ASE. Om du använder en extern ASE kan kommer du till dessa portar på det offentliga VIP. Om du är på en ILB ASE, kommer du till dessa portar på den interna Belastningsutjämnaren. Om du låsa port 443, kan det finnas en effekt på vissa funktioner som exponeras i portalen. Mer information finns i [Portal beroenden](#portaldep).

## <a name="ase-subnet-size"></a>Storleken för ASE-undernätet ##

Kan inte ändras storleken på det undernät som används som värd för en ASE när ASE har distribuerats.  ASE använder en adress för varje infrastrukturrollen samt för varje instans för isolerad App Service-plan.  Det finns dessutom 5 adresser som används av Azure Networking för varje undernät som har skapats.  En ASE med inga App Service-planer använder alls 12 adresser innan du skapar en app.  Om det är en ILB ASE kommer den använda 13-adresser innan du skapar en app i denna ASE. När du skalar ut din ASE läggs infrastrukturroller varje multipel av 15 och 20 i din App Service-planinstanser.

   > [!NOTE]
   > Inget annat kan finnas i undernätet men ASE. Var noga med att välja ett adressutrymme som tillåts för framtida tillväxt. Du kan inte ändra den här inställningen senare. Vi rekommenderar en storlek på `/24` med 256-adresser.

När du skalar upp eller ned, nya roller av rätt storlek har lagts till och sedan dina arbetsbelastningar migreras från den aktuella storleken till målstorleken. Efter att dina appar migreras de ursprungliga virtuella datorerna tas bort. Det innebär att om du hade en ASE med 100 ASP-instanser det skulle vara en punkt där du behöver dubbelt så många virtuella datorer.  Det är därför som vi rekommenderar användning av en ”/ 24” för alla ändringar som du kan behöva.  

## <a name="ase-dependencies"></a>ASE-beroenden ##

ASE inkommande åtkomst beroenden är:

| Användning | Från | Till |
|-----|------|----|
| Hantering | Hanteringsadresser för App Service | ASE-undernät: 454, 455 |
|  ASE intern kommunikation | ASE-undernät: alla portar | ASE-undernät: alla portar
|  Tillåt Azure-belastningsutjämnare inkommande | Azure-lastbalanserare | ASE-undernät: alla portar
|  App tilldelade IP-adresser | App som är tilldelade adresser | ASE-undernät: alla portar

Inkommande hanteringstrafik ger kontroll av ASE förutom systemövervakning. Käll-adresserna för den här trafiken finns i den [ASE Management adresser] [ ASEManagement] dokumentet. Nätverkssäkerhetskonfigurationen måste tillåta åtkomst från alla IP-adresser på port 454 och 455. Om du blockerar åtkomst från dessa adresser kan din ASE blir ohälsosamt och sedan blir pausas.

I ASE-undernät som det finns många portar som används för interna komponenten kan kommunikation och de ändra.  Detta kräver att alla portar i ASE-undernät är tillgänglig från ASE-undernät. 

För kommunikationen mellan Azure-belastningsutjämnaren och på ASE-undernätet är de portar som måste vara öppna minst 454 och 455 16001. 16001 port används för keep alive trafik mellan belastningsutjämnare och ASE. Om du använder en ILB ASE, så du kan låsa trafik till bara 454, 455, 16001 portar.  Om du använder en extern ASE måste du ta hänsyn till åtkomstportar vanlig app.  Om du använder app tilldelas adresser som du behöver öppna den till alla portar.  När en adress har tilldelats en viss app kan använda portar som inte känd av i förväg att skicka HTTP och HTTPS-trafik till ASE belastningsutjämnaren.

Om du använder appen tilldelade IP-adresser som du vill tillåta trafik från IP-adresser tilldelade till dina appar i ASE-undernätet.

En ASE beror på flera externa system för utgående åtkomst. Dessas system definieras med DNS-namn och mappas inte till en fast uppsättning IP-adresser. Därför kräver ASE utgående åtkomst från ASE-undernät till alla externa IP-adresser i olika portar. En ASE har följande utgående beroenden:

| Användning | Från | Till |
|-----|------|----|
| Azure Storage | ASE-undernät | Table.Core.Windows.NET, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 krävs endast för ASEv1.) |
| Azure SQL Database | ASE-undernät | Database.Windows.NET: 1433 |
| Azure-hantering | ASE-undernät | Management.Core.Windows.NET, management.azure.com, admin.core.windows.net: 443 |
| SSL-certifikatverifiering |  ASE-undernät            |  OCSP.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443 |
| Azure Active Directory        | ASE-undernät            |  login.Windows.NET: 443 |
| App Service-hantering        | ASE-undernät            |  gr-prod -<regionspecific>. cloudapp.net, az prod.metrics.nsatc .net: 443 |
| Azure DNS                     | ASE-undernät            |  Internet: 53 |
| ASE intern kommunikation    | ASE-undernät: alla portar |  ASE-undernät: alla portar |

Om ASE förlorar åtkomst till dessa beroenden, slutar fungera. När det sker tillräckligt länge inaktiveras ASE.

### <a name="customer-dns"></a>Kunden DNS ###

Om det virtuella nätverket konfigureras med en kunddefinierad DNS-server kan använda den för klienternas arbetsbelastningar. ASE behöver fortfarande kommunicera med Azure DNS för hanteringsändamål. 

Om det virtuella nätverket har konfigurerats med en kund DNS på den andra sidan av en VPN-anslutning, måste DNS-server kunna nås från det undernät som innehåller ASE.

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

När du använder en ILB ASE är SCM-webbplatsen inte internet som är åtkomliga från utanför det virtuella nätverket. När appen körs på en ILB ASE, kommer vissa funktioner inte fungerar från portalen.  

Många av de funktioner som är beroende av SCM-webbplatsen är också tillgängliga direkt i Kudu-konsolen. Du kan ansluta till den direkt i stället med hjälp av portalen. Om din app finns i en ILB ASE, kan du använda dina pucliceringsautentiseringsuppgifter för att logga in. URL till SCM-webbplatsen för en app som finns i en ILB ASE har följande format: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Om din ILB ASE har domännamnet *contoso.net* och appnamnet på din är *testapp*, appen har nåtts på *testapp.contoso.net*. SCM-webbplatsen som medföljer den uppnås vid *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Functions och webbjobb ##

Både Functions och webb-jobb beror på SCM-webbplatsen men stöds för användning i portalen, även om dina appar finns i en ILB ASE, så länge som din webbläsare kan nå SCM-webbplatsen.  Om du använder ett självsignerat certifikat med din ILB ASE, kommer du behöva aktivera webbläsaren för att lita på certifikatet.  För Internet Explorer och Edge innebär det att certifikatet måste finnas i arkivet med betrodda datorn.  Om du använder Chrome och sedan det innebär att du godkänt tidigare certifikatet i webbläsaren genom att trycka antas vara scm-webbplatsen direkt.  Den bästa lösningen är att använda ett kommersiellt certifikat som finns i webbläsarens certifikatkedja med förtroenden.  

## <a name="ase-ip-addresses"></a>ASE-IP-adresser ##

En ASE har några IP-adresser känna till. De är:

- **Offentliga inkommande IP-adressen**: används för trafik i en extern ASE och hanteringstrafik i både en extern ASE och en ILB ASE.
- **Utgående offentliga IP-Adressen**: används som ”från”-IP för utgående anslutningar från ase: N som lämnar det virtuella nätverket, vilket inte dirigeras ned en VPN-anslutning.
- **ILB-IP-adress**: Om du använder en ILB ASE.
- **App-tilldelad IP-baserad SSL-adresser**: bara möjligt med en extern ASE och när IP-baserad SSL har konfigurerats.

Dessa IP-adresser är väl synlig i en ASEv2 i Azure-portalen från ASE UI. Om du har en ILB ASE, visas den IP-Adressen för den interna Belastningsutjämnaren.

   > [!NOTE]
   > Dessa IP-adresser ändras inte så länge din ASE är igång.  Om din ASE blir har pausats och återställas, ändras de adresser som används av din ASE. Den normala orsaken till en ase-miljö ska bli uppehåll är om du blockerar inkommande åtkomst eller blockera åtkomst till en ASE-beroende. 

![IP-adresser][3]

### <a name="app-assigned-ip-addresses"></a>App-tilldelad IP-adresser ###

Du kan tilldela IP-adresser till enskilda appar med en extern ASE. Du kan inte göra det med en ILB ASE. Läs mer om hur du konfigurerar din app om du vill ha sin egen IP-adress, [binda ett befintligt anpassat SSL-certifikat till Azure web apps](../app-service-web-tutorial-custom-ssl.md).

När en app har sin egen IP-baserad SSL-adress, reserverar ASE två portar för att mappa till IP-adress. En port är för HTTP-trafik, och den andra porten för HTTPS. De portarna som listas i ASE UI i avsnittet IP-adresser. Trafik måste kunna nå dessa portar från VIP eller apparna är otillgängligt. Det här kravet är viktigt att komma ihåg när du konfigurerar Nätverkssäkerhetsgrupper (NSG).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper ##

[Nätverkssäkerhetsgrupper] [ NSGs] ger möjlighet att styra åtkomsten till nätverket inom ett virtuellt nätverk. När du använder portalen finns en implicit neka-regeln med lägsta prioritet att neka allt. Du skapar är din tillåter regler.

I en ASE har du inte åtkomst till de virtuella datorerna som används som värd för ASE själva. De är i en Microsoft-hanterad prenumeration. Om du vill begränsa åtkomsten till appar på ASE ange NSG: er på ASE-undernät. På så sätt kan uppmärksamma noggrann i ASE-beroenden. Om du blockerar eventuella beroenden slutar ASE fungera.

NSG: er kan konfigureras via Azure portal eller via PowerShell. Informationen här visar Azure-portalen. Du skapar och hanterar NSG: er i portalen som en resurs på toppnivå under **nätverk**.

När kraven på inkommande och utgående beaktas, bör NSG: erna likna NSG: er som visas i det här exemplet. VNet-adressintervall är _192.168.250.0/23_, och det undernät som ASE är i är _192.168.251.128/25_.

De första två inkommande kraven för ASE ska fungera visas överst i listan i det här exemplet. De Aktivera hantering av ASE och Tillåt ASE att kommunicera med sig själv. De andra posterna är alla klient som kan konfigureras och kan styra åtkomst till program i ASE. 

![Ingående säkerhetsregler][4]

En standardregel kan IP-adresser i det virtuella nätverket kan kommunicera med ASE-undernät. En annan standardregel som gör det möjligt för belastningsutjämnaren, även känt som det offentliga VIP, att kommunicera med ASE. Om du vill se standardreglerna **standardregler** bredvid den **Lägg till** ikon. Om du placerar en neka allt annat regel när NSG-regler visas måste du förhindra att trafik mellan VIP och ASE. Lägga till egna regel som tillåter inkommande för att förhindra att trafik som kommer från i det virtuella nätverket. Använda en källa som är lika med AzureLoadBalancer med valfri målplats **alla** och ett portintervall för **\***. Eftersom NSG-regel används på ASE-undernät, behöver du inte vara specifikt i målet.

Om du har tilldelat en IP-adress till din app, kontrollera att du behåller portarna öppna. Om du vill se portarna som **App Service Environment** > **IP-adresser**.  

Alla objekt som visas i följande regler för utgående behövs, förutom det sista objektet. De gör att nätverksåtkomsten till ASE-beroenden som noterades tidigare i den här artikeln. Om du blockerar någon av dem kan slutar din ASE fungera. Det sista objektet i listan kan din ASE att kommunicera med andra resurser i ditt virtuella nätverk.

![Utgående säkerhetsregler][5]

När dina NSG: er har definierats kan du tilldela dem till det undernät som ASE är på. Om du inte kommer ihåg ASE VNet eller undernät kan se du den i ASE-portalsidan. Tilldela undernätets NSG: N genom att gå till undernätet Användargränssnittet och välj NSG: N.

## <a name="routes"></a>Vägar ##

Tvingad tunneltrafik är när du ställer in vägar i ditt virtuella nätverk så att den utgående trafiken inte gå direkt till internet men någon annanstans som en ExpressRoute-gateway eller en virtuell installation.  Om du vill konfigurera din ASE i ett sådant och läsa dokumentet på [konfigurera App Service Environment med Tvingad tunneltrafik][forcedtunnel].  Det här dokumentet om alternativ som fungerar med ExpressRoute och Tvingad tunneltrafik.

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

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstens slutpunkter i dokumentationen [Tjänstens slutpunkter för virtuella nätverk][serviceendpoints]. 

   > [!NOTE]
   > Tjänstslutpunkter med SQL fungerar inte med ASE i US Government-regioner. Den här informationen är endast giltig i de offentliga Azure-regionerna.

När du aktiverar tjänstens slutpunkter för en resurs, finns det vägar som skapats med högre prioritet än andra vägar. Om du använder tjänstens slutpunkter med tvingad tunneltrafik för ASE, kommer hanteringstrafiken för Azure SQL och Azure Storage inte omfattas av den tvingade tunneltrafiken. 

När tjänstens slutpunkter är aktiverade på ett undernät med en Azure SQL-instans, måste alla Azure SQL-instanser som är anslutna från undernätet ha aktiverat tjänstens slutpunkter. Om du vill ha åtkomst till flera Azure SQL-instanser från samma undernät kan du inte aktivera tjänstens slutpunkter på en Azure SQL-instans och inte på en annan. Azure Storage fungerar inte på samma sätt som Azure SQL. När du aktiverar tjänstens slutpunkter med Azure Storage kan du låsa åtkomsten till resursen från undernätet, men du kan ändå använda andra Azure Storage-konton även om de inte har aktiverat tjänstens slutpunkter.  

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
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
