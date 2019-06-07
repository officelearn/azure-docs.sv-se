---
title: Nätverk - funktioner för Azure App Service | Microsoft Docs
description: Hur du använder olika App Service-nätverksfunktioner
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498956"
---
# <a name="app-service-networking-features"></a>Funktioner för App Service

Program i Azure App Service kan distribueras på flera olika sätt. Som standard nå Apptjänst värdbaserade appar direkt är åtkomlig via internet och kan endast internet finns slutpunkter. Många kundprogram måste dock att styra inkommande och utgående nätverkstrafik. Det finns flera funktioner som är tillgängliga i App Service för att uppfylla dessa behov. Utmaningen är att veta vilken funktion som ska användas för att lösa ett givet problem. Det här dokumentet är avsett att hjälpa kunder att avgöra vilken funktion som ska användas baserat på några exempel på användningsområden.

Det finns två primära distributionstyper för Azure App Service. Det finns flera innehavare offentlig tjänst som är värd för App Service-planer i kostnadsfri, delad, Basic, Standard, Premium och Premiumv2 priser för SKU: er. Sedan är den enda klienten App Service-miljö(ase), som är värd för isolerade SKU App Service-planer direkt i Azure Virtual Network (VNet). De funktioner du använder varierar på om du är i tjänsten flera innehavare eller i en ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funktioner för flera innehavare App Service 

Azure App Service är ett distribuerat system. De roller som hanterar inkommande HTTP/HTTPS-begäranden kallas klientdelar. De roller som värd för arbetsbelastningen kunden kallas arbetare. Alla roller i en App Service-distribution finns i ett nätverk med flera innehavare. Eftersom det finns många olika kunder i samma skalningsenhet för App Service kan ansluta du inte App Service-nätverket direkt till nätverket. I stället för att ansluta nätverken, behöver vi funktioner för att hantera olika aspekter av programmets kommunikation. De funktioner som hanterar begäranden till din app kan inte användas för att lösa problem när du gör anrop från din app. På samma sätt kan kan inte de funktioner som löser problem för anrop från din app användas för att lösa problem i din app.  

| Inkommande funktioner | Utgående funktioner |
|---------------------|-------------------|
| App tilldelats adress | Hybridanslutningar |
| Åtkomstbegränsningar | Gateway krävs för VNet-integrering |
| Serviceslutpunkter | VNet-Integration (förhandsversion) |

Om inte annat anges kan alla funktioner användas tillsammans. Du kan blanda funktioner för att lösa dina problem.

## <a name="use-case-and-features"></a>Användningsfall och funktioner

För alla angivna användningsfall, kan det finnas ett antal sätt att lösa problemet.  Funktionen rätt att använda är ibland på grund av orsaker som inte bara användningsfallet själva. Följande inkommande användningsfall föreslår hur du använder App Service nätverksfunktioner för att lösa problem när det gäller hur du styr trafiken till din app. 
 
| Inkommande användningsfall | Funktion |
|---------------------|-------------------|
| Stöd för IP-baserad SSL behoven för din app | App tilldelats adress |
| Inte delad, dedikerad inkommande adress för din app | App tilldelats adress |
| Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser | Åtkomstbegränsningar |
| Exponera min app på privata IP-adresser i mitt virtuella nätverk | ILB ASE </br> Application Gateway med Tjänsteslutpunkter |
| Begränsa åtkomsten till min app från resurser i ett virtuellt nätverk | Serviceslutpunkter </br> ILB ASE |
| Exponera min app på en privat IP-adress i mitt virtuella nätverk | ILB ASE </br> privata IP-Adressen för inkommande på en Programgateway med Tjänsteslutpunkter |
| Skydda min app med en WAF | Application Gateway + ILB ASE </br> Application Gateway med Tjänsteslutpunkter </br> Azure ytterdörren med åtkomstbegränsningar |
| Belastningsutjämna trafik till Mina appar i olika regioner | Azure ytterdörren med åtkomstbegränsningar | 
| Belastningsutjämna trafik i samma region | Application Gateway med Tjänsteslutpunkter | 

Följande utgående användningsfall föreslår hur du använder App Service nätverksfunktioner för att lösa utgående åtkomst behoven för din app. 

| Utgående användningsfall | Funktion |
|---------------------|-------------------|
| Komma åt resurser i ett virtuellt Azure-nätverk i samma region | VNET-integration </br> ASE |
| Komma åt resurser i ett virtuellt Azure-nätverk i en annan region | Gateway krävs för VNet-integrering </br> ASE- och VNet-peering |
| Få åtkomst till resurser som säkrats med Tjänsteslutpunkter | VNET-integration </br> ASE |
| Komma åt resurser i ett privat nätverk som inte är ansluten till Azure | Hybridanslutningar |
| Komma åt resurser i ExpressRoute-kretsar | VNet-Integration (begränsat till RFC 1918 adresser för tillfället) </br> ASE | 


### <a name="default-networking-behavior"></a>Standardbeteendet för nätverk

Azure App Service-skalningsenheter stöder många kunder i varje distribution. Med prenumerationerna kostnadsfri och delad SKU värd slutkundsarbetsbelastningar på arbetare för flera innehavare. Grundläggande och senare planer värd kundens arbetsbelastningar som arbetar heltid med endast en App Service-plan (ASP). Om du har en Standard App Service-plan, kommer alla appar i den här planen köras på samma worker. Om du skalar ut arbetaren sedan replikeras alla appar från att ASP på en ny arbetsprocess för varje instans i ASP. Arbetare som används för Premiumv2 skiljer sig från arbetare som används för andra planer. Varje App Service-distribution har en IP-adress som används för all inkommande trafik till appar i den App Service-distributionen. Det finns dock var som helst från 4 till 11-adresser som används för att göra utgående samtal. De här adresserna som delas av alla appar i den App Service-distributionen. Utgående adresser är olika baserat på de olika worker-typerna. Det innebär att de adresser som används av kostnadsfri, delad, Basic, Standard och Premium ASP skiljer sig från de adresser som används för utgående samtal från Premiumv2-ASP. Om du tittar i egenskaperna för din app, visas de inkommande och utgående adresser som används av din app. Om du vill låsa ett beroende med en IP-ACL kan använda possibleOutboundAddresses. 

![Appegenskaper](media/networking-features/app-properties.png)

App Service har ett antal slutpunkter som används för att hantera tjänsten.  Dessa adresser har publicerats i ett separat dokument och finns också i taggen AppServiceManagement IP-tjänsten. Taggen AppServiceManagement används bara med en App Service Environment (ASE) där du vill tillåta sådan trafik. App Service inkommande adresser spåras i AppService IP-taggen för tjänsten. Det finns ingen IP-service-tagg som innehåller de utgående adresser som används av App Service. 

![Inkommande och utgående diagram för App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>App tilldelats adress 

Funktionen app tilldelade adressen är en offshoot för IP-baserad SSL-kapaciteten och är tillgänglig genom att konfigurera SSL med din app. Den här funktionen kan användas för IP-baserad SSL-anrop men det kan också användas för att ge din app en adress som endast har. 

![Appen tilldelas adress diagram](media/networking-features/app-assigned-address.png)

När du använder en app som tilldelats adress passerar trafiken fortfarande samma frontend roller som hanterar all inkommande trafik till App Service-skalningsenhet. Den adress som har tilldelats din app men används endast av din app. Användningsområden för den här funktionen är att:

* Stöd för IP-baserad SSL behoven för din app
* Ange en dedikerad adress för din app inte delas med något annat

Du kan lära dig hur du anger en adress på din app med självstudiekursen för [konfigurera IP-baserad SSL][appassignedaddress]. 

### <a name="access-restrictions"></a>Åtkomstbegränsningar 

De åtkomstbegränsningar funktionen kan du filtrera **inkommande** förfrågningar baserat på organisationens IP-adress. Åtgärden som filtrerande äger rum på de frontend roller som är överordnad från worker rullar där dina appar körs. Eftersom klientdelen rollerna är överordnad från ”arbetarna”, kan funktionen åtkomstbegränsningar anses skydd på nätverksnivå för dina appar. Funktionen kan du skapa en lista över tillåta och neka-Adressblock som utvärderas i prioritetsordning. Det liknar funktionen Nätverkssäkerhetsgrupp (NSG) som finns i Azure Networking.  Du kan använda den här funktionen i en ASE eller i flera innehavare-tjänsten. När den används med en ILB ASE, kan du begränsa åtkomsten från privata-Adressblock.

![Åtkomstbegränsningar](media/networking-features/access-restrictions.png)

Funktionen åtkomstbegränsningar hjälper i scenarier där du vill begränsa IP-adresser som kan användas för att nå din app. Bland användning är för den här funktionen:

* Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser 
* Begränsa åtkomsten till kommer via en belastningsutjämning tjänst, till exempel Azure ytterdörren. Om du vill låsa den inkommande trafiken till Azure ytterdörren skapa regler för att tillåta trafik från 147.243.0.0/16 och 2a01:111:2050:: / 44. 

![Åtkomstbegränsningar med ytterdörren](media/networking-features/access-restrictions-afd.png)

Om du vill låsa åtkomsten till din app så att den endast kan nås från resurser i Azure Virtual Network (VNet) måste en statisk offentlig adress på det källan finns i ditt virtuella nätverk. Om resurserna som inte har en offentlig adress, bör du istället använda funktionen tjänstslutpunkter. Lär dig hur du aktiverar den här funktionen med självstudien på [konfigurera åtkomstbegränsningar][iprestrictions].

### <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänstslutpunkter kan du låsa **inkommande** åtkomst till din app så att källadressen måste komma från en uppsättning undernät som du väljer. Den här funktionen fungerar tillsammans med IP-åtkomstbegränsningar. Tjänstslutpunkter konfigureras i samma användarupplevelse som IP-åtkomstbegränsningar. Du kan skapa en tillåta/neka listan över regler för åtkomst som innehåller offentliga adresser och undernät i ditt virtuella nätverk. Den här funktionen stöder scenarier som:

![Tjänsteslutpunkter](media/networking-features/service-endpoints.png)

* Konfigurera en Programgateway med din app för att låsa inkommande trafik till din app
* Testricting åtkomst till din app till resurser i ditt virtuella nätverk. Detta kan omfatta virtuella datorer, ase-miljöer eller med andra appar som använder VNet-integrering 

![Tjänsteslutpunkter med application gateway](media/networking-features/service-endpoints-appgw.png)

Du kan lära dig mer om att konfigurera tjänstslutpunkter med din app i självstudien på [konfigurera åtkomstbegränsningar för tjänstens slutpunkt][serviceendpoints]
 
### <a name="hybrid-connections"></a>Hybridanslutningar

App Service-Hybridanslutningar kan göra **utgående** anrop till angivna TCP-slutpunkter. Slutpunkten kan vara lokalt, i ett virtuellt nätverk eller var som helst som tillåter utgående trafik till Azure på port 443. Funktionen kräver installation av en relay agent kallas Hybrid Connection Manager (HCM) på en Windows Server 2012 eller senare värd. Den HCM-system måste kunna nå Azure Relay på port 443. Den HCM-system kan laddas ned från App Service Hybrid anslutningar Användargränssnittet i portal. 

![Nätverksflödet för hybrid-anslutningar](media/networking-features/hybrid-connections.png)

Funktionen App Service-Hybridanslutningar baseras på funktionen Azure Relay-Hybridanslutningar. App Service använder en särskild typ av funktionen som stöder bara önskade utgående samtal från din app till en TCP-värd och port. Denna värd och port behöver bara matchas på värden där den HCM-system är installerad. När appen i App Service har en DNS-sökning på värd och port som definierats i din Hybridanslutning, dirigeras trafiken automatiskt om du vill gå via Hybridanslutningen och ut Hybridanslutningshanteraren. Om du vill veta mer om Hybridanslutningar, Läs i dokumentationen om [App Service-Hybridanslutningar][hybridconn]

Den här funktionen används ofta för att:

* Komma åt resurser i privata nätverk som inte är anslutna till Azure med ett VPN eller ExpressRoute
* Stöd för lift och SKIFT för lokala appar till App Service utan att även flytta stödjande databaser  
* På ett säkert sätt har åtkomst till en enda värd och port per Hybridanslutning. De flesta funktioner ger tillgång till ett nätverk och med Hybrid Connections du endast har värddator och port som du kan nå.
* Scenarier som inte motsvaras av andra metoder för utgående anslutningar
* Utföra utveckling i App Service där appar kan enkelt utnyttja lokala resurser 

Eftersom funktionen ger åtkomst till lokala resurser utan att en brandvägg för inkommande trafik hål, används det ofta av utvecklare. De andra utgående Apptjänst nätverksfunktionerna är mycket Azure virtuellt nätverk relaterade. Hybridanslutningar har inte ett beroende på gå via ett virtuellt nätverk och kan användas för en mängd olika nätverksbehov. Det är viktigt att Observera att funktionen App Service-Hybridanslutningar inte hand eller vet vad du gör ovanpå den. Det är att säga att du kan använda för åtkomst till en databas, en webbtjänst eller en godtycklig TCP-socket på en stordatorprogram. Funktionen tunnlar i stort sett TCP-paket. 

Den används också i ett flertal produktionsprogram samt Hybrid Connections är populärt för utveckling. Det är bra för att komma åt en webbtjänst eller en databas, men är inte lämplig för situationer med en bra många anslutningar som håller på att skapas. 

### <a name="gateway-required-vnet-integration"></a>Gateway krävs för VNet-integrering 

Gatewayen krävs VNet-integrering för App Service-funktionen gör det möjligt för din app ska ge **utgående** begäranden till ett Azure-nätverk. Funktionen fungerar genom att ansluta värden som din app körs på till en virtuell nätverksgateway för ditt virtuella nätverk med en punkt-till-plats-VPN. När du konfigurerar funktionen hämtar en punkt-till-plats-adresserna som tilldelats till varje instans av din app. Den här funktionen kan du komma åt resurser i klassisk eller Resource Manager-VNets i alla regioner. 

![Gateway krävs för VNet-integrering](media/networking-features/gw-vnet-integration.png)

Den här funktionen löser problemet med åtkomst till resurser i andra virtuella nätverk och kan även användas för att ansluta via ett virtuellt nätverk till andra virtuella nätverk eller lokalt. Det fungerar inte med ExpressRoute anslutna virtuella nätverk men gör med plats-till-plats VPN anslutna nätverk. Det är normalt olämpligt att använda den här funktionen från en app i en App Service Environment (ASE), eftersom ASE är redan i ditt virtuella nätverk. Användningsfall som löser den här funktionen är:

* Åtkomst till resurser på privata IP-adresser i ditt virtuella Azure-nätverk 
* Åtkomst till resurser lokalt om det finns en plats-till-plats-VPN 
* Åtkomst till resurser i peerkopplade virtuella nätverk 

När den här funktionen är aktiverad kommer appen att använda DNS-servern som målet VNet är konfigurerat med. Du kan läsa mer om den här funktionen i dokumentationen på [VNet-integrering för App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-integration

Gatewayen måste VNet-integrering-funktionen är användbar men fortfarande löser inte åtkomst till resurser över ExpressRoute. Det finns ett behov av appar för att kunna göra anrop till tjänsteslutpunkt skyddade tjänster ovanpå behöva nå via ExpressRoute-anslutningar. Kapaciteten för ett annat VNet-integrering för att lösa båda dessa ytterligare behov har lagts till. Den nya funktionen för VNet-integrering kan du placera serverdelen för din app i ett undernät i ett Resource Manager-VNet i samma region. Den här funktionen är inte tillgänglig från en App Service Environment som redan finns i ett virtuellt nätverk. Den här funktionen gör det möjligt att:

* Åtkomst till resurser i Resource Manager-VNets i samma region
* Åtkomst till resurser som är säkrade med Tjänsteslutpunkter 
* Åtkomst till resurser som är tillgängliga för ExpressRoute eller VPN-anslutningar

![VNET-integration](media/networking-features/vnet-integration.png)

Den här funktionen är en förhandsversion och ska inte användas för produktionsarbetsbelastningar. Om du vill veta mer om den här funktionen kan du läsa dokumentationen på [VNet-integrering för App Service][vnetintegration].

## <a name="app-service-environment"></a>App Service Environment 

App Service Environment (ASE) är en enskild klient-distribution i Azure App Service som körs i ditt virtuella nätverk. ASE möjliggör användningsfall som:

* Komma åt resurser i ditt virtuella nätverk
* Komma åt resurser över ExpressRoute
* Exponera dina appar med en privat adress i ditt virtuella nätverk 
* Komma åt resurser i Tjänsteslutpunkter 

Du behöver inte använda funktioner som integrering av virtuellt nätverk eller tjänstslutpunkter eftersom ASE är redan i ditt virtuella nätverk med en ASE. Om du vill få åtkomst till resurser, som SQL och lagring över tjänstslutpunkter kan du aktivera tjänstslutpunkter på ASE-undernät. Om du vill komma åt resurser i det virtuella nätverket, det finns ingen ytterligare konfiguration krävs.  Om du vill få tillgång till resurser via ExpressRoute, finns redan i det virtuella nätverket och behöver inte konfigurera något på ASE eller appar i den. 

Eftersom appar i en ILB ASE kan variabelinnehållet visas i en privat IP-adress, kan du enkelt lägga till WAF enheter om du vill exponera enbart för appar som du vill till internet och skydda resten. Det lämpar sig för enkel utveckling av program med flera nivåer. 

Det finns några saker som inte ännu är möjligt från tjänsten flera innehavare som kommer från en ase-miljö. Dessa är till exempel:

* Exponera dina appar på en privat IP-adress
* Skydda all utgående trafik med nätverkskontroller som inte är en del av din app 
* Ha dina appar i en enda klient-tjänst 
* Skala upp till många fler instanser än vad som är möjligt i tjänsten flera innehavare 
* Läsa in privata CA-klientcertifikat för användning av dina appar med privata CA skyddade slutpunkter 
* Tvinga TLS 1.1 på alla appar i systemet utan någon möjlighet att inaktivera på app-nivå 
* Ange en dedikerad utgående adress för alla appar i din ASE som inte delas med alla kunder 

![ASE i ett virtuellt nätverk](media/networking-features/app-service-environment.png)

ASE ger tillverkningskostnader runt isolerad och dedikerad appvärdfunktioner men kommer med några utmaningar för enhetshantering. Det finns vissa saker att tänka på innan du använder en operational ASE:
 
 * En ASE körs i det virtuella nätverket men har beroenden utanför det virtuella nätverket. Dessa beroenden måste tillåtas. Läs mer i [Nätverksöverväganden för App Service Environment][networkinfo]
 * En ASE skalas inte omedelbart som tjänsten flera innehavare. Du behöver förutse skalningsbehov i stället för att reaktivt skalning. 
 * En ASE har ett högre direkt kostnad som är associerade med den. För att få ut mest av din ASE, du bör planerar att placera många arbetsbelastningar i en ASE i stället att det används för små arbete
 * Appar i en ASE kan inte begränsa åtkomsten till vissa appar i en ASE och inte andra.
 * ASE är i ett undernät och alla nätverk regler gäller för all trafik till och från denna ASE. Om du vill tilldela regler för inkommande trafik för att bara en app kan använda åtkomstbegränsningar. 

## <a name="combining-features"></a>Kombinera funktioner 

De funktioner som anges för flera innehavare-tjänsten kan användas tillsammans för att lösa noggrannare användningsfall. Två av de vanliga användningsfallen beskrivs här, men de är bara exempel. Du kan lösa nästan alla dina system arkitektur behov genom att förstå de olika funktionerna gör.

### <a name="inject-app-into-a-vnet"></a>Mata in app i ett virtuellt nätverk

En begäran om vanliga är om hur du placera din app i ett virtuellt nätverk. Placera din app i ett virtuellt nätverk innebär att de inkommande och utgående slutpunkterna för en app i ett virtuellt nätverk. ASE ger den bästa lösningen för att lösa problemet, men du kan få ut mesta som behövs med i flera innehavare-tjänsten genom att kombinera funktioner. Du kan exempelvis ha endast intranätprogram med privata inkommande och utgående adresser genom att:

* Skapa en Programgateway med privat inkommande och utgående adress
* Skydda inkommande trafik till din app med Tjänsteslutpunkter 
* Använd den nya VNet-integrering så serverdelen för din app är i ditt virtuella nätverk 

Det här formatet distribution skulle inte ger dig en dedikerad adress för utgående trafik till internet och ger dig möjlighet att låsa all utgående trafik från din app.  Det här formatet distribution ger en mycket av vad du skulle bara annars får med en ASE. 

### <a name="create-multi-tier-applications"></a>Skapa program med flera nivåer

Ett flerskiktat program är ett program där API-serverdel appar endast kan nås från nivån i klientdelen. Om du vill skapa ett flerskiktat program, kan du:

* Använda VNet-integrering för att ansluta serverdelen för din webbapp på klientsidan med ett undernät i ett virtuellt nätverk
* Använda tjänstslutpunkter för att skydda inkommande trafik till din API-app till endast kommer från undernätet som används av din webbapp på klientsidan

![flerskiktat program](media/networking-features/multi-tier-app.png)

Du kan ha flera frontend-appar som använder samma API-app med hjälp av VNet-integrering från andra appar för klientdelar och tjänstslutpunkter från API-appen med sina undernät.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
