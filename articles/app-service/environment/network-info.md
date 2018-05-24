---
title: Överväganden för nätverk med en Azure Apptjänst-miljö
description: 'Förklarar ASE nätverkstrafik och hur du ställer in NSG: er och udr: er med din ASE'
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
ms.date: 03/20/2018
ms.author: ccompy
ms.openlocfilehash: d099163cdc34624afd8f01b8f1978c5ee902d1ff
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Överväganden för nätverk för en Apptjänstmiljö #

## <a name="overview"></a>Översikt ##

 Azure [Apptjänstmiljö] [ Intro] är en distribution av Azure App Service till ett undernät i ditt Azure-nätverk (VNet). Det finns två distributionstyper för en Apptjänst-miljö (ASE):

- **Externa ASE**: Visar ASE värd apparna på en internet-tillgänglig IP-adress. Mer information finns i [skapa en extern ASE][MakeExternalASE].
- **ILB ASE**: Visar ASE värd apparna på en IP-adress i ditt VNet. Den interna slutpunkten är en intern belastningsutjämnare (ILB), vilket är anledningen till den kallas en ILB ASE. Mer information finns i [skapa och använda en ILB ASE][MakeILBASE].

Det finns nu två versioner av Apptjänstmiljö: ASEv1 och ASEv2. Information om ASEv1 finns [introduktion till Apptjänstmiljö v1][ASEv1Intro]. ASEv1 kan distribueras i ett klassiskt eller Resource Manager VNet. ASEv2 kan endast distribueras till en Resource Manager-VNet.

Alla samtal från en ASE går till internet lämna VNet via en VIP för ASE. Offentliga IP-Adressen för den här VIP är sedan käll-IP för alla samtal från ASE går till internet. Om appar i din ASE gör anrop till resurser i ditt virtuella nätverk eller i en VPN-anslutning, är en av IP-adresser i undernätet som används av din ASE käll-IP. Eftersom ASE är inom VNet, kan den också komma åt resurser inom VNet utan någon ytterligare konfiguration. Om VNet som är ansluten till ditt lokala nätverk, har appar i din ASE också åtkomst till resurser det. Du behöver inte konfigurera ASE eller din app några ytterligare.

![Externa ASE][1] 

Om du har en extern ASE är det offentliga VIP också att dina appar ASE matcha för slutpunkten:

* HTTP/S. 
* FTP/S. 
* Web-distribution.
* Fjärrfelsökning.

![ILB ASE][2]

Om du har en ILB ASE är IP-adressen för ILB slutpunkten för HTTP/S, FTP-/ S, web distribution och fjärrfelsökning.

Åtkomstportar normal app är:

| Användning | Från | Till |
|----------|---------|-------------|
|  HTTP/HTTPS  | Kan konfigureras av användaren |  80, 443 |
|  FTP/FTPS    | Kan konfigureras av användaren |  21, 990, 10001-10020 |
|  Visual Studio fjärrfelsökning  |  Kan konfigureras av användaren |  4016, 4018, 4020, 4022 |

Detta är SANT om du är på en extern ASE eller på en ILB ASE. Om du är på en extern ASE nådde dessa portar på det offentliga VIP. Om du är på en ILB ASE nådde dessa portar på ILB. Om du kan låsa port 443, kan det finnas en effekt på vissa funktioner som exponeras i portalen. Mer information finns i [Portal beroenden](#portaldep).

## <a name="ase-subnet-size"></a>ASE undernätets storlek ##

Storleken på det undernät som används som värd för en ASE kan inte ändras när ASE har distribuerats.  ASE använder en adress för varje infrastrukturrollen samt för varje plan isolerade App Service-instans.  Dessutom är 5 adresser som används av Azure-nätverk för varje undernät som har skapats.  En ASE med inga programtjänstplaner använder alls 12 adresser innan du skapar en app.  Om det är en ILB ASE sedan använder 13 adresser innan du skapar en app i den ASE. När du skalar upp planer för App-tjänsten kräver ytterligare adresser för varje klient som har lagts till.  Som standard läggs frontservrar för varje 15 totala App Service-plan instanser. 

   > [!NOTE]
   > Inget annat kan finnas i undernätet men ASE. Se till att välja ett adressutrymme som gör det möjligt för framtida tillväxt. Du kan inte ändra denna inställning senare. Vi rekommenderar en storlek på `/25` med 128-adresser.

## <a name="ase-dependencies"></a>ASE beroenden ##

En ASE inkommande åtkomst beroende är:

| Användning | Från | Till |
|-----|------|----|
| Hantering | App Service management-adresser | ASE undernät: 454, 455 |
|  ASE intern kommunikation | ASE undernät: alla portar | ASE undernät: alla portar
|  Tillåt Azure belastningsutjämnare inkommande | Azure-belastningsutjämnare | ASE undernät: alla portar
|  Appen tilldelade IP-adresser | App som har tilldelats adresser | ASE undernät: alla portar

Inkommande trafik innehåller kommandot och kontroll av ASE förutom systemövervakning av. Käll-IP-adresser för den här trafiken visas i den [ASE Management adresser] [ ASEManagement] dokumentet. Nätverkssäkerhetskonfigurationen måste tillåta åtkomst från alla IP-adresser på port 454 och 455.

I det finns många portar som används för interna komponenten ASE undernätet kan kommunikation och de ändra.  Detta kräver att alla portar i ASE undernätet som ska vara tillgänglig från undernätet som ASE. 

För kommunikationen mellan Azure belastningsutjämnare och ASE undernätet är de portar som måste vara öppna minst 454 och 455 16001. 16001 port används för keep alive trafik mellan belastningsutjämnaren och ASE. Om du använder en ILB ASE och sedan kan du låsa trafik till bara 454, 455, 16001 portar.  Om du använder en extern ASE måste du ta hänsyn till åtkomstportar normal app.  Om du använder app som har tilldelats adresser måste du öppna den på alla portar.  När en adress har tilldelats en viss app kan använda portar som inte är kända av i förväg skicka HTTP och HTTPS-trafik till ASE belastningsutjämnaren.

Om du använder app tilldelade IP-adresser som du vill tillåta trafik från IP-adresser som är tilldelade till dina appar ASE-undernät.

En ASE beror på flera externa system för utgående åtkomst. Dessa beroenden system definieras med DNS-namn och mappas inte till en fast uppsättning IP-adresser. ASE kräver därför utgående åtkomst från undernätet som ASE till alla externa IP-adresser i olika portar. En ASE har följande utgående beroenden:

| Användning | Från | Till |
|-----|------|----|
| Azure Storage | ASE undernät | Table.Core.Windows.NET, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 krävs endast för ASEv1.) |
| Azure SQL Database | ASE undernät | Database.Windows.NET: 1433, 11000 11999, 14000 14999 (Mer information finns i [SQL Database V12 portar används](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).)|
| Azure-hantering | ASE undernät | management.core.windows.net, management.azure.com: 443 
| SSL-certifikatverifiering |  ASE undernät            |  OCSP.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | ASE undernät            |  Internet: 443
| App Service-hantering        | ASE undernät            |  Internet: 443
| Azure DNS                     | ASE undernät            |  Internet: 53
| ASE intern kommunikation    | ASE undernät: alla portar |  ASE undernät: alla portar

Om ASE förlorar åtkomst till dessa beroenden, slutar fungera. När det händer tillräckligt länge kan har ASE pausats.

### <a name="customer-dns"></a>Kunden DNS ###

Om det virtuella nätverket konfigureras med en kunddefinierade DNS-server, använda klienternas arbetsbelastningar. ASE fortfarande behöver kommunicera med Azure DNS för hanteringsändamål. 

Om det virtuella nätverket konfigureras med en kund DNS på andra sidan av en VPN-anslutning, måste DNS-servern vara nåbar från det undernät som innehåller ASE.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portalen beroenden ##

Förutom funktionella beroenden ASE finns några extra artiklar som rör portal upplevelsen. Några av funktionerna i Azure-portalen är beroende av direkt åtkomst till _SCM plats_. Det finns två URL: er för varje app i Azure App Service. Den första Webbadressen är att komma åt din app. URL som andra är att komma åt webbplatsen SCM, som också kallas den _Kudu konsolen_. Funktioner som använder webbplatsen SCM inkluderar:

-   Webjobs.
-   Functions
-   Loggströmning
-   Kudu
-   Tillägg
-   Processutforskaren
-   Konsolen

När du använder en ILB ASE den SCM platsen inte är tillgänglig utanför det virtuella nätverket internet. När din app finns i en ASE ILB, fungerar inte vissa funktioner från portalen.  

Många av de funktioner som är beroende av SCM-platsen finns också direkt i Kudu-konsolen. Du kan ansluta till den direkt i stället för med hjälp av portalen. Om din app finns i en ASE ILB, kan du använda dina publishing autentiseringsuppgifter för att logga in. URL för åtkomst till webbplatsen SCM för en app som finns i en ASE ILB har följande format: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Om din ILB ASE är domännamnet *contoso.net* och appnamnet på din är *testapp*, appen har nåtts på *testapp.contoso.net*. SCM-platsen som medföljer den nås vid *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funktioner och webjobs. ##

Både funktioner och webb-jobb beror på vilken SCM-plats men stöds för användning i portalen, även om dina appar finns i en ASE ILB, så länge din webbläsare kan nå SCM-platsen.  Om du använder ett självsignerat certifikat med ILB-ASE behöver du aktivera webbläsaren ska lita på certifikatet.  Måste vara i arkivet med betrodda datorn IE och kant som innebär att certifikatet.  Om du använder Chrome sedan det innebär att tidigare godkänt certifikatet i webbläsaren genom att träffa förmodligen scm plats direkt.  Den bästa lösningen är att använda ett kommersiella certifikat som är i webbläsaren förtroendekedja för.  

## <a name="ase-ip-addresses"></a>ASE IP-adresser ##

En ASE har några IP-adresser som du bör känna till. De är:

- **Offentliga IP-adressen för inkommande**: används för appen trafik i en extern ASE och hantering av trafik i både en extern ASE och en ILB ASE.
- **Utgående offentliga IP-Adressen**: används som ”från”-IP för utgående anslutningar från ASE som lämnar VNet som inte dirigeras av en VPN-anslutning.
- **ILB IP-adress**: Om du använder en ILB ASE.
- **App-tilldelade IP-baserade SSL-adresser**: bara möjligt med en extern ASE och IP-baserade SSL har konfigurerats.

Alla IP-adresserna är enkelt synliga i en ASEv2 i Azure-portalen från ASE-Gränssnittet. Om du har en ILB ASE visas IP-Adressen för ILB.

![IP-adresser][3]

### <a name="app-assigned-ip-addresses"></a>App-tilldelad IP-adresser ###

Du kan tilldela IP-adresser till enskilda appar med en extern ASE. Du kan göra det med en ILB ASE. Mer information om hur du konfigurerar din app om du vill ha sin egen IP-adress finns [binda ett befintligt anpassat SSL-certifikat till Azure-webbappar](../app-service-web-tutorial-custom-ssl.md).

När en app har sin egen IP-baserade SSL-adress, reserverar ASE två portar för att mappa till IP-adressen. En port är för HTTP-trafik och andra porten för HTTPS. De portarna som visas i Användargränssnittet för ASE i avsnittet IP-adresser. Trafik måste kunna nå dessa portar från VIP eller appar som inte är tillgängligt. Det här kravet är viktigt att tänka på när du konfigurerar Nätverkssäkerhetsgrupper (NSG: er).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper ##

[Nätverkssäkerhetsgrupper] [ NSGs] ger möjlighet att styra nätverksåtkomst inom ett VNet. När du använder portalen, finns det en implicit neka-regeln på lägst prioritet neka allt. Du skapar är en Tillåt-regler.

I en ASE inte åtkomst till de virtuella datorerna som används som värd för ASE sig själv. De är i en Microsoft-hanterad prenumeration. Om du vill begränsa åtkomsten till appar på ASE ange NSG: er på undernätet ASE. Gör det måste vara försiktig uppmärksam på ASE beroenden. Om du blockerar eventuella beroenden slutar i ASE fungera.

NSG: er kan konfigureras via Azure portal och PowerShell. Den här informationen visar Azure-portalen. Du skapar och hanterar NSG: er i portalen som en översta resurs under **nätverk**.

När kraven på inkommande och utgående beaktas, bör de NSG: er likna NSG: er som visas i det här exemplet. VNet-adressintervallet är _192.168.250.0/23_, och undernät som ASE är _192.168.251.128/25_.

De två första inkommande kraven för ASE ska fungera som visas överst i listan i det här exemplet. De ASE hantering och Tillåt ASE att kommunicera med sig själv. De andra posterna alla konfigurerbara klient och kan styra nätverksåtkomst till program ASE-värd. 

![Inkommande säkerhetsregler][4]

En standardregel kan IP-adresser i virtuella nätverk tala med ASE undernätet. En annan standardregel som aktiverar belastningsutjämnare, även kallat det offentliga VIP att kommunicera med ASE. Om du vill se standardreglerna, Välj **standard regler** bredvid den **Lägg till** ikon. Om du placerar en neka allt annat regel när NSG: N regler visas kan du förhindra att trafik mellan VIP och ASE. Lägga till egna regel som tillåter inkommande för att förhindra att trafik som kommer från inuti VNet. Med ett mål för en datakälla som är lika med AzureLoadBalancer **alla** och ett portintervall på **\***. Eftersom NSG regeln tillämpas på undernätet ASE, behöver du inte måste vara specifikt i målet.

Om som din app tilldelats en IP-adress, kontrollerar du att de portar hålls öppna. Om du vill se vilka portar, Välj **Apptjänstmiljö** > **IP-adresser**.  

Alla objekt som visas i följande utgående regler behövs, förutom det sista objektet. De aktivera nätverksåtkomst till ASE beroenden som noterades tidigare i den här artikeln. Om du blockerar någon av dem kan slutar din ASE fungera. Det sista objektet i listan kan din ASE att kommunicera med andra resurser i ditt VNet.

![Utgående säkerhetsregler][5]

När dina NSG: er har definierats kan du tilldela dem till undernät som din ASE på. Om du inte kommer ihåg ASE VNet eller undernät, kan du se den från ASE portalsidan. Gå till undernätet för Gränssnittet för att tilldela undernätet NSG: N och välj NSG: N.

## <a name="routes"></a>Vägar ##

Tvingad tunneling är när du ställer in vägar på ditt VNet så att den utgående trafiken inte gå direkt till internet men någon annanstans som en ExpressRoute-gateway eller en virtuell installation.  Om du behöver konfigurera din ASE på ett sådant sedan läsa dokumentet på [konfigurera din Apptjänst-miljö med Tvingad tunneltrafik][forcedtunnel].  Det här dokumentet visar alternativ som fungerar med ExpressRoute och Tvingad tunneling.

När du skapar en ASE i portalen skapa vi också en uppsättning vägtabeller i undernätet som skapas med ASE.  Dessa vägar att helt enkelt att skicka utgående trafik direkt till internet.  
Följ dessa steg om du vill skapa samma vägar manuellt:

1. Gå till Azure-portalen. Välj **nätverk** > **routningstabeller**.

2. Skapa en ny routningstabell i samma region som ditt VNet.

3. Inom din routningstabellen UI, Välj **vägar** > **Lägg till**.

4. Ange den **nästa hopptyp** till **Internet** och **adressprefixet** till **0.0.0.0/0**. Välj **Spara**.

    Då visas något som liknar följande:

    ![Funktionella vägar][6]

5. När du har skapat den nya vägtabellen går du till det undernät som innehåller din ASE. Välj din routningstabellen från listan i portalen. När du sparar ändringen ska du sedan se NSG: er och vägar som anges med ditt undernät.

    ![NSG: er och vägar][7]

## <a name="service-endpoints"></a>Serviceslutpunkter ##

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstens slutpunkter i dokumentationen [Tjänstens slutpunkter för virtuella nätverk][serviceendpoints]. 

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
