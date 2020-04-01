---
title: Nätverksfunktioner
description: Lär dig mer om nätverksfunktionerna i Azure App Service och vilka funktioner du behöver för dina nätverksbehov för säkerhet eller funktionalitet.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 79f85261115dbddcb0b04cd2863a90912de2ab87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474900"
---
# <a name="app-service-networking-features"></a>Nätverksfunktioner för App Service

Program i Azure App Service kan distribueras på flera sätt. Som standard är App Service-värdappar direkt tillgängliga för internet och kan endast nå slutpunkter för internetbaserade värden. Många kundprogram måste dock styra inkommande och utgående nätverkstrafik. Det finns flera funktioner i App-tjänsten för att tillgodose dessa behov. Utmaningen är att veta vilken funktion som ska användas för att lösa ett visst problem. Det här dokumentet är avsett att hjälpa kunderna att avgöra vilken funktion som ska användas baserat på vissa exempel på användningsfall.

Det finns två primära distributionstyper för Azure App Service. Det finns en offentlig tjänst för flera innehavare, som är värd för App Service-abonnemang i prisskonsvensken för kostnadsfria, delade, grundläggande, standard-, premium- och premiumv2-priser. Sedan har vi den enda klientapptjänstmiljön (ASE), som är värd för isolerade SKU App Service-abonnemang direkt i ditt virtuella Azure-nätverk (VNet). Vilka funktioner du använder varierar beroende på om du är i tjänsten med flera innehavare eller i en ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Nätverksfunktioner för App Service med flera innehavare 

Azure App Service är ett distribuerat system. Rollerna som hanterar inkommande HTTP/HTTPS-begäranden kallas front-ends. De roller som är värd för kundens arbetsbelastning kallas arbetare. Alla roller i en App Service-distribution finns i ett nätverk med flera innehavare. Eftersom det finns många olika kunder i samma App Service-skalningsenhet kan du inte ansluta App Service-nätverket direkt till nätverket. Istället för att ansluta nätverken behöver vi funktioner för att hantera de olika aspekterna av applikationskommunikation. De funktioner som hanterar begäranden till din app kan inte användas för att lösa problem när du ringer från din app. På samma sätt kan de funktioner som löser problem för samtal från din app inte användas för att lösa problem med din app.  

| Inkommande funktioner | Utgående funktioner |
|---------------------|-------------------|
| Tilldelad adress | Hybridanslutningar |
| Begränsningar för åtkomst | Gateway krävs VNet Integration |
| Tjänstslutpunkter | VNET-integration |

Om inget annat anges kan alla funktioner användas tillsammans. Du kan blanda funktionerna för att lösa dina olika problem.

## <a name="use-case-and-features"></a>Användningsfall och funktioner

För varje givet användningsfall kan det finnas några sätt att lösa problemet.  Rätt funktion att använda beror ibland på orsaker utöver bara själva användningsfallet. Följande inkommande användningsfall föreslår hur du använder App Service-nätverksfunktioner för att lösa problem med att styra trafik som går till din app. 
 
| Inkommande användningsfall | Funktion |
|---------------------|-------------------|
| Stöd för IP-baserade SSL-behov för din app | app tilldelad adress |
| Inte delad, dedikerad inkommande adress för din app | app tilldelad adress |
| Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser | Begränsningar för åtkomst |
| Begränsa åtkomsten till min app från resurser i ett virtuella nätverk | Tjänstslutpunkter </br> ILB ASE </br> Privat slutpunkt (förhandsgranskning) |
| Exponera min app på en privat IP i mitt virtuella nätverk | ILB ASE </br> privat IP för inkommande på en Programgateway med tjänstslutpunkter </br> Tjänstens slutpunkt (förhandsgranskning) |
| Skydda min app med en WAF | Programgateway + ILB ASE </br> Application Gateway med tjänstslutpunkter </br> Azure ytterdörr med åtkomstbegränsningar |
| Belastningsjämnad trafik till mina appar i olika regioner | Azure ytterdörr med åtkomstbegränsningar | 
| Belastningsbalanstrafik i samma region | [Application Gateway med tjänstslutpunkter][appgwserviceendpoints] | 

Följande utgående användningsfall föreslår hur du använder App Service-nätverksfunktioner för att lösa utgående åtkomstbehov för din app. 

| Ärenden om utgående användning | Funktion |
|---------------------|-------------------|
| Komma åt resurser i ett virtuellt Azure-nätverk i samma region | VNET-integration </br> Ase |
| Komma åt resurser i ett virtuellt Azure-nätverk i en annan region | Gateway krävs VNet Integration </br> ASE- och VNet-peering |
| Komma åt resurser som skyddas med tjänstslutpunkter | VNET-integration </br> Ase |
| Komma åt resurser i ett privat nätverk som inte är anslutet till Azure | Hybridanslutningar |
| Få tillgång till resurser över ExpressRoute-kretsar | VNET-integration </br> Ase | 
| Säker utgående trafik från din webbapp | VNet-integrations- och nätverkssäkerhetsgrupper </br> Ase | 
| Dirigera utgående trafik från webbappen | VNet-integrations- och flödestabeller </br> Ase | 


### <a name="default-networking-behavior"></a>Standardbeteende för nätverk

Azure App Service-skalningsenheterna stöder många kunder i varje distribution. Den kostnadsfria och delade SKU-abonnemangen är värd för kundarbetsbelastningar för arbetare med flera innehavare. Basic och ovanstående planer är värd för kundarbetsbelastningar som endast är avsedda för en App Service-plan (ASP). Om du hade en Standard App Service-plan körs alla appar i planen på samma arbetare. Om du skalar ut arbetaren replikeras alla appar i asp på en ny arbetare för varje instans i ASP. De arbetstagare som används för Premiumv2 skiljer sig från de arbetstagare som används för de andra planerna. Varje App Service-distribution har en IP-adress som används för all inkommande trafik till apparna i apptjänstens distribution. Det finns dock allt från 4 till 11 adresser som används för att ringa utgående samtal. Dessa adresser delas av alla appar i apptjänstens distribution. De utgående adresserna är olika beroende på de olika arbetartyperna. Det innebär att de adresser som används av free, shared, basic, standard och Premium ASPs skiljer sig från de adresser som används för utgående samtal från Premiumv2 ASPs. Om du tittar i egenskaperna för din app kan du se de inkommande och utgående adresser som används av din app. Om du behöver låsa ett beroende med en IP-åtkomstkontrollista använder du possibleOutboundAddresses. 

![Appegenskaper](media/networking-features/app-properties.png)

Apptjänsten har ett antal slutpunkter som används för att hantera tjänsten.  Dessa adresser publiceras i ett separat dokument och finns även i IP-tjänstmärket AppServiceManagement. AppServiceManagement-taggen används endast med en APP Service Environment (ASE) där du behöver tillåta sådan trafik. De inkommande apptjänsten-adresserna spåras i AppService IP-tjänsttaggen. Det finns ingen IP-tjänsttagg som innehåller de utgående adresser som används av App Service. 

![Diagram för inkommande och utgående apptjänst](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Tilldelad adress 

Den apptilldelade adressfunktionen är en utlöpare av DEN IP-baserade SSL-funktionen och nås genom att konfigurera SSL med din app. Den här funktionen kan användas för IP-baserade SSL-samtal, men den kan också användas för att ge din app en adress som bara den har. 

![Tilldelade adressdiagram för app](media/networking-features/app-assigned-address.png)

När du använder en app tilldelad adress går trafiken fortfarande igenom samma frontend-roller som hanterar all inkommande trafik till apptjänstens skalningsenhet. Adressen som har tilldelats din app används dock bara av din app. Användningsfallen för den här funktionen är att:

* Stöd för IP-baserade SSL-behov för din app
* Ange en dedikerad adress för din app som inte delas med något annat

Du kan lära dig hur du anger en adress i din app med självstudien om [Lägg till ett TLS/SSL-certifikat i Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Begränsningar för åtkomst 

Med funktionen Åtkomstbegränsningar kan du filtrera **inkommande** begäranden baserat på IP-adressen för ursprung. Filtreringsåtgärden utförs på frontend-roller som är uppströms från de arbetsroller där dina appar körs. Eftersom frontend-rollerna är uppströms från arbetarna kan funktionen Åtkomstbegränsningar betraktas som skydd på nätverksnivå för dina appar. Med funktionen kan du skapa en lista över tillåt och neka adressblock som utvärderas i prioritetsordning. Det liknar funktionen NSG (Network Security Group) som finns i Azure Networking.  Du kan använda den här funktionen i en ASE- eller tjänsten med flera innehavare. När den används med en ILB ASE kan du begränsa åtkomsten från privata adressblock.

![Begränsningar för åtkomst](media/networking-features/access-restrictions.png)

Funktionen Åtkomstbegränsningar hjälper till i scenarier där du vill begränsa DE IP-adresser som kan användas för att nå din app. Bland användningsfallen för den här funktionen finns:

* Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser 
* Begränsa åtkomsten till att komma via en belastningsutjämningstjänst, till exempel Azure Front Door. Om du vill låsa inkommande trafik till Azure Front Door skapar du regler för att tillåta trafik från 147.243.0.0/16 och 2a01:111:2050::/44. 

![Tillträdesbegränsningar med ytterdörr](media/networking-features/access-restrictions-afd.png)

Om du vill låsa åtkomsten till din app så att den bara kan nås från resurser i ditt virtuella Azure-nätverk (VNet) behöver du en statisk offentlig adress på vad din källa finns i ditt virtuella nätverk. Om resurserna inte har någon offentlig adress bör du använda funktionen Tjänstslutpunkter i stället. Lär dig hur du aktiverar den här funktionen med självstudien om [Konfigurera åtkomstbegränsningar][iprestrictions].

### <a name="service-endpoints"></a>Tjänstslutpunkter

Med tjänstslutpunkter kan du låsa **inkommande** åtkomst till din app så att källadressen måste komma från en uppsättning undernät som du väljer. Den här funktionen fungerar tillsammans med IP Access-begränsningarna. Tjänstslutpunkter anges i samma användarupplevelse som IP Access-begränsningarna. Du kan skapa en tillåt/neka lista över åtkomstregler som innehåller offentliga adresser samt undernät i dina virtuella nätverk. Den här funktionen stöder scenarier som:

![tjänstens slutpunkter](media/networking-features/service-endpoints.png)

* Konfigurera en programgateway med appen för att låsa inkommande trafik till din app
* Begränsa åtkomsten till din app till resurser i ditt virtuella nätverk. Detta kan omfatta virtuella datorer, ASE eller till och med andra appar som använder VNet-integrering 

![tjänstslutpunkter med programgateway](media/networking-features/service-endpoints-appgw.png)

Du kan läsa mer om hur du konfigurerar tjänstslutpunkter med din app i självstudien om [Konfigurera åtkomstbegränsningar för tjänstslutpunkt][serviceendpoints]

### <a name="private-endpoint-preview"></a>Privat slutpunkt (förhandsgranskning)

Privat slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till din Web App av Azure Private Link. Privat slutpunkt använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in webbappen i ditt virtuella nätverk. Den här funktionen är endast till för **inkommande** flöden till din webbapp.
[Använda privata slutpunkter för Azure Web App (förhandsversion)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hybridanslutningar

Med Hybridanslutningar för App Service kan dina appar ringa **utgående** samtal till angivna TCP-slutpunkter. Slutpunkten kan vara lokalt, i ett virtuella nätverk eller var som helst som tillåter utgående trafik till Azure i port 443. Funktionen kräver installation av en reläagent som kallas Hybrid Connection Manager (HCM) på en Windows Server 2012 eller nyare värd. HCM måste kunna nå Azure Relay vid port 443. HCM kan hämtas från apptjänsthybridanslutningsgränssnittet i portalen. 

![Nätverksflöde för hybridanslutningar](media/networking-features/hybrid-connections.png)

Funktionen Hybridanslutningar för App-tjänst bygger på Azure Relay Hybrid Connections-funktionen. App Service använder en specialiserad form av funktionen som bara stöder att ringa utgående samtal från din app till en TCP-värd och port. Den här värden och porten behöver bara lösa på värden där HCM är installerat. När appen i App Service gör en DNS-sökning på värden och porten som definierats i hybridanslutningen omdirigeras trafiken automatiskt för att gå igenom hybridanslutningen och ut i Hybridanslutningshanteraren. Mer information om hybridanslutningar finns i dokumentationen om [Hybridanslutningar][hybridconn] för App Service

Den här funktionen används ofta för att:

* Komma åt resurser i privata nätverk som inte är anslutna till Azure med en VPN eller ExpressRoute
* Stöd lyft och skift av lokala appar till App Service utan att behöva flytta stödjande databaser  
* Ge åtkomst till en enda värd och port per hybridanslutning på ett säkert sätt. De flesta nätverksfunktioner öppnar åtkomsten till ett nätverk och med hybridanslutningar har du bara den enda värd och port du kan nå.
* Täckscenarier som inte omfattas av andra utgående anslutningsmetoder
* Utför utveckling i App Service där apparna enkelt kan utnyttja lokala resurser 

Eftersom funktionen möjliggör åtkomst till lokala resurser utan ett inkommande brandväggshål är den populär bland utvecklare. De andra utgående App Service-nätverksfunktionerna är mycket Azure Virtual Networking-relaterade. Hybridanslutningar har inget beroende av att gå via ett virtuella nätverk och kan användas för ett bredare utbud av nätverksbehov. Det är viktigt att notera att funktionen Hybridanslutningar för App Service inte bryr sig eller vet vad du gör ovanpå den. Det vill säga att du kan använda den för att komma åt en databas, en webbtjänst eller en godtycklig TCP-socket på en stordator. Funktionen tunnlar i huvudsak TCP-paket. 

Medan Hybrid Connections är populärt för utveckling, det används också i många produktionsapplikationer också. Det är bra för att komma åt en webbtjänst eller databas, men är inte lämpligt för situationer som innebär att skapa många anslutningar. 

### <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet Integration 

Gateway krävs App Service VNet Integration funktionen gör det möjligt för din app att göra **utgående** förfrågningar till en Azure Virtual Network. Funktionen fungerar genom att ansluta värden som appen körs på till en virtuell nätverksgateway på ditt virtuella nätverk med en vpn från punkt till plats. När du konfigurerar funktionen får appen en av de punkt-till-plats-adresser som tilldelats varje instans. Med den här funktionen kan du komma åt resurser i virtuella nätverk för Klassisk eller Resurshanteraren i valfri region. 

![Gateway krävs VNet Integration](media/networking-features/gw-vnet-integration.png)

Den här funktionen löser problemet med åtkomst till resurser i andra virtuella nätverk och kan även användas för att ansluta via ett virtuella nätverk till antingen andra virtuella nätverk eller till och med lokalt. Det fungerar inte med ExpressRoute anslutna virtuella nätverk men gör med Site-to-site VPN-anslutna nätverk. Det är normalt olämpligt att använda den här funktionen från en app i en App Service Environment (ASE), eftersom ASE redan finns i ditt virtuella nätverk. De användningsfall som den här funktionen löser är:

* Komma åt resurser på privata IP-adresser i dina virtuella Azure-nätverk 
* Komma åt resurser lokalt om det finns en plats-till-plats-VPN 
* Komma åt resurser i peer-virtuella nätverk 

När den här funktionen är aktiverad använder appen den DNS-server som målet virtuella nätverk har konfigurerats med. Du kan läsa mer om den här funktionen i dokumentationen om [App Service VNet Integration][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-integration

Gateway krävs VNet Integration funktionen är mycket användbar men ändå inte lösa åtkomstresurser över ExpressRoute. Utöver behovet av att nå över ExpressRoute-anslutningar finns det ett behov av att appar kan ringa till tjänstens slutpunktssäkrade tjänster. För att lösa båda dessa ytterligare behov har en annan VNet-integrationsfunktion lagts till. Med den nya VNet-integrationsfunktionen kan du placera appens backend i ett undernät i ett resurshanteraren-nätverk i samma region. Den här funktionen är inte tillgänglig från en App Service-miljö, som redan finns i ett virtuella nätverk. Den här funktionen gör det möjligt:

* Komma åt resurser i virtuella nätverk för Resurshanteraren i samma region
* Komma åt resurser som är skyddade med tjänstslutpunkter 
* Komma åt resurser som är tillgängliga för ExpressRoute- eller VPN-anslutningar
* Skydda all utgående trafik 
* Tvinga tunnel alla utgående trafik. 

![VNET-integration](media/networking-features/vnet-integration.png)

Mer information om den här funktionen finns i dokumenten på [App Service VNet Integration][vnetintegration].

## <a name="app-service-environment"></a>App Service-miljön 

En App Service Environment (ASE) är en enda klientdistribution av Azure App Service som körs i ditt virtuella nätverk. ASE möjliggör användningsfall som:

* Komma åt resurser i ditt virtuella nätverk
* Få tillgång till resurser över ExpressRoute
* Exponera dina appar med en privat adress i ditt virtuella nätverk 
* Få tillgång till resurser över tjänstslutpunkter 

Med en ASE behöver du inte använda funktioner som VNet-integrering eller tjänstslutpunkter eftersom ASE redan finns i ditt virtuella nätverk. Om du vill komma åt resurser som SQL- eller Storage-slutpunkter aktiverar du tjänstslutpunkter i ASE-undernätet. Om du vill komma åt resurser i det virtuella nätverket krävs ingen ytterligare konfiguration.  Om du vill komma åt resurser över ExpressRoute, du är redan i det virtuella nätverket och behöver inte konfigurera något på ASE eller apps inuti den. 

Eftersom apparna i en ILB ASE kan exponeras på en privat IP-adress kan du enkelt lägga till WAF-enheter för att exponera bara de appar som du vill till internet och hålla resten säkert. Det lämpar sig för enkel utveckling av flernivåprogram. 

Det finns några saker som ännu inte är möjliga från tjänsten med flera innehavare som kommer från en ASE. Dessa inkluderar saker som:

* Exponera dina appar på en privat IP-adress
* Skydda all utgående trafik med nätverkskontroller som inte ingår i appen 
* Vara värd för dina appar i en enda klienttjänst 
* Skala upp till många fler instanser än vad som är möjligt i tjänsten med flera innehavare 
* Läsa in privata certifikatutfärdarklientcertifikat för användning av dina appar med skyddade privata certifikatutfärdare 
* Tvinga TLS 1.1 i alla appar som finns i systemet utan att kunna inaktivera på appnivå 
* Ange en dedikerad utgående adress för alla appar i din ASE som inte delas med några kunder 

![ASE i ett virtuella nätverk](media/networking-features/app-service-environment.png)

ASE ger den bästa historien kring isolerade och dedikerade app hosting men kommer med vissa utmaningar förvaltning. Några saker att tänka på innan du använder en operativ ASE är:
 
 * En ASE körs inuti ditt virtuella nätverk men har beroenden utanför det virtuella nätverket. Dessa beroenden måste tillåtas. Läs mer i [nätverksöverväganden för en App Service-miljö][networkinfo]
 * En ASE skalas inte omedelbart som tjänsten med flera innehavare. Du måste förutse skalningsbehov i stället för att skala reaktivt. 
 * En ASE har en högre up front kostnad i samband med det. För att få ut det mesta av din ASE, bör du planera att sätta många arbetsbelastningar i en ASE snarare än att ha den används för små insatser
 * Apparna i en ASE kan inte begränsa åtkomsten till vissa appar i en ASE och inte andra.
 * The ASE is in a subnet and any networking rules apply to all the traffic to and from that ASE. Om du bara vill tilldela regler för inkommande trafik för en app använder du Åtkomstbegränsningar. 

## <a name="combining-features"></a>Kombinera funktioner 

De funktioner som är kända för tjänsten med flera innehavare kan användas tillsammans för att lösa mer detaljerade användningsfall. Två av de vanligaste användningsfallen beskrivs här, men de är bara exempel. Genom att förstå vad de olika funktionerna gör kan du lösa nästan alla dina systemarkitekturbehov.

### <a name="inject-app-into-a-vnet"></a>Injicera app i ett virtuella nätverk

En vanlig begäran gäller hur du placerar din app i ett virtuella nätverk. Att placera appen i ett virtuella nätverk innebär att de inkommande och utgående slutpunkterna för en app finns i ett virtuella nätverk. ASE ger den bästa lösningen för att lösa detta problem, men du kan få det mesta av vad som behövs med i multi-tenant-tjänsten genom att kombinera funktioner. Du kan till exempel endast vara värd för intranät-program med privata inkommande och utgående adresser genom att:

* Skapa en programgateway med privat inkommande och utgående adress
* Skydda inkommande trafik till din app med tjänstslutpunkter 
* Använd den nya VNet-integreringen så att appens backend finns i ditt virtuella nätverk 

Den här distributionsstilen ger dig inte en dedikerad adress för utgående trafik till internet eller ger dig möjlighet att låsa all utgående trafik från din app.  Denna distribution stil skulle ge dig en mycket av vad du skulle bara annars få med en ASE. 

### <a name="create-multi-tier-applications"></a>Skapa program på flera nivåer

Ett program på flera nivåer är ett program där API-serverdelsapparna endast kan nås från frontend-nivån. Om du vill skapa ett program på flera nivåer kan du:

* Använda VNet-integrering för att ansluta backend för din klientapp med ett undernät i ett VNet
* Använd tjänstslutpunkter för att skydda inkommande trafik till din API-app för att bara komma från undernätet som används av din frontend-webbapp

![app på flera nivåer](media/networking-features/multi-tier-app.png)

Du kan låta flera frontend-appar använda samma API-app genom att använda VNet-integrering från andra frontend-appar och tjänstslutpunkter från API-appen med sina undernät.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
