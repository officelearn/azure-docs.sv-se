---
title: Azure Virtual Datacenter - ett Nätverksperspektiv
description: Lär dig hur du skapar virtuella datacenter i Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2018
ms.author: jonor
ms.openlocfilehash: fc3f334771c11d6917e15628557adfb59051f0f6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621029"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Azure Virtual Datacenter: Ett Nätverksperspektiv
**Microsoft Azure**: flytta snabbare, spara pengar, integrera lokala appar och data

## <a name="overview"></a>Översikt
Migrerar lokala program till Azure, ger även utan betydande ändringar (en metod som kallas ”lift and shift”), organisationer fördelarna med en säker och kostnadseffektiv infrastruktur. Företag bör dock om du vill få ut det mesta av flexibiliteten möjligt med molnbaserad databehandling, utvecklas sina arkitekturer för att dra nytta av funktioner i Azure. Microsoft Azure levererar hyperskalade tjänster och infrastruktur, företagsklass funktioner och tillförlitlighet och många alternativ för hybridanslutning. Kunderna kan välja att få åtkomst till de här molntjänsterna via Internet eller med Azure ExpressRoute, som tillhandahåller privata nätverksanslutningar. Microsoft Azure-plattformen kan kunder sömlöst utöka sin infrastruktur till molnet och skapa flera nivåer arkitektur. Dessutom ger förbättrade funktioner genom att erbjuda tjänster och virtuella installationer som är optimerade för att köra i Azure med Microsoft-partner.

Den här artikeln innehåller en översikt över mönster och designer som kan användas för att lösa arkitektoniska frågor för skala, prestanda och säkerhet som många kunder står inför när de överväger att flytta till molnet. En översikt över hur du kombinerar olika organisationens IT-roller i hanteringen och styrning av systemet även beskrivs med betoning säkerhetskrav och kostnaden optimering.

## <a name="what-is-a-virtual-data-center"></a>Vad är ett virtuellt Datacenter?
Molnlösningar har utformats i dagar, för att hantera enda, relativt isolerad, program, i offentliga spektrumet. Den här metoden fungerade bra för några år. Dock är fördelarna med molnet lösningar blev tydligt och flera storskaliga arbetsbelastningar har finns på molnet, adressering säkerhet, tillförlitlighet, prestanda och kostnad frågor av distributioner på en eller fler regioner blev viktiga under hela livscykel av Molntjänsten.

I följande cloud distribution diagram visas några exempel på säkerhetsluckor (röd ram) och utrymme för optimering av virtuella nätverksinstallationer i arbetsbelastningar (gul ruta).

[![0]][0]

Virtuella datacenter (vDC) föddes ur den här nödvändigt för att skala för att stödja företagsarbetsbelastningar och behovet av att åtgärda problemet som införs när stöder storskaliga program i det offentliga molnet med.

En vDC är inte bara programbelastningar i molnet, men även nätverket, säkerhet, hantering och infrastruktur (till exempel DNS- och Directory Services). Vanligtvis finns även en privat anslutning till ett lokalt nätverk eller data center. När fler och fler arbetsbelastningar flyttar till Azure, är det viktigt att tänka på infrastruktur som stöder och objekt som dessa arbetsbelastningar är placerade i. Tänka noggrant hur resurser är strukturerade undvika ökningen av hundratals ”arbetsbelastning islands” som måste hanteras separat med oberoende dataflöde, säkerhetsmodeller och efterlevnad utmaningar.

Ett virtuellt Datacenter är i princip en samling med separata men relaterade entiteter med vanliga stödjande funktioner, funktioner och infrastruktur. Du kan spara lägre kostnad på grund av stordriftsfördelar optimerad säkerhet via komponent- och flow centralisering, tillsammans med enklare åtgärder, hantering och granskningar för efterlevnad genom att visa dina arbetsbelastningar som en integrerad vDC.

> [!NOTE]
> Det är viktigt att förstå att funktionen vDC är **inte** diskreta Azure-produkt, men en kombination av olika funktioner och möjligheter att uppfylla dina exakta krav. vDC är ett sätt att tänka kring dina arbetsbelastningar och Azure-användning för att maximera dina resurser och möjligheter i molnet. Den virtuella domänkontrollanten är därför en modulbaserad lösning på hur du bygger upp IT-tjänster i Azure och följer organisationens roller och ansvarsområden.

Funktionen vDC kan hjälpa företag att hämta arbetsbelastningar och program till Azure för följande scenarier:

-   Som är värd för flera relaterade arbetsbelastningar
-   Migrera arbetsbelastningar från en lokal miljö till Azure
-   Implementera delad eller centraliserad säkerhet och krav för åtkomst till i arbetsbelastningar
-   Blanda DevOps och centraliserade IT på lämpligt sätt för ett stort företag

Nyckeln till att låsa upp fördelarna med vDC, är en centraliserad topologi (NAV och ekrar) med en blandning av funktioner i Azure: [virtuella Azure-nätverket][VNet], [NSG: er] [ NSG], [VNet-Peering][VNetPeering], [användardefinierade vägar (UDR)][UDR], och Azure identitet med hjälp av [rollen Base Åtkomstkontroll (RBAC)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Vilka som behöver ett virtuellt Datacenter?
Alla Azure-kunder som behöver flytta mer än ett par arbetsbelastningar till Azure kan dra nytta av tänka på med hjälp av vanliga resurser. Beroende på storleken, kan även i enskilda program dra nytta av de mönster och komponenter som används för att skapa en vDC.

Om din organisation har en central IT-avdelningen, nätverk, säkerhet och/eller/efterlevnadsavdelningen team, en vDC kan hjälpa att tillämpa principen punkter, uppdelning av tull, och säkerställa enhetliga de underliggande komponenterna samtidigt programteam så mycket frihet och kontroll som passar dina behov.

Organisationer som vill DevOps kan utnyttja vDC-begrepp för att ge auktoriserade fickor för Azure-resurser och se till att de har fullständig kontroll i gruppen (prenumeration eller resursgrupp grupp i en gemensam prenumeration), men nätverket och säkerhetsgränser Följ efterlevnad som definieras av en central princip i en virtuella hubbnätverket och resursgrupp.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Överväganden om hur du implementerar ett virtuellt Datacenter
När du designar en vDC, finns det flera pivotal problem att tänka på:

-   Identitets- och katalogtjänster
-   Säkerhetsinfrastruktur
-   Anslutningen till molnet
-   Anslutningsmöjligheter inom molnet

##### <a name="identity-and-directory-service"></a>*Identitets- och katalogtjänst*
Identitets-och katalogtjänster är en viktig aspekt av alla data resurser, både lokalt och i molnet. Identitet är relaterat till alla aspekter av åtkomst och behörighet att tjänster inom vDC. För att säkerställa att endast behöriga användare och processer åtkomst till din Azure-konto och resurser, använder flera typer av autentiseringsuppgifter för autentisering i Azure. Dessa inkluderar lösenord (för att få åtkomst till Azure-konto), krypteringsnycklar, digitala signaturer och certifikat. [*Azure Multi-Factor Authentication* (MFA)] [ MFA] är ett extra lager av säkerhet för åtkomst till Azure-tjänster. Azure MFA ger stark autentisering med en rad enkla verifieringsalternativ – telefonsamtal, SMS eller mobilapp – så att kunderna kan välja den metod de föredrar.

Alla stort företag måste definiera en process för hantering av identitet som beskriver hanteringen av individuella identiteter, deras autentisering, auktorisering, roller och behörigheter inom eller mellan funktionen vDC. Målen för den här processen bör vara att öka säkerheten och produktivitet och minskade kostnader, driftstopp och repetitiva manuella uppgifter.

Enterprise/organisationer kan kräva en krävande blandning av tjänster för olika rad-för-företag (LOB) och anställda har ofta olika roller när inblandad olika projekt. En vDC kräver bra samarbete mellan olika team, var och en med specifika rolldefinitioner att hämta system som kör med bra styrning. I matrisen med ansvarsområden, åtkomst och rättigheter kan vara mycket komplexa. Identitetshantering i vDC implementeras via [ *Azure Active Directory* (AAD)] [ AAD] och rollbaserad åtkomstkontroll (RBAC).

En katalogtjänst är en delad informationsinfrastruktur för att hitta, hantera, administrera och ordna vardagliga objekt och nätverksresurser. De här resurserna kan innehålla volymer, mappar, filer, skrivare, användare, grupper, enheter och andra objekt. Varje resurs i nätverket är anses vara ett objekt med katalogservern. Information om en resurs lagras som en samling med attribut som är associerade med den resursen eller objekt.

Alla Microsoft online företagstjänster förlitar sig på Azure Active Directory (AAD) för att logga in och andra identitet. Azure Active Directory är en omfattande molnlösning för identitets- och åtkomsthantering med hög tillgänglighet som kombinerar kärnkatalogstjänster, avancerad identitetsstyrning och programåtkomsthantering. AAD kan integreras med en lokal Active Directory för att aktivera enkel inloggning för alla molnbaserade och lokalt värdbaserade (lokalt) program. Användarattributen för den lokala Active Directory kan synkroniseras automatiskt till AAD.

En enda global administratör krävs inte för att tilldela alla behörigheter i en vDC. I stället kan varje viss avdelning (eller en grupp av användare eller tjänster i katalogtjänsten) ha de behörigheter som krävs för att hantera sina egna resurser i en vDC. Strukturera behörigheter kräver belastningsutjämning. För många behörigheter kan inverka negativt på prestanda, effektivitet, och för få eller Lös behörigheter kan öka säkerhetsrisker. Azure rollbaserad åtkomstkontroll (RBAC) hjälper dig för att lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för vDC-resurser.

##### <a name="security-infrastructure"></a>*Säkerhetsinfrastruktur*
Säkerhetsinfrastruktur, i samband med en vDC beror främst på uppdelning av trafik i funktionen vDC nätverkssegment för specifika virtuella och hur du styr inkommande och utgående flöden i hela funktionen vDC. Azure baseras på arkitektur med flera innehavare som förhindrar obehörig och oavsiktlig trafik mellan distributioner, med hjälp av virtuellt nätverk (VNet)-isolering, åtkomstkontrollistor (ACL), belastningsutjämnare och IP-filter, tillsammans med trafikflödesprinciper. Network adress translation (NAT) avgränsar intern nätverkstrafik från extern trafik.

Azure-strukturen allokerar infrastrukturresurser till klientarbetsbelastningar och hanterar kommunikation till och från virtuella datorer (VM). Azure-hypervisorn tillämpar minnes- och processuppdelning mellan virtuella datorer och på ett säkert sätt skickar nätverkstrafik till gästoperativsystemens klienter.

##### <a name="connectivity-to-the-cloud"></a>*Anslutningen till molnet*
Funktionen vDC ha anslutning med externa nätverk för att erbjuda tjänster till kunder, partner och/eller interna användare. Detta innebär vanligen anslutning inte bara till Internet, utan också till lokala nätverk och datacenter.

Kunder kan skapa sina säkerhetsprinciper för att styra vad och hur specifika vDC värdbaserade tjänster som är tillgängligt till och från Internet med virtuella nätverksinstallationer (med filtrering och trafik kontroll) och anpassad routning principer och nätverket filtrering) Användardefinierad Routning och Nätverkssäkerhetsgrupper).

Företag behöver ofta att ansluta VDC till lokala Datacenter eller andra resurser. Anslutningar mellan Azure och lokala nätverk är därför en viktig aspekt när du utformar en effektiv arkitektur. Företag har två olika sätt att skapa en gränssnittet mellan vDC och lokala i Azure: överföring via Internet och/eller genom privata direkta anslutningar.

En [ **Azure plats-till-plats-VPN** ] [ VPN] är en sammanlänkning tjänst via Internet mellan lokala nätverk och vDC, kan fastställas genom säker krypterad anslutningar (IPsec/IKE-tunnlar). Azure-plats-till-plats-anslutningen är smidigt och snabbt att skapa, och kräver inte några ytterligare inköp, när alla anslutningar som ansluter via internet.

[**ExpressRoute** ] [ ExR] är en tjänst i Azure anslutning som kan du skapa privata anslutningar mellan vDC och lokala nätverk. ExpressRoute-anslutningar inte går via det offentliga Internet och erbjuda högre säkerhet, tillförlitlighet och högre hastigheter (upp till 10 Gbit/s) tillsammans med konsekvent svarstid. ExpressRoute är mycket användbart för VDC, som kunder kan få fördelarna med regler för efterlevnad som är associerade med privata anslutningar ExpressRoute.

Distribution av ExpressRoute-anslutningar innebär att engagera sig i en ExpressRoute-leverantör. För kunder som behöver för att komma igång snabbt, det är vanligt att först använda plats-till-plats-VPN för att upprätta en anslutning mellan det virtuella Domänkontrollantsobjektets och lokala resurser och migrera sedan till ExpressRoute-anslutning.

##### <a name="connectivity-within-the-cloud"></a>*Anslutningsmöjligheter inom molnet*
[Virtuella nätverk] [ VNet] och [VNet-Peering] [ VNetPeering] är de grundläggande anslutning nätverkstjänsterna i en vDC. Ett virtuellt nätverk garanterar en naturlig gräns för isolering för vDC-resurser och VNet-peering tillåter inbördes kommunikation mellan olika virtuella nätverk inom samma Azure-region eller över regioner. Kontrollera trafik i ett virtuellt nätverk och mellan virtuella nätverk måste matcha en uppsättning säkerhetsregler som angetts via åtkomstkontrollistor ([Nätverkssäkerhetsgrupp][NSG]), [virtuella nätverksinstallationer ] [ NVA], och anpassade routningstabeller ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Översikt över virtuella Datacenter

### <a name="topology"></a>Topologi
Hub och spokes modellen utökade virtuella datacenter i en enda Azure-region

[![1]][1]

Hubben är den centrala zon som styr och inspekterar inkommande och/eller utgående trafik mellan olika zoner: Internet, lokalt och ekrarna. Topologi för NAV och ekrar ger IT-avdelningen ett effektivt sätt att tillämpa säkerhetsprinciper på en central plats, samtidigt som det minskar risken för felkonfiguration och exponering.

Hubben innehåller vanliga tjänstkomponenter som förbrukas av ekrarna. Här följer några exempel på vanliga centrala tjänster:

-   Windows Active Directory-infrastruktur (med relaterade ADFS-tjänsten) krävs för autentisering av tredje part kommer åt från ej betrodda nätverk innan de får åtkomst till arbetsbelastningarna i ekern
-   En DNS-tjänst för att lösa namngivningen för arbetsbelastningen i ekrar, till åtkomst till resurser lokalt och på Internet
-   En PKI-infrastruktur för att implementera enkel inloggning på arbetsbelastningar
-   Flödeskontroll (TCP/UDP) mellan ekrar och Internet
-   Flödeskontroll mellan eker och lokalt
-   Om du vill kan flödeskontroll mellan en eker

Funktionen vDC minskar totala kostnaden med hjälp av det delade hub mellan flera ekrar.

Rollen för varje eker kan vara till värd olika typer av arbetsbelastningar. Ekrarna kan också ge en modulbaserad lösning för upprepningsbara distributioner (till exempel utveckling och testning, Acceptanstestet för användare, Förproduktion och produktion) på samma arbetsbelastningarna. Ekrarna kan också användas för att särskilja och aktivera olika grupper inom organisationen (till exempel DevOps grupper). Det är möjligt att distribuera en grundläggande arbetsbelastning eller avancerade flerskiktade arbetsbelastningar med kontroll över trafik mellan nivåerna inuti en eker.

##### <a name="subscription-limits-and-multiple-hubs"></a>Prenumerationsbegränsningar och flera hubbar
I Azure med varje komponent, oavsett vilken typ i en Azure-prenumeration. Isolering av Azure-komponenter i olika Azure-prenumerationer kan uppfylla kraven i olika Onlineindexgenerering, till exempel hur du konfigurerar differentierad nivåer av åtkomst och auktorisering.

En enda vDC kan skala upp till stort antal ekrar, även om, precis som med varje IT-system, det finns gränser för plattformar. Hubb-distribution är bunden till en specifik Azure-prenumeration, som har begränsningar och gränser (till exempel max antal VNet-peering - Se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar] [ Limits] information). I fall där gränser kan vara ett problem arkitekturen kan skalas upp ytterligare genom att utöka modellen från en enda hubb-ekrar till ett kluster med nav och ekrar. Flera hubbar i en eller flera Azure-regioner kan kopplas samman med VNet-Peering, ExpressRoute eller VPN för plats-till-plats.

[![2]][2]

Introduktionen av flera hubbar ökar kostnaden och hantering av arbetet med systemet och skulle bara motiveras av skalbarhet (exempel: system gränser eller redundans) och regionala replikering (exempel: återställning av slutanvändare prestanda eller katastrof). I scenarier som kräver flera hubbar, alla hubbar bör sträva efter att erbjuda samma uppsättning tjänster för operativa enkel.

##### <a name="interconnection-between-spokes"></a>Gränssnittet mellan ekrar
Det är möjligt att implementera komplexa arbetsbelastningar för flera nivåer i en enda eker. Konfigurationer för flera nivåer kan implementeras med undernät (en för varje nivå) i samma virtuella nätverk och filtrering flöden med NSG: er.

Å andra sidan, vilja arkitekt distribuera en arbetsbelastning på flera nivåer över flera virtuella nätverk. Med VNet-peering kan kan ekrarna ansluta till andra ekrar i samma hubb eller annan hubs. Ett typexempel på det här scenariot gäller där programmet bearbetning servrar finns i en eker (VNet), medan databasen distribueras i en annan eker (VNet). I det här fallet är det enkelt att sammankoppling ekrar med VNet-peering och därigenom undvika transporteras via hubben. En noggrann granskning av arkitektur och säkerhet bör utföras för att säkerställa att kringgå navet inte kringgå viktiga säkerhets- eller granskning punkter som bara kan finnas i navet.

[![3]][3]

Ekrar kan också samman till en eker som fungerar som en hubb. Den här metoden skapar en hierarki med två nivåer: eker i den högre nivån (nivå 0) blir hubb för lägre ekrar (nivå 1) i hierarkin. Ekrarna av vDC behöver du vidarebefordra trafik till den centrala hubben att nå antingen till lokala nätverk eller internet. En arkitektur med två hub introducerar komplex routning som tar bort fördelarna med en enkel hub-spoke-relation.

Även om Azure tillåter komplexa topologier kan är ett av huvudprinciperna för vDC konceptet repeterbarhet och enkelhet. För att minimera hanteringsarbete är enkla hub-spoke-design Referensarkitektur rekommenderade vDC.

### <a name="components"></a>Komponenter
Ett virtuellt Datacenter består av fyra grundläggande komponenttyper: **infrastruktur**, **perimeternätverk**, **arbetsbelastningar**, och **övervakning**.

Varje komponenttyp består av olika Azure-funktioner och resurser. Din vDC består av instanser av flera komponenter typer och flera varianter av samma komponenttyp. Du kan till exempel ha många olika, logiskt åtskilda arbetsbelastning-instanser som representerar olika program. Du kan använda dessa olika komponenttyper och instanser för att slutligen skapa funktionen vDC.

[![4]][4]

Föregående övergripande arkitekturen i en vDC visar olika komponenttyper som används i olika zoner topologins hubb-ekrar. Diagrammet visar infrastrukturkomponenter i olika delar av arkitekturen.

Som en bra idé (för en den lokala domänkontrollanten eller vDC) åtkomst vara rättigheter och behörigheter gruppbaserad. Hanterar grupper, kan i stället för enskilda användare underhålla åtkomstprinciper konsekvent mellan olika team och aids i minimerar konfigurationsfel. Tilldela och ta bort användare till och från relevanta grupper kan du uppdatera behörigheter för en specifik användare kontinuerligt.

Varje rollgrupp ska ha ett unikt prefix för sina namn som gör det enkelt att identifiera vilken grupp som är associerade med vilken arbetsbelastning. Exempelvis en arbetsbelastning som är värd för en autentiseringstjänst kan ha grupper med namnet *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps och AuthServiceInfraOps.* På samma sätt för centraliserad roller eller roller som inte är relaterade till en specifik tjänst, kan föregås av ”Corp” *CorpNetOps* till exempel.

Många organisationer använder en variant av följande grupper för att tillhandahålla en större uppdelning av roller:

-   Den *central IT-grupp (Corp)* har äganderätt att styra infrastruktur (till exempel nätverk och säkerhet) och därför måste ha rollen som deltagare för prenumerationen (och har kontroll över hubben) och nätverk-bidragsgivarbehörighet i ekrarna. Stor organisation ofta dela dessa management ansvar mellan flera team som; en nätverksåtgärder (CorpNetOps)-gruppen (med exklusiva fokus på nätverk) och gruppen säkerhetsåtgärder (CorpSecOps) (ansvarig för brandväggs- och säkerhetskonfiguration princip). I det specifika fallet måste två olika grupper som ska skapas för tilldelning av dessa anpassade roller.
-   Den *dev & testgrupp (AppDevOps)* har ansvaret för att distribuera arbetsbelastningar (appar eller tjänster). Den här gruppen tar rollen virtuell Datordeltagare för IaaS-distributioner och/eller en eller flera PaaS deltagare roller (se [inbyggda roller för rollbaserad åtkomstkontroll][Roles]). Du kan också kan utveckling / testning -teamet behöva synlighet för säkerhetsprinciper (NSG) och routning-principer (UDR) i navet eller en specifik eker. Därför, förutom rollerna deltagare för arbetsbelastningar, den här gruppen måste också rollen för Nätverksläsaren.
-   Den *drift och underhåll grupp (CorpInfraOps eller AppInfraOps)* ha ansvar för att hantera arbetsbelastningar i produktion. Den här gruppen måste vara prenumerationsdeltagare arbetsbelastningar i produktion prenumerationer. Vissa organisationer kan även utvärdera om de behöver en ytterligare eskalering support-teamet grupp med rollen prenumerationsdeltagare i produktion och central knutpunkt-prenumeration för att kunna åtgärda potentiella konfigurationsproblem i produktion miljö.

En vDC struktureras så att grupper som skapats för de centrala IT-grupper hantera hubben har motsvarande grupper på arbetsbelastningsnivå. Endast de centrala IT-grupperna kommer att kunna styra extern åtkomst och översta behörigheter i prenumerationen utöver att hantera hub-resurser. Dock skulle arbetsbelastningsgrupper som kunna styra resurser och behörigheter för sina virtuella nätverk separat på Central IT.

Funktionen vDC måste vara partitionerade som värd för flera projekt på ett säkert sätt mellan olika rad-för-företag (LOB). Alla projekt kräver olika isolerade miljöer (utveckling, UAT, produktion). Separata Azure-prenumerationer för var och en av dessa miljöer erbjuder fysisk isolering.

[![5]][5]

Det föregående diagrammet visar relationen mellan en organisations projekt, användare, grupper och de miljöer där Azure-komponenter har distribuerats.

Vanligtvis i IT-miljö (eller nivå) är ett system där flera program distribueras och körs. Stora företag använder en utvecklingsmiljö (där ursprungligen gjorda ändringar och testas) och en produktionsmiljö (vad slutanvändarna använder). Dessa miljöer är ofta åtskilda med flera mellanlagringsmiljöer mellan dem att tillåta fasindelad distribution (distribution), testning och återställning i händelse av problem. Distribution arkitekturer variera avsevärt, men vanligtvis den grundläggande processen för med början vid utveckling (utveckling) och slutar på produktion (PROD) fortfarande följs.

En vanlig arkitektur för dessa typer av miljöer med flera nivåer består av DevOps (utveckling och testning), UAT (mellanlagring) och produktionsmiljöer. Organisationer kan använda en eller flera Azure AD-klienter att definiera åtkomst och rättigheter till dessa miljöer. Diagrammet ovan visar ett fall där två olika Azure AD-klienter som används: en för DevOps och UAT och andra exklusivt för produktion.

Förekomsten av olika Azure AD klienter tillämpar avgränsningen mellan miljöer. Samma grupp av användare (t.ex, centrala IT) måste autentiseras med hjälp av en annan URI för att få åtkomst till en annan AD-klient ändra roller och behörigheter för antingen DevOps eller produktion miljöer för ett projekt. Förekomst av autentisering av olika användare att få åtkomst till olika miljöer minskar eventuella avbrott och andra problem som orsakas av mänskliga fel.

#### <a name="component-type-infrastructure"></a>Komponenttyp: infrastruktur
Den här komponenten är där infrastrukturen som stöder de flesta finns. Det är också där din centraliserade IT-avdelningen, säkerhet och/eller efterlevnadsteam tillbringa större delen av tiden.

[![6]][6]

Infrastrukturkomponenter ger en gränssnittet mellan de olika komponenterna i en vDC. finns i både hubben och ekrarna. Ansvaret för att hantera och underhålla infrastrukturkomponenter brukar tilldelas till centrala IT och/eller säkerhetsteamet.

En av de primära uppgifterna i IT-infrastruktur-teamet är att garantera konsekvens av IP-adress scheman i hela företaget. Privata IP-adressutrymmet som tilldelats vDC-måste vara konsekvent och överlappar inte varandra med privata IP-adresser som tilldelats i dina lokala nätverk.

Även om NAT på den lokala edge-routrar eller i Azure-miljöer kan undvika IP-adresskonflikter uppstå, läggs komplikationer till din infrastrukturkomponenter. Enkelheten med management är ett av huvudmålen med vDC, så använder NAT för att hantera IP-frågor inte är en rekommenderad lösning.

Infrastrukturkomponenter innehåller följande funktioner:

-   [**Identitets-och katalogtjänster**][AAD]. Åtkomst till alla resurstyper i Azure styrs av en identitet som lagras i en katalogtjänst. Katalogtjänsten lagrar inte bara en lista över användare, utan också åtkomsträttigheter till resurser i en specifik Azure-prenumeration. De här tjänsterna kan finnas endast molnbaserad eller de kan synkroniseras med lokala identitet som lagras i Active Directory.
-   [**Virtuellt nätverk**][VPN]. Virtuella nätverk är en av huvudkomponenterna i en vDC och gör det möjligt att skapa en gräns för isolering av trafik på Azure-plattformen. Ett virtuellt nätverk består av en eller flera virtuella nätverkssegment, var och en med ett prefix för specifika IP-nätverk (undernät). Det virtuella nätverket definierar ett internt perimeter område där IaaS-datorer och PaaS-tjänster kan upprätta privat kommunikation. Virtuella datorer (och PaaS-tjänster) i ett virtuellt nätverk inte kan kommunicera direkt med virtuella datorer (och PaaS-tjänster) i ett annat virtuellt nätverk, även om båda virtuella nätverken har skapats av samma kund, i samma prenumeration. Isolering är en viktig egenskap som ser till kundens virtuella datorer och kommunikation förblir privata inom ett virtuellt nätverk.
-   [**UDR**][UDR]. Som standard baserat på den routningstabellens standardsystem dirigeras trafik i ett virtuellt nätverk. En användardefinierad väg är en anpassad routningstabell som nätverksadministratörer kan koppla till en eller flera undernät för att skriva över beteendet för routningstabellen system och definiera en sökväg för kommunikation inom ett virtuellt nätverk. Förekomst av udr: er garanterar den utgående trafiken från eker-överföring via specifik anpassade virtuella datorer och/eller virtuella nätverksinstallationer och belastningsutjämnare som finns i hubben och ekrarna.
-   [**NSG**][NSG]. En Nätverkssäkerhetsgrupp är en lista över säkerhetsregler som fungerar som trafikfiltrering på IP-källor, IP-mål, protokoll, IP-källportar och IP-målet portar. NSG: N kan tillämpas på ett undernät, ett virtuellt nätverkskort kort som är associerade med en Azure-dator, eller båda. NSG: erna är nödvändiga för att implementera en rätt flödeskontroll i hubben och ekrarna. Vilken säkerhetsnivå som tillhandahålls av NSG: N är en funktion av vilka portar som du öppnar och i vilket syfte. Kunder bör installera ytterligare per VM-filter med värdbaserade brandväggar, till exempel IPtables eller Windows-brandväggen.
-   [**DNS**][DNS]. Namnmatchning för resurser i de virtuella nätverken i en vDC tillhandahålls via DNS. Azure tillhandahåller DNS-tjänster för både [offentliga][DNS] och [privata] [ PrivateDNS] namnmatchning. Privata zoner tillhandahåller namnmatchning både inom ett virtuellt nätverk och mellan olika virtuella nätverk. Du kan ha privata zoner som inte bara span mellan olika virtuella nätverk i samma region, men även över regioner och prenumerationer. För matchning av offentlig ger Azure DNS en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.
-   [**Prenumeration** ] [ SubMgmt] och [ **Resource Group Management**][RGMgmt]. En prenumeration definierar en naturlig gräns för att skapa flera grupper av resurser i Azure. Resurser i en prenumeration monteras tillsammans i en logisk behållare med namnet resursgrupper. Resursgruppen representerar en logisk grupp att organisera resurser i en vDC.
-   [**RBAC**][RBAC]. Via RBAC är det möjligt att kartan organisationsroller tillsammans med behörighet att komma åt specifika Azure-resurser, så att du kan begränsa användare till endast en viss delmängd av åtgärder. Med RBAC kan du bevilja åtkomst genom att tilldela rätt roll till användare, grupper och program inom relevanta omfånget. Omfattningen för en rolltilldelning kan vara en Azure-prenumeration, en resursgrupp eller en enskild resurs. RBAC kan arv av behörigheter. En roll som tilldelats på en överordnad omfattning ger också åtkomst till de underordnade objekten som ingår i detta. Med RBAC kan du hålla isär uppgifter och bevilja endast mängden åtkomst till användare som de behöver för att utföra sitt arbete. Exempelvis kan du använda RBAC för att låta en anställd hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration.
-   [**VNet-Peering**][VNetPeering]. Den grundläggande funktion som används för att skapa infrastrukturen i en vDC är VNet-Peering är en mekanism som ansluter två virtuella nätverk (Vnet) i samma region via Azure datacenternätverk eller via Azures stamnätverk i hela världen över regioner.

#### <a name="component-type-perimeter-networks"></a>Komponenttyp: Perimeternätverk
[Perimeternätverk] [ DMZ] komponenter (även kallat ett DMZ-nätverk) kan du förse nätverksanslutning med din lokala eller fysiska datacenternätverk, tillsammans med någon anslutning till och från Internet. Det är också där din nätverks- och säkerhetsfunktioner team som är sannolikt tillbringa större delen av tiden.

Inkommande paket ska flöda via funktioner för nätverkssäkerhet i hubben, till exempel brandväggen, ID: N och IP-adresser, innan de når backend-servrarna i ekrarna. Internet-bunden paket från arbetsbelastningarna bör också flödar genom säkerhetsenheter i perimeternätverket för genomförande av principer, kontroll och granskning, innan de lämnar nätverket.

Perimeter nätverkskomponenter innehåller följande funktioner:

-   [Virtuella nätverk][VNet], [UDR][UDR], [NSG][NSG]
-   [Virtuell nätverksinstallation][NVA]
-   [Belastningsutjämnare][ALB]
-   [Application Gateway][AppGW] / [WAF][WAF]
-   [Offentliga IP-adresser][PIP]

Vanligtvis är centrala IT och security team har ansvar för krav definitions- och driften av perimeternätverk.

[![7]][7]

Det föregående diagrammet visar verkställandet av två perimetrar med åtkomst till internet och ett lokalt nätverk, både fasta i hubben. I en enda hubb skala perimeternätverket till internet upp för stora mängder Onlineindexgenerering, med hjälp av flera grupper av brandväggar för webbprogram (Waf) och/eller brandväggar.

[**Virtuella nätverk** ] [ VNet] hubben bygger vanligtvis på ett virtuellt nätverk med flera undernät som värd för olika typer av tjänster som filtrering, kontrollera trafik till eller från internet via nva: er, Waf och Azure Programgatewayer.

[**UDR** ] [ UDR] med UDR kunder kan distribuera brandväggar, IDS/IPS, och andra virtuella installationer och dirigera nätverkstrafik via dessa funktioner för nätverkssäkerhet för tvingande av säkerhetsprinciper gräns granskning och granskning. Udr: er kan skapas i både hub och spokes att garantera att trafik eltransit via specifik anpassade virtuella datorer, virtuella nätverksinstallationer och belastningsutjämnare som används av funktionen vDC. En UDR måste anges i undernäten i ekern genom att ange frontend IP-adressen för den interna belastningsutjämnaren som nästa hopp för att garantera att trafik som genereras från virtuella datorer som är bosatta i eker-överföring till rätt virtuella installationer. Den interna belastningsutjämnaren distribuerar intern trafik till virtuella installationer (load balancer backend-pool).

[![8]][8]

[**Virtuella nätverksinstallationer** ] [ NVA] i hubben, hanteras normalt perimeternätverket med åtkomst till internet via en grupp av brandväggar och/eller brandväggar för webbprogram (Waf).

Olika Onlineindexgenerering använda ofta många webbprogram och programmen tenderar att drabbas av olika sårbarheter och potentiella hot. Brandväggar för program är en särskild sorts produkt som används för att identifiera attacker mot webbprogram (HTTP/HTTPS) i mer detalj än en allmän brandvägg. Waf har jämfört med tradition brandväggsteknik, en uppsättning specifika funktioner för att skydda interna servrar mot hot.

En brandvägg servergrupp är grupp brandväggar som arbetar tillsammans under samma vanliga administration, med en uppsättning säkerhetsregler för att skydda arbetsbelastningar som finns i ekrar, och kontrollera åtkomsten till lokala nätverk. En brandvägg servergrupp har mindre specialiserade programvara jämfört med en WAF, men har en bred front omfattning att filtrera och granska alla typer av trafik i ut- och ingångsanspråk. Brandväggen servergrupper implementeras normalt i Azure via virtuella nätverksinstallationer (Nva), som är tillgängliga på Azure marketplace.

Det rekommenderas att använda en uppsättning nva: er för trafik från Internet, och en annan för trafik som skickas i lokala. Med hjälp av endast en uppsättning nva: er för både utgör en säkerhetsrisk, eftersom det ger någon säkerhetsperimeter mellan de två uppsättningarna med nätverkstrafik. Med separata nva: er minskar komplexiteten för kontroll av säkerhetsregler och tydliggör vilka regler som motsvarar vilka inkommande nätverksbegäran.

De flesta stora företag hantera flera domäner. Azure DNS kan användas som värd för DNS-posterna för en viss domän. Som exempel kan Virtual IP Address (VIP) för Azure-externa belastningsutjämnaren (eller Waf) registreras i A-posten för en Azure DNS-post.

[**Azure Load Balancer** ] [ ALB] Azure load balancer erbjuder en Layer 4 (TCP, UDP)-tjänsten, som kan distribuera inkommande trafik mellan instanser av tjänsten som definierats i en belastningsutjämnad uppsättning med hög tillgänglighet. Trafik som skickas till belastningsutjämnaren från klientdelen slutpunkter (offentliga IP-slutpunkter eller privata IP-adresslutpunkter) kan distribueras med eller utan adressöversättning till en uppsättning med backend-IP-adresspool (exempel som; Virtuella nätverksinstallationer eller virtuella datorer).

Azure Load Balancer kan avsöker hälsotillståndet för de olika serverinstanserna samt och när en avsökning inte svarar slutar belastningsutjämnaren att skicka trafik till den felaktiga instansen. I en vDC har vi förekomsten av en extern belastningsutjämnare i navet (exempelvis balansera trafik till nva: er) och i ekrar (för att utföra uppgifter, som belastningsutjämning trafik mellan olika virtuella datorer i ett program med flera nivåer).

[**Application Gateway** ] [ AppGW] Microsoft Azure Application Gateway är en dedikerad virtuell installation som tillhandahåller programleveranskontroll (ADC) som en tjänst erbjuder olika layer 7 belastningsutjämning funktioner för ditt program. Det hjälper dig att optimera webbservergruppens produktivitet genom att avlasta CPU-intensiv SSL avslutning till application gateway. Här finns även andra layer 7-routningsfunktioner, till exempel resursallokeringsdistribution av inkommande trafik, cookiebaserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. En brandvägg för webbaserade program (WAF) ingår också som en del av programgatewayens WAF SKU. Denna SKU skyddar webbprogram mot vanliga säkerhetsproblem och hot på Internet. Application Gateway kan konfigureras som internetuppkopplad gateway, endast intern gateway eller en kombination av båda. 

[**Offentliga IP-adresser** ] [ PIP] vissa Azure-funktionerna kan du associera Tjänsteslutpunkter till en offentlig IP-adress som kan nås från internet till resursen. Den här slutpunkten används Network Address Translation (NAT) för att dirigera trafik till den interna adressen och porten på Azure-nätverket. Den här sökvägen är det vanligaste sättet för extern trafik till virtuella nätverk. Offentliga IP-adresser kan konfigureras för att avgöra vilken trafik som skickas och hur och var den översätts till det virtuella nätverket.

#### <a name="component-type-monitoring"></a>Komponenttyp: övervakning
Övervakning komponenter ger insyn och aviseringar från alla andra komponenter-typer. Alla team ska ha åtkomst till övervakning för komponenterna och tjänster som de har åtkomst till. Om du har en centraliserad help desk eller operations team kan måste de ha implementerat åtkomst till data som tillhandahålls av dessa komponenter.

Azure erbjuder olika typer av loggning och övervakningstjänster för att spåra beteendet för Azure värdbaserade resurser. Styrning och kontroll av arbetsbelastningar i Azure är baserat inte bara på insamling av loggdata, men också möjligheten att utlösaråtgärder baserat på specifika rapporterade händelser.

[**Azure Monitor** ] [ Monitor] -Azure innehåller flera tjänster som individuellt utför en viss roll eller en uppgift i övervakningsutrymmet. Tillsammans levererar dessa tjänster en heltäckande lösning för att samla in, analysera och agera utifrån telemetrin från dina program och de Azure-resurser som stöder dem. De kan också användas för att övervaka kritiska lokala resurser så att du får en hybridövervakningsmiljö. Det första steget i att utveckla en fullständig övervakningsstrategi för ditt program är att förstå de verktyg och data som är tillgängliga.

Det finns två huvudtyper för loggar i Azure:

-   [**Aktivitetsloggar** ] [ ActLog] (kallas även ”arbetsloggen”) ger information om åtgärder som utförts på resurser i Azure-prenumeration. De här loggarna rapporterar kontrollplanet händelser för dina prenumerationer. Varje Azure-resurs producerar granskningsloggar.

-   [**Azure diagnostikloggar** ] [ DiagLog] är loggar som genereras av en resurs som tillhandahåller omfattande, frekventa data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp.

[![9]][9]

I en vDC är det mycket viktigt att spåra loggarna NSG: er, särskilt den här informationen:

-   [**Händelseloggar**][NSGLog]: innehåller information om vilka NSG-regler tillämpas på virtuella datorer och instansroller som baseras på MAC-adress.
-   [**Prestandaräknaren loggar**][NSGLog]: spårar hur många gånger varje NSG-regel har utförts för att neka eller tillåta trafik.

Alla loggar kan lagras i Azure Storage-konton för granskning, statiska analys eller säkerhetskopiering. När loggarna lagras i ett Azure storage-konto kan kan kunder använda olika typer av ramverk för att hämta, Förbered, analysera och visualisera data för att rapportera status och hälsa av molnresurser.

Stora företag bör redan har hämtat ett standard ramverk för att övervaka lokala system och kan utöka ramverket för att integrera loggar som genereras av molndistributioner. För organisationer som vill behålla all loggning i molnet, [Log Analytics] [... / log-analytics/log-analytics-overview .md] är ett bra alternativ. Eftersom Log Analytics har implementerats som en molnbaserad tjänst kan kan du ha den igång snabbt med minsta möjliga investering i infrastrukturtjänster. Log Analytics kan också integreras med System Center-komponenter, till exempel System Center Operations Manager för att utöka din befintliga hanteringsinvestering i molnet.

Log Analytics är en tjänst i Azure som hjälper dig att samla in, korrelera, söka och agera på logg- och prestandadata data som genereras av operativsystem, program och infrastrukturkomponenter för molnet. Det ger kunderna operativa realtidsinsikter med integrerad sökning och anpassade instrumentpaneler för att analysera alla poster i alla arbetsbelastningar i en vDC.

Den [nätverk prestanda Övervakare (NPM)] [ NPM] lösningen i OMS kan ge detaljerad nätverk information slutpunkt till slutpunkt, inklusive en vy av dina Azure-nätverk och lokala nätverk. Med specifika Övervakare för ExpressRoute och offentliga tjänster.

#### <a name="component-type-workloads"></a>Komponenttyp: arbetsbelastningar
Komponenter för arbetsbelastningen är där dina faktiska program och tjänster finns. Det är också där dina program utvecklingsteam tillbringa större delen av tiden.

Arbetsbelastningen möjligheterna är oändliga verkligen. Här följer några av typerna av möjliga arbetsbelastningar:

**Intern LOB-program**

Line-of-business program är datorn kritiska för pågående åtgärder på ett företag. LOB-program har vissa gemensamma egenskaper:

-   **Interaktiv**. LOB-program är interaktiva av typen: data har angetts och returneras resultatet/rapporter.
-   **Datadriven**. LOB-program är data beräkningsintensiva ofta åtkomst till databaserna eller en annan lagringsenhet.
-   **Integrerad**. LOB-program erbjudandet integrering med andra system inom eller utanför organisationen.

**Kundinriktade webbplatser (Internet eller internt riktade)** de flesta program som interagerar med Internet är webbplatser. Azure erbjuder möjligheten att köra en webbplats på en IaaS-VM eller från en [Azure Web Apps] [ WebApps] plats (PaaS). Azure Web Apps stöd för integrering med virtuella nätverk som gör att distributionen av Web Apps i eker av en vDC. När du tittar på interna riktade webbplatser med VNET-integrering kan du behöver inte exponera en Internet-slutpunkt för dina program, men kan använda resurser via privata inte är internet dirigerbara adresser från ditt privata virtuella nätverk i stället.

**Big Data-analys** när data behöver skala upp till ett mycket stort, databaserna kan inte skalas korrekt. Hadoop-teknik ger ett system för att köra distribuerade frågor parallellt på stort antal noder. Kunderna har möjlighet att köra arbetsbelastningar i virtuella IaaS-datorer eller PaaS ([HDInsight][HDI]). HDInsight stöder distribution till en plats-baserad VNet, kan distribueras till ett kluster i en eker för funktionen vDC.

**Händelser och meddelanden**
[Azure Event Hubs] [ EventHubs] är en storskalig telemetriintegreringstjänst som samlar in, omvandlar och lagrar flera miljoner händelser. Som en distribuerad strömningsplattform erbjuder snabba svarstider och konfigurerbar kvarhållning så att du kan mata in enorma mängder telemetridata i Azure och läsa data från flera program. Med Event Hubs, kan en enda dataström stöder både i realtid och batch-baserade pipelines.

En mycket tillförlitlig molnbaserad meddelandetjänst mellan program och tjänster, kan implementeras via [Azure Service Bus] [ ServiceBus] att erbjudanden asynkrona asynkron meddelandetjänst för kommunikation mellan klient och server tillsammans med strukturerade först in först ut (FIFO) meddelanden och publicera/prenumerera på funktioner.

[![10]][10]

### <a name="multiple-vdc"></a>Flera vDC
Hittills har den här artikeln fokuserar på en enda vDC, som beskriver grundläggande komponenter och arkitektur som bidrar till en flexibel vDC. Azure-funktioner, till exempel Azure load balancer, Nva, tillgänglighetsuppsättningar, skalningsuppsättningar, tillsammans med andra mekanismer som kan ingå i ett system som gör det möjligt att skapa solid nivåer i serviceavtalet i produktionstjänster.

Men en enda vDC finns inom en enda region och är sårbar för större avbrott som kan påverka hela regionen. Kunder som vill uppnå hög serviceavtal behöver skydda tjänster via distributioner av samma projekt i två (eller fler) VDC, placeras i olika regioner.

Förutom SLA-frågor finns det flera vanliga scenarier där distribuera flera VDC rimligt:

-   Nationella inställningar/Global närvaro
-   Haveriberedskap
-   Mekanism för att minska trafiken mellan DC

#### <a name="regionalglobal-presence"></a>Nationella inställningar/Global närvaro
Azure-datacenter finns i flera regioner över hela världen. När du väljer flera Azure-datacenter, kunder bör överväga två relaterade faktorer: geografiska avstånd och svarstid. Kunder måste utvärdera geografiska avståndet mellan VDC och avståndet mellan vDC och slutanvändare att erbjuda den bästa användarupplevelsen.

Den Azure-Region där VDC finns måste också uppfylla regelkraven som upprättats av alla jurisdiktionen som din organisation fungerar.

#### <a name="disaster-recovery"></a>Haveriberedskap
Implementeringen av en plan för haveriberedskap är starkt relaterat till vilken typ av arbetsbelastning berörda och möjligheten att synkronisera arbetsbelastning tillstånd mellan olika VDC. Vi rekommenderar vill de flesta kunder synkronisera programdata mellan distributioner som körs i två olika VDC att implementera en mekanism för snabb redundans. De flesta program är känsliga för svarstid och som kan orsaka potentiella timeout och fördröjning i datasynkronisering.

Synkronisering eller heartbeat-övervakning av program i olika VDC kräver kommunikation mellan dem. Två VDC i olika regioner kan vara ansluten via:

-   VNet-Peering - VNet-Peering kan ansluta hubbar i flera regioner
-   ExpressRoute privat peering när vDC-hubbarna är anslutna till samma ExpressRoute-krets
-   flera ExpressRoute-kretsar som är anslutna via din företagets ryggrad och vDC-nät är ansluten till ExpressRoute-kretsar
-   Plats-till-plats VPN-anslutningar mellan dina vDC-hubbar per Azure-region

VNet-Peering- eller ExpressRoute-anslutningar är vanligtvis den förvalda mekanismen på grund av högre bandbredd och konsekvent fördröjning när transporteras via inom Microsofts stamnätverk.

Det finns inga magic recept att verifiera ett program som distribueras mellan två (eller fler) olika VDC som finns i olika regioner. Kunder bör köra nätverkstester kriteriet för att verifiera svarstiden och bandbredden för anslutningar och mål om synkron eller asynkron replikering är rätt och optimal återställningstid (RTO) kan vara för dina arbetsbelastningar.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mekanism för att minska trafiken mellan DC
En effektiv teknik för att omdirigera trafik inkommande i en Domänkontrollant till en annan baseras på DNS. [Med Azure Traffic Manager] [ TM] använder mekanismen System DNS (Domain Name) för att dirigera slutanvändartrafik till den lämpligaste offentliga slutpunkten i en specifik vDC. Traffic Manager söker regelbundet tjänstehälsa för offentliga slutpunkter i olika VDC via avsökningar, och vid avbrott för dessa slutpunkter dirigeras automatiskt till den sekundära vDC.

Traffic Manager fungerar på Azure-offentliga slutpunkter och kan användas, till exempel att kontrollen/minska trafiken till virtuella Azure-datorer och Web Apps i lämplig vDC. Traffic Manager kan återhämtas även körs vid en hel Azure-region misslyckas och kan styra distributionen av användartrafik för tjänstslutpunkter i olika VDC flera villkor (till exempel fel i en tjänst i en specifik vDC eller att välja vDC med den lägsta Nätverksfördröjningen för klienten).

### <a name="conclusion"></a>Sammanfattning
Virtuella Datacenter är en metod för migrering av data center i molnet som använder en kombination av funktioner och möjligheter för att skapa en skalbar arkitektur i Azure som maximerar molnet Resursanvändning, minska kostnaderna och förenkla system styrning. VDC konceptet är baserad på en hubb-ekrar topologi, tillhandahålla vanliga delade tjänster i hubben och så att specifika program/arbetsbelastningar på ekrarna. En vDC matchar strukturen för företagets roller, där olika avdelningar (centrala IT, DevOps, drift och underhåll) arbetar tillsammans med en specifik lista över roller och uppgifter. En vDC uppfyller kraven för en ”Lift and Shift”-migrering, men även ger många fördelar med att interna molndistributioner.

## <a name="references"></a>Referenser
Följande funktioner beskrivs i det här dokumentet. Klicka på länkarna för mer information.

| | | |
|-|-|-|
|Nätverksfunktioner|Belastningsutjämning|Anslutning|
|[Azure-nätverk][VNet]</br>[Nätverkssäkerhetsgrupper][NSG]</br>[NSG-loggar][NSGLog]</br>[Användardefinierad Routning][UDR]</br>[Virtuella nätverksinstallationer][NVA]</br>[Offentliga IP-adresser][PIP]</br>[DNS]|[Azure-belastningsutjämnare (L3) ][ALB]</br>[Application Gateway (L7) ][AppGW]</br>[Brandvägg för webbaserade program][WAF]</br>[Med Azure Traffic Manager][TM] |[VNet-Peering][VNetPeering]</br>[Virtuellt privat nätverk][VPN]</br>[ExpressRoute][ExR]
|Identitet</br>|Övervakning</br>|Metodtips</br>|
|[Azure Active Directory][AAD]</br>[Multifaktorautentisering][MFA]</br>[Rollen grundläggande åtkomstkontroller][RBAC]</br>[Standardroller för AAD][Roles] |[Azure Monitor][Monitor]</br>[Aktivitetsloggar][ActLog]</br>[Diagnostikloggar][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Övervakare av nätverksprestanda][NPM]|[Perimeternätverket nätverk Metodtips][DMZ]</br>[Prenumerationshantering][SubMgmt]</br>[Hantering av resursgrupp][RGMgmt]</br>[Azure-prenumerationsbegränsningar][Limits] |
|Andra Azure-tjänster|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Nästa steg
 - Utforska [VNet-Peering][VNetPeering], rejält teknik för vDC NAV och ekrar Designer
 - Implementera [AAD] [ AAD] att komma igång med [RBAC] [ RBAC] utforskning
 - Utveckla en modell för prenumeration och resursgrupp hantering och RBAC modell för att uppfylla struktur, krav, och principerna i din organisation. Den viktigaste aktiviteten planerar. Planera så mycket som användbart för omorganisering, sammanslagningar, den nya produkten rader osv.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "exempel på komponenten överlappar varandra" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "utförliga exemplet med nav och ekrar vDC"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "kluster med nav och ekrar"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "eker att ekrar"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "blockdiagram där funktionen vDC-nivå"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "användare, grupper, prenumerationer och projekt"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "övergripande infrastruktur diagrammet"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "övergripande infrastruktur diagrammet"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "VNet-Peering och perimeter-nätverk"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "Översiktsdiagram för övervakning"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "Översiktsdiagram för arbetsbelastning"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg
[DNS]: https://docs.microsoft.com/azure/dns/dns-overview
[PrivateDNS]: https://docs.microsoft.com/azure/dns/private-dns-overview
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/role-based-access-control/overview
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[NPM]: https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
