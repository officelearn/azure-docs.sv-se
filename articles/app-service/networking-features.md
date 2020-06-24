---
title: Nätverksfunktioner
description: Lär dig mer om nätverksfunktionerna i Azure App Service och vilka funktioner du behöver för att nätverket ska ha säkerhet eller funktioner.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 87a8291387d50fae7027d007eedf49fa55b39b74
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052772"
---
# <a name="app-service-networking-features"></a>App Service nätverksfunktioner

Program i Azure App Service kan distribueras på flera sätt. Som standard är App Service värdbaserade appar direkt tillgängliga för Internet och kan bara nå värdbaserade slut punkter för Internet. Många kund program behöver dock kontrol lera inkommande och utgående nätverks trafik. Det finns flera funktioner som är tillgängliga i App Service för att uppfylla dessa behov. Utmaningen är att veta vilken funktion som ska användas för att lösa ett specifikt problem. Det här dokumentet är avsett att hjälpa kunderna att avgöra vilken funktion som ska användas baserat på några exempel på användnings fall.

Det finns två primära distributions typer för Azure App Service. Det finns en offentlig tjänst för flera innehavare som är värd för App Service planer i pris listorna kostnads fri, delad, Basic, standard, Premium och Premiumv2. Sedan finns det en enda klient App Service-miljön (ASE) som är värd för isolerade SKU App Service-planer direkt i Azure-Virtual Network (VNet). Vilka funktioner du använder beror på om du befinner dig i tjänsten för flera innehavare eller i en ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funktioner för flera innehavare App Service nätverksfunktioner 

Azure App Service är ett distribuerat system. Rollerna som hanterar inkommande HTTP/HTTPS-begäranden kallas frontend-slut. De roller som är värdar för kundens arbets belastning kallas för arbetare. Alla roller i en App Service-distribution finns i ett nätverk med flera innehavare. Eftersom det finns många olika kunder i samma App Service skalnings enhet kan du inte ansluta App Service nätverket direkt till nätverket. I stället för att ansluta nätverken behöver vi funktioner för att hantera olika aspekter av program kommunikationen. De funktioner som hanterar begär anden till din app kan inte användas för att lösa problem när du gör anrop från din app. På samma sätt kan inte de funktioner som löser problem med anrop från din app användas för att lösa problem med din app.  

| Inkommande funktioner | Utgående funktioner |
|---------------------|-------------------|
| App-tilldelad adress | Hybridanslutningar |
| Åtkomst begränsningar | Gateway krävs VNet-integrering |
| Tjänstslutpunkter | VNET-integration |

Om inget annat anges kan alla funktioner användas tillsammans. Du kan blanda funktionerna för att lösa de olika problemen.

## <a name="use-case-and-features"></a>Användnings fall och funktioner

För alla typer av användnings fall kan det finnas några sätt att lösa problemet.  Den rätta funktionen för att använda är ibland på grund av orsaker som ligger utanför själva användnings fallet. I följande fall av inkommande användning föreslås hur du använder App Service nätverksfunktioner för att lösa problem med att styra trafik till din app. 
 
| Inkommande användnings fall | Funktion |
|---------------------|-------------------|
| Stöd för IP-baserade SSL-behov för din app | app-tilldelad adress |
| Inte delad, dedikerad inkommande adress för din app | app-tilldelad adress |
| Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser | Åtkomst begränsningar |
| Begränsa åtkomsten till min app från resurser i ett virtuellt nätverk | Tjänstslutpunkter </br> ILB ASE </br> Privat slut punkt (för hands version) |
| Exponera min app på en privat IP-adress i mitt VNet | ILB ASE </br> privat IP för inkommande på en Application Gateway med tjänst slut punkter </br> Tjänst slut punkt (för hands version) |
| Skydda min app med en WAF | Application Gateway + ILB ASE </br> Application Gateway med tjänstslutpunkter </br> Azures frontend-dörr med åtkomst begränsningar |
| Belastnings Utjämnings trafik till Mina appar i olika regioner | Azures frontend-dörr med åtkomst begränsningar | 
| Belastnings Utjämnings trafik i samma region | [Application Gateway med tjänstslutpunkter][appgwserviceendpoints] | 

I följande fall av utgående användning föreslås hur du använder App Service nätverksfunktioner för att lösa utgående åtkomst behov för din app. 

| Utgående användnings fall | Funktion |
|---------------------|-------------------|
| Få åtkomst till resurser i ett Azure-Virtual Network i samma region | VNET-integration </br> ASE |
| Få åtkomst till resurser i ett Azure-Virtual Network i en annan region | Gateway krävs VNet-integrering </br> ASE och VNet-peering |
| Åtkomst till resurser som skyddas med tjänst slut punkter | VNET-integration </br> ASE |
| Åtkomst till resurser i ett privat nätverk som inte är anslutna till Azure | Hybridanslutningar |
| Få åtkomst till resurser över ExpressRoute-kretsar | VNET-integration </br> ASE | 
| Säker utgående trafik från din webbapp | VNet-integrering och nätverks säkerhets grupper </br> ASE | 
| Dirigera utgående trafik från din webbapp | VNet-integrering och routningstabeller </br> ASE | 


### <a name="default-networking-behavior"></a>Standard nätverks beteende

Azure App Service skalnings enheter har stöd för många kunder i varje distribution. De kostnads fria och delade SKU-planerna är värd för kund arbets belastningar på arbetare med flera innehavare. Basic, och över planerar kund arbets belastningar som endast är dedikerade till en App Service plan (ASP). Om du har en standard App Service plan kommer alla appar i planen att köras i samma arbets kraft. Om du skalar ut arbets tagaren replikeras alla appar i ASP-nätverket till en ny arbets tagare för varje instans i din ASP. De anställda som används för Premiumv2 skiljer sig från de anställda som används för de andra planerna. Varje App Service distribution har en IP-adress som används för all inkommande trafik till apparna i som App Service distribution. Det finns dock från 4 till 11 adresser som används för att göra utgående samtal. De här adresserna delas av alla appar i som App Service-distributionen. De utgående adresserna är olika baserat på olika typer av arbetare. Det innebär att adresserna som används av de kostnads fria, delade, grundläggande, standard-och Premium-ASP: en skiljer sig från de adresser som används för utgående anrop från Premiumv2-ASP. Om du tittar i egenskaperna för appen kan du se de inkommande och utgående adresser som används av din app. Om du behöver låsa ett beroende med en IP-ACL använder du possibleOutboundAddresses. 

![Egenskaper för app](media/networking-features/app-properties.png)

App Service har ett antal slut punkter som används för att hantera tjänsten.  De här adresserna publiceras i ett separat dokument och finns också i AppServiceManagement IP service tag. Taggen AppServiceManagement används endast med en App Service-miljön (ASE) där du behöver tillåta sådan trafik. App Service inkommande adresser spåras i AppService IP service tag. Det finns ingen IP-tjänstetagg som innehåller de utgående adresser som används av App Service. 

![App Service inkommande och utgående diagram](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>App-tilldelad adress 

Appens tilldelade adress funktion är en offshoot av IP-baserad SSL-kapacitet och används genom att konfigurera SSL med din app. Den här funktionen kan användas för IP-baserade SSL-anrop, men den kan också användas för att ge din app en adress som bara innehåller den. 

![App-tilldelat adress diagram](media/networking-features/app-assigned-address.png)

När du använder en app-tilldelad adress, går trafiken fortfarande genom samma klient dels roller som hanterar all inkommande trafik i App Service skalnings enhet. Den adress som är tilldelad till din app används dock bara av din app. Användnings fallen för den här funktionen är att:

* Stöd för IP-baserade SSL-behov för din app
* Ange en dedikerad adress för din app som inte delas med något annat

Du kan lära dig hur du anger en adress i din app med själv studie kursen om hur du [lägger till ett TLS/SSL-certifikat i Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Åtkomst begränsningar 

Med funktionen åtkomst begränsningar kan du filtrera **inkommande** förfrågningar baserat på den ursprungliga IP-adressen. Filtrerings åtgärden utförs på de frontend-roller som är överordnade från arbets rollerna där dina appar körs. Eftersom front-end-rollerna är överordnade från arbets tagarna kan funktionen åtkomst begränsningar betraktas som skydd på nätverks nivå för dina appar. Med funktionen kan du bygga en lista över tillåtna och neka-adressblock som utvärderas i prioritetsordning. Det liknar funktionen nätverks säkerhets grupp (NSG) som finns i Azure-nätverk.  Du kan använda den här funktionen i en ASE eller i tjänsten flera innehavare. När det används med en ILB-ASE kan du begränsa åtkomsten från privata adress block.

![Åtkomst begränsningar](media/networking-features/access-restrictions.png)

Funktionen åtkomst begränsningar hjälper dig i scenarier där du vill begränsa vilka IP-adresser som kan användas för att nå din app. Bland användnings exemplen för den här funktionen är:

* Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser 
* Begränsa åtkomsten till att komma via en belastnings Utjämnings tjänst, till exempel Azure-frontend. Om du vill låsa inkommande trafik till Azure-frontend skapar du regler för att tillåta trafik från 147.243.0.0/16 och 2a01:111:2050::/44. 

![Åtkomst begränsningar med front dörren](media/networking-features/access-restrictions-afd.png)

Om du vill låsa åtkomsten till din app så att den bara kan nås från resurser i din Azure-Virtual Network (VNet) behöver du en statisk offentlig adress på vilken källa som är i ditt VNet. Om resurserna inte har en offentlig adress bör du använda funktionen tjänst slut punkter i stället. Lär dig hur du aktiverar den här funktionen med självstudien om hur du [konfigurerar åtkomst begränsningar][iprestrictions].

### <a name="service-endpoints"></a>Tjänstslutpunkter

Med tjänst slut punkter kan du låsa **inkommande** åtkomst till din app så att käll adressen måste komma från en uppsättning undernät som du väljer. Den här funktionen fungerar tillsammans med begränsningar för IP-åtkomst. Tjänst slut punkter anges i samma användar upplevelse som begränsningarna för IP-åtkomst. Du kan bygga en lista över tillåtna/nekade åtkomst regler som innehåller offentliga adresser samt undernät i din virtuella nätverk. Den här funktionen stöder scenarier som:

![tjänst slut punkter](media/networking-features/service-endpoints.png)

* Konfigurera en Application Gateway med din app för att låsa inkommande trafik till din app
* Begränsa åtkomsten till din app till resurser i ditt VNet. Detta kan vara virtuella datorer, ASE eller till och med andra appar som använder VNet-integrering 

![tjänst slut punkter med Application Gateway](media/networking-features/service-endpoints-appgw.png)

Du kan lära dig mer om hur du konfigurerar tjänst slut punkter med din app i självstudien om hur du [konfigurerar åtkomst begränsningar för tjänst slut punkt][serviceendpoints]

### <a name="private-endpoint-preview"></a>Privat slut punkt (för hands version)

Privat slut punkt är ett nätverks gränssnitt som ansluter dig privat och säkert till din webbapp via en privat Azure-länk. Privat slut punkt använder en privat IP-adress från ditt virtuella nätverk, vilket på ett effektivt sätt ansluter webbappen till ditt VNet. Den här funktionen är endast för **inkommande** flöden till din webbapp.
[Använda privata slut punkter för Azure Web App (för hands version)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hybridanslutningar

App Service Hybridanslutningar gör det möjligt för dina appar att göra **utgående** anrop till angivna TCP-slutpunkter. Slut punkten kan vara lokal, i ett VNet eller var som helst som tillåter utgående trafik till Azure på port 443. Funktionen kräver installation av en relay-agent som kallas Hybridanslutningshanteraren (HCM) på en Windows Server 2012 eller en nyare värd. HCM måste kunna uppnå Azure Relay på port 443. HCM kan hämtas från App Service Hybridanslutningar gränssnittet i portalen. 

![Hybridanslutningar nätverks flöde](media/networking-features/hybrid-connections.png)

App Service Hybridanslutningar-funktionen bygger på Azure Relay Hybridanslutningar-funktionen. App Service använder en specialiserad form av funktionen som endast stöder utgående samtal från din app till en TCP-värd och port. Den här värden och porten behöver bara lösas på den värd där HCM är installerad. När appen, i App Service, en DNS-sökning på värden och porten som definierats i din hybrid anslutning, omdirigeras trafiken automatiskt för att gå igenom hybrid anslutningen och ta bort Hybridanslutningshanteraren. Läs mer om Hybridanslutningar i dokumentationen om [App Service hybridanslutningar][hybridconn]

Den här funktionen används ofta för att:

* Åtkomst till resurser i privata nätverk som inte är anslutna till Azure med en VPN-eller ExpressRoute
* Stöd för hiss och flyttning av lokala appar till App Service utan att du behöver flytta stöd databaser  
* Ge säker åtkomst till en enda värd och port per hybrid anslutning. De flesta nätverksfunktioner öppnar åtkomst till ett nätverk och med Hybridanslutningar du bara har den enda värden och port som du kan nå.
* Täck scenarier som inte omfattas av andra metoder för utgående anslutning
* Utför utveckling i App Service där apparna enkelt kan utnyttja lokala resurser 

Eftersom funktionen ger till gång till lokala resurser utan inkommande brand Väggs hål, är den populär med utvecklare. De andra utgående App Service nätverksfunktionerna är mycket virtuella Azure-nätverk relaterade. Hybridanslutningar är inte beroende av att gå igenom ett VNet och kan användas för en större mängd olika nätverks behov. Det är viktigt att Observera att App Service Hybridanslutningar-funktionen inte bryr dig om eller vet vad du gör på den. Det vill säga att du kan använda den för att få åtkomst till en databas, en webb tjänst eller en godtycklig TCP-socket på en stordator. Funktionen i huvudsak tunnel TCP-paket. 

Även om Hybridanslutningar är populär för utveckling, används även det även i flera produktions program. Det är bra att komma åt en webb tjänst eller databas, men är inte lämplig för situationer där många anslutningar skapas. 

### <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet-integrering 

Den gateway som krävs för App Service VNet-integrering gör att din app kan göra **utgående** förfrågningar till en Azure-Virtual Network. Funktionen fungerar genom att ansluta värden som din app körs på till en Virtual Network gateway på ditt VNet med en punkt-till-plats-VPN. När du konfigurerar funktionen hämtar appen en av de punkt-till-plats-adresser som tilldelats varje instans. Med den här funktionen kan du komma åt resurser i antingen klassiskt eller Resource Manager-virtuella nätverk i vilken region som helst. 

![Gateway krävs VNet-integrering](media/networking-features/gw-vnet-integration.png)

Den här funktionen löser problemet med att komma åt resurser i andra virtuella nätverk och kan även användas för att ansluta via ett VNet till antingen andra virtuella nätverk eller till och med lokalt. Den fungerar inte med ExpressRoute-anslutna virtuella nätverk men gör med plats-till-plats-VPN-anslutna nätverk. Det är normalt olämpligt att använda den här funktionen från en app i en App Service-miljön (ASE) eftersom ASE redan finns i ditt VNet. De användnings fall som den här funktionen löser är:

* Åtkomst till resurser i privata IP-adresser i dina virtuella Azure-nätverk 
* Åtkomst till resurser lokalt om det finns en plats-till-plats-VPN 
* Åtkomst till resurser i peer-virtuella nätverk 

När den här funktionen är aktive rad använder appen den DNS-server som målets VNet har kon figurer ATS med. Du kan läsa mer om den här funktionen i dokumentationen om [App Service VNet-integrering][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-integration

Den gateway som krävs för VNet-integrering är mycket användbar men matchar fortfarande inte åtkomst till resurser i ExpressRoute. Om du behöver komma åt över ExpressRoute-anslutningar finns det ett behov av att appar kan ringa till tjänstens slut punkts säkra tjänster. För att lösa båda de ytterligare behoven har en annan VNet-integrerings funktion lagts till. Med den nya funktionen för VNet-integrering kan du placera appens Server del i ett undernät i ett Resource Manager VNet i samma region. Den här funktionen är inte tillgänglig från en App Service-miljön, som redan finns i ett VNet. Den här funktionen aktiverar:

* Åtkomst till resurser i Resource Manager-virtuella nätverk i samma region
* Åtkomst till resurser som skyddas med tjänst slut punkter 
* Åtkomst till resurser som är tillgängliga via ExpressRoute eller VPN-anslutningar
* Skydda all utgående trafik 
* Tvinga tunnel trafik all utgående trafik. 

![VNET-integration](media/networking-features/vnet-integration.png)

Läs mer om den här funktionen i dokumenten på [App Service VNet-integrering][vnetintegration].

## <a name="app-service-environment"></a>App Service Environment 

En App Service-miljön (ASE) är en enda klient distribution av Azure App Service som körs i ditt VNet. ASE möjliggör användnings fall som:

* Få åtkomst till resurser i ditt VNet
* Få åtkomst till resurser över ExpressRoute
* Exponera dina appar med en privat adress i ditt VNet 
* Få åtkomst till resurser över tjänst slut punkter 

Med en ASE behöver du inte använda funktioner som VNet-integrering eller tjänst slut punkter eftersom ASE redan finns i ditt VNet. Om du vill komma åt resurser som SQL eller Storage över tjänst slut punkter aktiverar du tjänst slut punkter i ASE-undernätet. Om du vill få åtkomst till resurser i det virtuella nätverket krävs ingen ytterligare konfiguration.  Om du vill få åtkomst till resurser över ExpressRoute är du redan i VNet och behöver inte konfigurera något på ASE eller apparna inuti den. 

Eftersom appar i en ILB-ASE kan exponeras på en privat IP-adress kan du enkelt lägga till WAF-enheter för att exponera bara de appar som du vill ha på Internet och hålla resten säkra. Den lämpar sig för enkel utveckling av program på flera nivåer. 

Det finns vissa saker som ännu inte är möjliga från tjänsten för flera innehavare som kommer från en ASE. De omfattar till exempel:

* Exponera dina appar på en privat IP-adress
* Skydda all utgående trafik med nätverks kontroller som inte är en del av din app 
* Vara värd för dina appar i en enda klient tjänst 
* Skala upp till många fler instanser än vad som är möjligt i tjänsten för flera innehavare 
* Läs in privata CA-klientcertifikat som ska användas av dina appar med privat CA-säkra slut punkter 
* Framtvinga TLS 1,1 i alla appar som finns i systemet utan möjlighet att inaktivera på App-nivå 
* Tillhandahålla en dedikerad utgående adress för alla appar i din ASE som inte delas med några kunder 

![ASE i ett virtuellt nätverk](media/networking-features/app-service-environment.png)

ASE ger den bästa artikeln runt isolerad och dedikerad app-värd, men följer vissa hanterings utmaningar. Några saker att tänka på innan du använder en drifts ASE är:
 
 * En ASE körs i ditt VNet men har beroenden utanför det virtuella nätverket. Dessa beroenden måste vara tillåtna. Läs mer i [nätverks överväganden för en app service-miljön][networkinfo]
 * En ASE skalas inte direkt som tjänsten för flera innehavare. Du måste förutse skalnings behoven snarare än att skalas om. 
 * En ASE har högre fram till gångs kostnad. För att få ut mesta möjliga av din ASE bör du planera att placera många arbets belastningar i en ASE i stället för att använda den för små ansträngningar
 * Apparna i en ASE kan inte begränsa åtkomsten till vissa appar i en ASE och inte andra.
 * ASE finns i ett undernät och eventuella nätverks regler gäller för all trafik till och från den ASE. Använd åtkomst begränsningar om du vill tilldela regler för inkommande trafik för bara en app. 

## <a name="combining-features"></a>Kombinera funktioner 

De funktioner som anges för tjänsten för flera innehavare kan användas tillsammans för att lösa mer avancerade användnings fall. Två av de vanligaste användnings fallen beskrivs här, men de är bara exempel. Genom att förstå vad de olika funktionerna gör kan du lösa nästan alla system arkitektur behov.

### <a name="inject-app-into-a-vnet"></a>Mata in app i ett VNet

En gemensam begäran finns i hur du sätter din app i ett VNet. Att placera din app i ett VNet innebär att inkommande och utgående slut punkter för en app finns i ett VNet. ASE tillhandahåller den bästa lösningen för att lösa problemet, men du kan få ut mesta möjliga av vad som behövs i tjänsten flera innehavare genom att kombinera funktioner. Du kan till exempel endast vara värd för program i intranätet med privata inkommande och utgående adresser genom att:

* Skapa en Application Gateway med privat inkommande och utgående adress
* Skydda inkommande trafik till din app med tjänstens slut punkter 
* Använd den nya VNet-integreringen så att appens Server del är i ditt VNet 

Den här distributions stilen ger dig inte en dedikerad adress för utgående trafik till Internet eller ger dig möjlighet att låsa all utgående trafik från din app.  Det här distributions formatet skulle ge dig en stor del av vad du bara skulle kunna komma åt i en ASE. 

### <a name="create-multi-tier-applications"></a>Skapa program på flera nivåer

Ett program på flera nivåer är ett program där API-backend-apparna bara kan nås från klient delen. Om du vill skapa ett program med flera nivåer kan du:

* Använd VNet-integrering för att ansluta Server delen för din frontend-webbapp med ett undernät i ett virtuellt nätverk
* Använd tjänstens slut punkter för att skydda inkommande trafik till din API-app så att den endast kommer från det undernät som används av din frontend-webbapp

![app med flera nivåer](media/networking-features/multi-tier-app.png)

Du kan ha flera frontend-appar som använder samma API-app genom att använda VNet-integrering från andra frontend-appar och tjänst slut punkter från API-appen med sina undernät.  

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
