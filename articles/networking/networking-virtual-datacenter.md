---
title: 'Virtuella Microsoft Azure-Datacenter: Ett Nätverksperspektiv | Microsoft Docs'
description: Lär dig hur du skapar din virtuella datacenter i Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jonor
ms.openlocfilehash: 7fcd8e12a7109218387788e47eddad48e72797bb
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="microsoft-azure-virtual-datacenter-a-network-perspective"></a>Virtuella Microsoft Azure-Datacenter: Ett Nätverksperspektiv
**Microsoft Azure**: flytta snabbare, spara pengar, integrera lokala appar och data

## <a name="overview"></a>Översikt
Migrera lokala program till Azure, ger även utan några betydande ändringar (en metod som kallas ”lyfta och flytta”), organisationer fördelarna med en säker och kostnadseffektiv infrastruktur. Företag bör dock om du vill utnyttja flexibiliteten möjligt med cloud computing-lösningar, utvecklas sina arkitekturer för att dra nytta av funktioner i Azure. Microsoft Azure ger storskaliga tjänster och infrastruktur, funktioner i företagsklass och tillförlitlighet och många alternativ för hybridanslutning. Kunder kan välja att använda dessa molntjänster via Internet eller med Azure ExpressRoute som tillhandahåller anslutningar privata nätverk. Microsoft Azure-plattformen gör att kunder vill sömlöst utöka sin infrastruktur till molnet och skapa flera nivåer arkitekturer. Dessutom ger Microsoft-partner förbättrade funktioner genom att erbjuda tjänster och virtuella installationer som är optimerade för att köras i Azure.

Den här artikeln innehåller en översikt över mönster och design som kan användas för att lösa problem som arkitektur skala, prestanda och säkerhet många kunder står inför när du tänker flytta masse till molnet. En översikt över hur du anpassar olika organisationens IT-roller i hanteringen och styrning av systemet är också beskrivs med betoning säkerhetskrav och kostnaden optimering.

## <a name="what-is-a-virtual-data-center"></a>Vad är ett virtuella Datacenter?
Molnlösningar har utformats för värdar enda, relativt isolerade applikationer i offentliga spektrumet i Internets första dagar. Den här metoden fungerade bra för några år. Dock är fördelarna med molnet lösningar blev tydligt och flera stora arbetsbelastningar har finns i molnet, adressering säkerhet, tillförlitlighet och prestanda och kostnad gäller för distributioner i en eller flera områden blev viktiga genom hela livscykeln för Molntjänsten.

Följande molnet distribution diagrammet visar några exempel på säkerhetsbrister (röd ruta) och utrymme för optimering virtuella nätverksinstallationer över arbetsbelastningar (gul rutan).

[![0]][0]

Virtuella datacenter (vDC) föddes från detta är nödvändigt för att skala för att stödja arbetsbelastningar på företag och du behöver åtgärda de problem som införs när stöder storskaliga program i det offentliga molnet.

En vDC är inte bara programbelastningar i molnet, men även i nätverket, säkerhet, hantering och infrastruktur (till exempel DNS- och Directory Services). Vanligtvis finns även en privat anslutning till ett lokalt nätverk eller data center. När fler och fler arbetsbelastningar flyttar till Azure, är det viktigt att tänka på stödjande infrastruktur och de objekt som dessa arbetsbelastningar placeras i. Tänka noggrant hur resurser är strukturerade kan undvika den ökande mängden av hundratals ”arbetsbelastning i Oceanien och Västindien” som måste hanteras separat med oberoende dataflöde, säkerhetsmodeller och kompatibilitet utmaningar.

Ett virtuellt Datacenter är egentligen en samling av separat men relaterade entiteter med vanliga stödjande funktioner, funktioner och infrastruktur. Genom att visa dina arbetsbelastningar som en integrerad vDC, inser du minskade kostnader på grund av stordriftsfördelar optimerade säkerhet via komponenten och data flödet centralisering, tillsammans med enklare åtgärder, hantering och kompatibilitet granskningar.

> [!NOTE]
> Det är viktigt att förstå att vDC är **inte** en diskret Azure produkten, men en kombination av olika funktioner och möjligheter att uppfylla dina exakta behov. vDC är ett sätt att tänka igenom dina arbetsbelastningar och användning av Azure för att maximera dina resurser och förmågor i molnet. Den virtuella domänkontrollanten är därför en modulär metod för hur du bygger upp IT-tjänster i Azure, respektera organisationens roller och ansvarsområden.

VDC hjälper företag hämta arbetsbelastningar och program till Azure för följande scenarier:

-   Värd för flera relaterade arbetsbelastningar
-   Migrera arbetsbelastningar från en lokal miljö till Azure
-   Implementerar delade eller centraliserad säkerhet och åtkomstkraven över arbetsbelastningar
-   Blandning av DevOps och centraliserad IT korrekt för ett stort företag

Nyckeln för att låsa upp fördelarna med vDC, är en centraliserad topologi (NAV och ekrar) med en blandning av Azure-funktioner: [Azure VNet][VNet], [NSG: er][NSG], [VNet-Peering][VNetPeering], [användardefinierade vägar (UDR)][UDR], och Azure identitet med hjälp av [roll Base åtkomstkontroll (RBAC)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Som behöver ett virtuella Datacenter?
Azure kunder som behöver flytta mer än ett par arbetsbelastningar till Azure kan utnyttja tänka använder gemensamma resurser. Beroende på storleken, kan även i enskilda program dra nytta av de mönster och komponenter som används för att skapa en vDC.

Om din organisation har en central IT-avdelningen, nätverk, säkerhet och/eller/efterlevnadsavdelningen team, en vDC hjälper dig att tillämpa principen punkter, uppdelning av tull och enhetlig underliggande gemensamma komponenter medan ger programmet team så mycket frihet och kontroll som passar dina behov.

Organisationer som behöver till DevOps kan använda vDC-begrepp för att ange auktoriserade bara till Azure-resurser och se till att de har fullständig kontroll i gruppen (prenumerationen eller resursen grupp i en gemensam prenumeration), men gränserna som nätverks- och förblir kompatibla som definierats av en central princip i ett nav VNet och resursgruppen.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Överväganden om hur du implementerar ett virtuella Datacenter
När du designar en vDC, finns det flera spela en central problem att tänka på:

-   Identitets- och katalogtjänster
-   Säkerhetsinfrastruktur
-   Anslutningen till molnet
-   Anslutning i molnet

##### <a name="identity-and-directory-service"></a>*Identitets- och katalogtjänst*
Identitets- och Directory services är en viktig aspekt av alla data resurser, både lokalt och i molnet. Identitet är relaterad till alla aspekter av åtkomst och behörighet för att tjänster inom vDC. För att säkerställa att endast auktoriserade användare och processer åtkomst till dina Azure-konto och resurser, använder Azure flera typer av autentiseringsuppgifter för autentisering. Dessa inkluderar lösenord (för att få åtkomst till Azure-konto), krypteringsnycklar, digitala signaturer och certifikat. [*Azure Multi-Factor Authentication* (MFA)] [ MFA] är ett extra lager av säkerhet för åtkomst till Azure-tjänster. Azure MFA ger stark autentisering med ett antal alternativ för enkel verifiering – telefonsamtal, textmeddelande eller mobilapp, och att kunder kan välja lämplig metod.

Stora företag måste definiera en process för identiteten som beskriver hanteringen av individuella identiteter, deras autentisering, auktorisering, roller och privilegier inom eller mellan vDC. Målen för den här processen bör vara att öka säkerheten och produktivitet och minskade kostnader, driftstopp och repetitiva manuella uppgifter.

Företag eller organisationer kan kräva en krävande blandning av olika rad-för-företag (LOB)-tjänster och anställda som ofta har olika roller ni med olika projekt. En vDC kräver bra samarbete mellan olika team med vissa rolldefinitioner att få system körs med bra styrning. I matrisen med ansvar, åtkomst och rättigheter kan vara mycket komplex. Identity management i vDC implementeras via [ *Azure Active Directory* (AAD)] [ AAD] och rollbaserad åtkomstkontroll (RBAC).

En katalogtjänst är en delad informationsinfrastruktur för att hitta, hantera, administrera och ordna vanliga objekt och nätverksresurser. Dessa resurser kan innehålla volymer, mappar, filer, skrivare, användare, grupper, enheter och andra objekt. Varje resurs i nätverket är ett objekt som katalogservern. Information om en resurs lagras som en uppsättning attribut som är associerade med denna resurs eller objekt.

Alla Microsoft online företagstjänster förlitar sig på Azure Active Directory (AAD) för inloggning och andra identitet. Azure Active Directory är en omfattande molnlösning för identitets- och åtkomsthantering med hög tillgänglighet som kombinerar kärnkatalogstjänster, avancerad identitetsstyrning och programåtkomsthantering. AAD kan integreras med lokala Active Directory för att aktivera enkel inloggning för alla molnbaserad och värdbaserad lokalt (lokalt) program. Användarattribut för lokala Active Directory-kan synkroniseras automatiskt till AAD.

En enda global administratör krävs inte att tilldela alla behörigheter i en vDC. I stället ha varje viss avdelning (eller grupp av användare eller tjänster i katalogtjänsten) de behörigheter som krävs för att hantera sina egna resurser inom en vDC. Behörighet att strukturera kräver belastningsutjämning. För många behörigheter kan hindra prestanda effektivitet och för få eller Lös behörigheter kan öka säkerhetsrisker. Azure rollbaserad åtkomstkontroll (RBAC) hjälper dig för att lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för vDC-resurser.

##### <a name="security-infrastructure"></a>*Säkerhetsinfrastruktur*
Säkerhetsinfrastruktur i samband med en vDC beror huvudsakligen på avgränsning av trafiken i det vDC nätverkssegment för specifika virtuella och hur du styr ingående och utgående flödar i vDC. Azure är baserad på arkitektur med flera innehavare som förhindrar att obehöriga och oavsiktliga trafik mellan distributioner, med isolering av virtuella nätverk (VNet) åtkomstkontrollistor (ACL), belastningsutjämnare och IP-filter, tillsammans med principer för flödet av trafik. Network address translation (NAT) separerar interna nätverkstrafiken från externa trafiken.

Azure-strukturen allokerar infrastrukturresurser till klienternas arbetsbelastningar och hanterar kommunikation till och från virtuella datorer (VM). Hypervisor-programmet i Azure tvingar minne och processen åtskillnad mellan virtuella datorer och på ett säkert sätt skickar nätverkstrafik till innehavare för gäst-OS.

##### <a name="connectivity-to-the-cloud"></a>*Anslutningen till molnet*
VDC måste ansluta till externa nätverk för att erbjuda tjänster till kunder, partner och interna användare. Detta innebär vanligen anslutningen inte bara till Internet, utan också att lokala nätverk och datacenter.

Kan kunderna skapa sina säkerhetsprinciper för att styra vad och hur specifika vDC värdbaserade tjänster som är tillgänglig från Internet via nätverket virtuella installationer (med filtrering och trafik kontroll) och anpassade routning principer och nätverket filtrering (användaren definierat Routning och Nätverkssäkerhetsgrupper).

Företag behöver ofta ansluta VDC till lokala Datacenter eller andra resurser. Finns en anslutning mellan Azure och lokala nätverk är därför en viktig aspekt när du skapar en effektiv arkitektur. Företag har två olika sätt att skapa en sambandet mellan virtuella och lokala i Azure: överföring via Internet och/eller privata direkta anslutningar.

En [ **Azure plats-till-plats-VPN** ] [ VPN] är en samtrafik tjänst via Internet mellan lokala nätverk och vDC fastställas genom säkra krypterade anslutningar (IPsec/IKE-tunnlar). Azure plats-till-plats-anslutning är flexibel och snabbt att skapa, och kräver inte några ytterligare upphandling, eftersom alla anslutningar som ansluter via internet.

[**ExpressRoute** ] [ ExR] är en Azure anslutningen-tjänst som låter dig skapa privata anslutningar mellan virtuella och lokala nätverk. ExpressRoute-anslutningar inte gå via det offentliga Internet och ger högre säkerhet, tillförlitlighet och högre hastigheter (upp till 10 Gbit/s) tillsammans med konsekvent svarstid. ExpressRoute är mycket användbar för VDC, som ExpressRoute kunder kan få fördelarna med kompatibilitetsregler som är associerade med privata anslutningar.

Distribuera ExpressRoute anslutningar innebär att arbeta med en ExpressRoute-leverantör. För kunder som behöver komma igång snabbt, det är vanligt att använda ursprungligen plats-till-plats VPN för att upprätta en anslutning mellan vDC lokala resurser och migrera sedan till ExpressRoute-anslutning.

##### <a name="connectivity-within-the-cloud"></a>*Anslutning i molnet*
[Vnet] [ VNet] och [VNet-Peering] [ VNetPeering] är grundläggande anslutningsmöjligheter för nätverkstjänster inuti en vDC. Ett VNet garanterar en naturlig gräns för isolering för vDC-resurser och VNet-peering tillåter inbördes kommunikation mellan olika Vnet i samma Azure-region. Trafikkontroll i ett virtuellt nätverk och mellan virtuella nätverk måste matcha en uppsättning säkerhetsregler som angetts via åtkomstkontrollistor ([Nätverkssäkerhetsgruppen][NSG]), [virtuella nätverksinstallationer][NVA], och anpassade routningstabeller ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Översikt över virtuella Datacenter

### <a name="topology"></a>topologi
NAV och ekrar modellen utökade virtuella datacenter inom en enda Azure region

[![1]][1]

Navet är den centrala zon som styr och kontrollerar ingång och/eller utgående trafik mellan olika zoner: Internet, lokalt och i ekrar. Topologi för NAV och ekrar ger IT-avdelningen ett effektivt sätt att tillämpa säkerhetsprinciper på en central plats samtidigt minska risken för felkonfiguration och exponering.

Hubben innehåller vanliga tjänstkomponenter som används av ekrarna. Här följer några exempel på vanliga centrala tjänster:

-   Windows Active Directory-infrastruktur (med relaterade ADFS-tjänsten) krävs för autentisering av användare från tredje part använder från ej betrodda nätverk innan du hämtar åtkomst till arbetsbelastningarna i ekrar
-   En DNS-tjänsten för att matcha namn för arbetsbelastning i ekrar, till åtkomst till resurser lokalt och på Internet
-   En PKI-infrastruktur att implementera enkel inloggning för arbetsbelastningar
-   Flödeskontroll (TCP/UDP) mellan ekrar och Internet
-   Flödeskontroll mellan ekrar och lokalt
-   Om du vill flödeskontroll mellan en ekrar

VDC minskar övergripande kostnader med hjälp av det delade hubb mellan flera ekrar.

Rollen för varje ekrar kan vara till värden olika typer av arbetsbelastningar. Ekrarna kan också ge en modulär metod för repeterbara distributioner (till exempel utveckling och testning, användargodkännande, Förproduktion och produktion) för samma arbetsbelastningar. Ekrarna kan också användas för att särskilja och aktivera olika grupper i din organisation (till exempel DevOps grupper). Det är möjligt att distribuera en grundläggande arbetsbelastning eller komplexa arbetsbelastningar i flera nivåer med trafikkontroll mellan nivåer inuti en ekrar.

##### <a name="subscription-limits-and-multiple-hubs"></a>Prenumerationsbegränsningar och flera nav
I Azure distribueras varje komponent, oavsett vilken typ i en Azure-prenumeration. Isolering av Azure komponenter i olika Azure-prenumerationer kan uppfylla kraven för olika LOB, till exempel konfigurera olika nivåer av åtkomst och auktorisering.

En enda vDC kan skala upp till stort antal ekrar, även om som varje IT-system är plattformar gränser. Hubb-distributionen är bunden till en viss Azure-prenumeration som har begränsningar och gränser (till exempel en max antal VNet peerkopplingar - finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar] [ Limits] mer information). I fall där gränser kan vara ett problem arkitekturen kan skalas upp ytterligare genom att utöka modellen från en enda hubb ekrar till ett kluster med nav och ekrar. Flera nav i en eller flera Azure-regioner samman med hjälp av Express Route eller plats-till-plats-VPN.

[![2]][2]

För flera nav ökar prestanda för kostnad och hantering av systemet och kan endast motiveras av skalbarhet (exempel: system gränser eller redundans) och regionala replikering (exempel: återställning av slutanvändare prestanda eller katastrof). I scenarier som kräver flera nav, alla hubbar bör sträva efter att erbjuda samma uppsättning tjänster för operativa enkel.

##### <a name="interconnection-between-spokes"></a>Sambandet mellan ekrar
Det är möjligt att implementera komplexa arbetsbelastningar i flera nivåer inuti en enda ekrar. Flera nivåer konfigurationer kan implementeras med hjälp av undernät (en för varje nivå) i samma virtuella nätverk och filtrering flöden med hjälp av NSG: er.

Å andra sidan kanske en systemarkitekt vill distribuera en arbetsbelastning med flera nivåer i flera Vnet. Med VNet-peering kan ekrar ansluta till andra ekrar i samma hubb eller annan hubs. Ett typexempel på det här scenariot är fallet där programmet bearbetning servrar finns i en ekrar (VNet) när databasen är distribuerat i en annan ekrar (VNet). I det här fallet är det enkelt att kopplas samman ekrar med VNet-peering och därigenom undvika transporteras via hubben. En noggrann arkitektur och säkerhet granskning ska utföras för att säkerställa att kringgå navet inte kringgå viktiga säkerhets- eller granskning som bara kan finnas i hubben.

[![3]][3]

Ekrar kan också kopplas till en eker som fungerar som ett nav. Den här metoden skapar en hierarki med två nivåer: ekrar på högre nivå (nivå 0) blir hubb för lägre ekrar (nivå 1) i hierarkin. Ekrar för vDC behöver vidarebefordra trafiken till central knutpunkt nå ut antingen till lokalt nätverk eller internet. En arkitektur med två nivåer av NAV introducerar komplex routning som tar bort fördelarna med en enkel nav-eker-relation.

Även om Azure tillåter komplexa topologier, är en grundläggande principerna för vDC konceptet repeterbarhet och enkelhet. För att minimera hanteringsarbete, är den enkla nav-eker-designen Referensarkitektur rekommenderade vDC.

### <a name="components"></a>Komponenter
Ett virtuellt Datacenter består av fyra grundläggande komponent: **infrastruktur**, **perimeternätverk**, **arbetsbelastningar**, och **övervakning**.

Varje komponenttyp består av olika Azure-funktioner och resurser. Din vDC består av instanser av olika komponenter och flera varianter av samma komponenttyp. Du kan till exempel ha många olika, logiskt åtskilda arbetsbelastning instanser som representerar olika program. Du kan använda dessa komponenttyper av olika och instanser för att slutligen skapa vDC.

[![4]][4]

Föregående övergripande arkitekturen för en vDC visar olika komponent som används i olika områden av topologi för NAV-ekrar. Diagrammet visar infrastrukturkomponenter i olika delar av arkitekturen.

Som en bra idé (för en lokal DC eller vDC) åtkomst vara rättigheter och behörigheter gruppbaserade. Behandlar grupper kan i stället för enskilda användare upprätthålla åtkomstprinciper konsekvent mellan olika team och hjälpmedel för minimera konfigurationsfel. Tilldela och ta bort användare till och från relevanta grupper kan du uppdatera behörigheterna för en specifik användare kontinuerligt.

Varje grupp med rollen ska ha ett unikt prefix för deras namn som gör det enkelt att identifiera vilken grupp som är associerad med vilken arbetsbelastning. Exempelvis kan en arbetsbelastning som värd för en autentiseringstjänst kan ha grupper med namnet *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps och AuthServiceInfraOps.* På samma sätt för centraliserad roller eller roller som inte hör till en viss tjänst, kan inleds med ”Corp” *CorpNetOps* t.ex.

Många organisationer använder en variation av följande grupper för att tillhandahålla en större uppdelning av roller:

-   Den *central IT-grupp (Corp)* har äganderätt att styra infrastruktur (till exempel nätverk och säkerhet) och därför måste ha rollen deltagare på prenumerationen (och ha kontroll över hubben) och bidragsgivarbehörighet i ekrarna. Stora företag ofta dela dessa management ansvar mellan flera team som; en nätverksåtgärder (CorpNetOps)-gruppen (med exklusiv fokus på nätverk) och gruppen säkerhetsåtgärder (CorpSecOps) (ansvarig för brandväggen och säkerhet). I detta specifika fall behöver två olika grupper skapas för tilldelning av dessa anpassade roller.
-   Den *dev & testgrupp (AppDevOps)* har ansvar för att distribuera arbetsbelastningar (appar eller tjänster). Den här gruppen tar rollen för Virtual Machine-deltagare för IaaS-distributioner och/eller en eller flera PaaS-deltagare roller (se [inbyggda roller för rollbaserad åtkomstkontroll][Roles]). Utveckling och test-teamet kan eventuellt behöver ha synlighet för säkerhetsprinciper (NSG: er) och routningsprinciper (UDR) i hubben eller en specifik ekrar. Därför förutom rollerna deltagare för arbetsbelastningar för måste den här gruppen också rollen för Nätverksläsaren.
-   Den *drift och underhåll grupp (CorpInfraOps eller AppInfraOps)* ha ansvar för hantering av arbetsbelastningar i produktion. Den här gruppen måste vara en prenumeration deltagare för arbetsbelastningar i några prenumerationer för produktion. Vissa organisationer kan även utvärdera om de behöver en ytterligare eskalering team supportgrupp med rollen för prenumerationen deltagare i produktion och central knutpunkt-prenumeration för att åtgärda potentiella konfigurationsproblem i produktionsmiljön.

En vDC är strukturerad så att grupper som skapats för central IT-grupper hantera hubben har motsvarande grupper på arbetsbelastningsnivå. Utöver att hantera hubb resurser skulle endast central IT-grupper kunna styra extern åtkomst och översta behörigheter för prenumerationen. Dock skulle arbetsbelastningsgrupper som kunna styra resurser och behörigheter för sina VNet oberoende på Central IT.

VDC måste vara partitionerade som värd för flera projekt på ett säkert sätt mellan olika rad-för-företag (LOB). Alla projekt kräver olika isolerade miljöer (Dev, UAT, produktion). Separata Azure-prenumerationer för var och en av dessa miljöer isolera naturlig.

[![5]][5]

Föregående diagram visar relationen mellan en organisations projekt, användare, grupper och miljöer där Azure komponenter har distribuerats.

Vanligtvis i IT-miljö (eller nivån) är ett system där flera program har distribuerats och körs. Stora företag använder en utvecklingsmiljö (där ursprungligen gjorda ändringar och testas) och en produktionsmiljö (vad slutanvändare använda). Dessa miljöer är ofta åtskilda med flera mellanlagringsmiljöer mellan dem att tillåta stegvis distribution (distribution), testning och återställning vid problem. Distribution arkitekturer variera avsevärt, men vanligtvis den grundläggande processen för börjar vid utveckling (utveckling) och slutar vid produktion (PROD) fortfarande följs.

En gemensam arkitektur för dessa typer av miljöer med flera nivåer består av DevOps (utveckling och testning), UAT (mellanlagring) och produktionsmiljöer. Organisationer kan använda en eller flera Azure AD-klienter att definiera åtkomst och rättigheter till dessa miljöer. I föregående diagram visas ett fall där två olika används för Azure AD-klienter: en för DevOps och UAT och andra exklusivt för produktion.

Finns olika Azure AD-klienter tillämpar åtskillnad mellan miljöer. Samma grupp av användare (exempelvis Central IT) måste autentiseras med en annan URI för att få åtkomst till en annan AD-klient ändra roller eller behörigheter för antingen DevOps eller produktion miljöer för ett projekt. Förekomsten av autentisering av olika användare att komma åt olika miljöer minskar möjliga avbrott och andra problem som orsakas av mänskliga fel.

#### <a name="component-type-infrastructure"></a>Komponenttyp: infrastruktur
Den här komponenten är där infrastrukturen som stöder de flesta finns. Det är också där din centraliserad IT, säkerhet, och/eller efterlevnad teamen lägger största delen av tiden.

[![6]][6]

Infrastrukturkomponenter ger en sambandet mellan de olika komponenterna i en vDC och finns i både navet och ekrarna. Ansvaret för att hantera och underhålla infrastrukturkomponenter brukar tilldelas till centrala IT och/eller säkerhetsteam.

En av IT-infrastruktur-teamet primära uppgifter är att garantera konsekvens av IP-adress scheman över hela företaget. Den privata IP-adressutrymme tilldelats vDC måste vara konsekvent och inte överlappar privata IP-adresser tilldelas på ditt lokala nätverk.

När NAT på routrar i utkanten av lokalt eller i Azure-miljöer kan undvika IP-adresskonflikter, lägger till problem i din infrastrukturkomponenter. Enkel hantering är ett av huvudmålen med vDC, så använder NAT för att hantera IP-frågor inte är en rekommenderad lösning.

Infrastruktur innehåller följande funktioner:

-   [**Identitets- och directory services**][AAD]. Åtkomst till varje resurstypen i Azure styrs av en identitet som lagras i en katalogtjänst. Katalogtjänsten lagrar listan över användare utan även åtkomsträttigheter till resurser i en viss Azure-prenumeration. Dessa tjänster kan finnas endast molnbaserad eller de kan synkroniseras med lokala identitet som lagras i Active Directory.
-   [**Virtuellt nätverk**][VPN]. Virtuella nätverk är en av huvudkomponenterna i en vDC och gör att du kan skapa en gräns för isolering av trafik på Azure-plattformen. Ett virtuellt nätverk består av en eller flera virtuella nätverkssegment, var och en med ett specifikt IP-nätverksprefix (undernät). Det virtuella nätverket definierar ett internt perimeter område där IaaS virtuella datorer och PaaS-tjänster kan upprätta privata meddelanden. Virtuella datorer (och PaaS-tjänster) i ett virtuellt nätverk inte kan kommunicera direkt med virtuella datorer (och PaaS-tjänster) i ett annat virtuellt nätverk, även om båda virtuella nätverken skapas av samma kund under samma prenumeration. Isolering är en kritisk egenskap som garanterar kundens virtuella datorer och kommunikation förblir privat inom ett virtuellt nätverk.
-   [**UDR**][UDR]. Som standard utifrån system routningstabellen omdirigeras trafik i ett virtuellt nätverk. En användardefinierad väg är en anpassad routningstabell som nätverksadministratörer kan koppla till en eller flera undernät kan du skriva över beteendet i routningstabellen system och definiera en sökväg för kommunikation inom ett virtuellt nätverk. Förekomsten av udr: er garanterar att utgående trafik från eker-överföring via specifika egna virtuella datorer och/eller nätverket virtuella installationer och belastningsutjämnare finns i hubben och ekrarna.
-   [**NSG**][NSG]. En Nätverkssäkerhetsgrupp är en lista över säkerhetsregler som fungerar som trafik filtrering på IP-källor, IP-målet, protokoll, IP-källportar och IP-målet portar. NSG: N kan tillämpas på ett undernät, en virtuell NIC-kort som är associerade med en Azure VM, eller båda. De NSG: er är nödvändiga för att implementera rätt flödeskontroll i hubben och ekrarna. Säkerhetsnivån som tillhandahålls av NSG: N är en funktion av vilka portar som du öppnar och för vilket syfte. Kunder bör installera ytterligare per virtuell filter med värdbaserade brandväggar, till exempel IPtables eller Windows-brandväggen.
-   **DNS**. Namnmatchning för resurser i Vnet av en vDC tillhandahålls via DNS. Omfånget för namnmatchning av standard-DNS är begränsad till VNet. Vanligtvis en anpassad DNS-tjänsten måste distribueras i hubben som en del av gemensamma tjänster, men de huvudsakliga konsumenterna av DNS-tjänster finns i ekrar. Om det behövs kan kunderna skapa en hierarkisk struktur för DNS-med delegering av DNS-zoner till ekrarna.
-   [** Prenumeration] [ SubMgmt] och [resursgruppen Management][RGMgmt]**. En prenumeration definierar en naturlig gräns för att skapa flera grupper av resurser i Azure. Resurser i en prenumeration är sammanfogas i logiska behållare med namnet resursgrupper. Resursgruppen representerar en logisk grupp att ordna resurser av en vDC.
-   [**RBAC**][RBAC]. Via RBAC är det möjligt att mappa organisationsroller tillsammans med behörighet att komma åt specifika Azure-resurser, så att du kan begränsa användare till endast en viss delmängd av åtgärder. Med RBAC, kan du bevilja åtkomst genom att tilldela användare, grupper och program inom relevanta omfånget rätt roll. Omfånget för en rolltilldelning kan vara en Azure-prenumeration, resursgrupp eller en enskild resurs. RBAC kan arv av behörigheter. En roll som tilldelats en överordnad omfattning även ger åtkomst till underordnade som finns i den. Med RBAC kan du särskilja uppgifter och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete. Till exempel använda RBAC så att en medarbetare som hanterar virtuella datorer i en prenumeration medan en annan kan hantera SQL DBs inom samma prenumeration.
-   [**VNet-Peering**][VNetPeering]. Grundläggande funktion som används för att skapa en vDC-infrastruktur är VNet-Peering, en funktion som ansluter två virtuella nätverk (Vnet) i samma region via nätverket av Azure-datacentret.

#### <a name="component-type-perimeter-networks"></a>Komponenttyp: Perimeternätverk
[Perimeternätverk] [ DMZ] komponenter (även kallat DMZ nätverk) kan du ange nätverksanslutning med din lokala eller fysiska datacenternätverk, tillsammans med någon anslutning till och från Internet. Det är också där din nätverks- och team som är sannolikt tillbringar större delen av tiden.

Inkommande paket ska passera säkerhetsenheter i hubben, till exempel brandvägg, ID: N och IP-adresser, innan det nådde backend-servrarna i ekrarna. Internet-bunden paket från arbetsbelastningarna bör också flödar genom säkerhetsenheter i perimeternätverket för tvingande principer, kontroll och granskning, innan de lämnar nätverket.

Perimeternätverk nätverkskomponenter innehåller följande funktioner:

-   [Virtuella nätverk][VNet], [UDR][UDR], [NSG][NSG]
-   [Virtuell nätverksenhet][NVA]
-   [Belastningsutjämnare][ALB]
-   [Application Gateway][AppGW] / [WAF][WAF]
-   [Offentliga IP-adresser][PIP]

Vanligtvis central IT och säkerhet team har ansvar för Kravdefinition av och drift av perimeternätverk.

[![7]][7]

Föregående diagram visar verkställandet av två ytgränser med åtkomst till internet och ett lokalt nätverk, både i hubben. I ett enda NAV skala perimeternätverket till internet upp för att stödja många LOB, med hjälp av flera grupper av Web Application brandväggar (WAFs) och/eller brandväggar.

[**Virtuella nätverk** ] [ VNet] hubben bygger vanligtvis på ett VNet med flera undernät som värd för olika typer av tjänster filtrering och inspektera trafik till eller från internet via NVAs, WAFs och Azure Programgatewayer.

[**UDR** ] [ UDR] med UDR kunder kan distribuera brandväggar, ID: N/IP-adresser, och andra virtuella installationer och dirigera nätverkstrafik via hushållsapparater säkerhet för gräns tvingande säkerhetsprinciper, granskning och kontroll. Udr: er kan skapas i både NAV och ekrar att garantera att trafik eltransit specifika egna virtuella datorer, virtuella nätverksenheter och belastningsutjämnare som används av vDC. En UDR måste anges i eker-undernät genom att ange frontend IP-adressen för den interna belastningsutjämnaren som nästa hopp för att garantera att trafik som genereras från virtuella datorer i eker-överföring till rätt virtuella installationer. Den interna belastningsutjämnaren distribuerar interna trafiken till virtuella installationer (belastningen belastningsutjämnaren backend-pool).

[![8]][8]

[**Nätverks-virtuella installationer** ] [ NVA] i hubben, hanteras normalt perimeternätverket med åtkomst till internet via en servergrupp brandväggar och/eller Web Application brandväggar (WAFs).

Dessa program brukar drabbas av olika säkerhetsproblem och potentiella kryphål olika LOB använder ofta många webbprogram. Web Applications brandväggar är en särskild RAS av produkten som används för att identifiera attacker mot webbprogram (HTTP/HTTPS) i djupare än en allmän brandvägg. Jämfört med tradition brandväggsteknik har WAFs en uppsättning specifika funktioner för att skydda interna webbservrar mot hot.

Grupp med en brandvägg är grupp brandväggar som arbetar tillsammans under samma vanliga administration, med en uppsättning säkerhetsregler för att skydda arbetsbelastningar som finns i ekrar, och kontrollera åtkomsten till lokala nätverk. Grupp med en brandvägg har mindre specialiserat programvara jämfört med en Brandvägg, men har en bred application-scope för att filtrera och granska alla typer av trafik i ut- och ingångsanspråk. Brandväggen servergrupper implementeras normalt i Azure via nätverk virtuella installationer (NVAs) som är tillgängliga i Azure marketplace.

Det rekommenderas att använda en uppsättning NVAs för trafik på Internet och en annan för trafik som skickas lokalt. Med bara en uppsättning NVAs för både utgör en säkerhetsrisk eftersom det ger dig inga säkerhet perimeter mellan två mängder av nätverkstrafik. Med separata NVAs minskar den kontrollerar säkerhetsregler och det är tydligt vilka regler som motsvarar vilka inkommande nätverksbegäran.

De flesta stora företag hantera flera domäner. Azure DNS kan användas som värd för DNS-posterna för en viss domän. Som exempel kan virtuella IP-adress (VIP) för Azure extern belastningsutjämnare (eller WAFs) registreras i A-posten för en Azure DNS-post.

[**Azure belastningsutjämnare** ] [ ALB] Azure belastningsutjämnare erbjuder en lager 4 (TCP, UDP)-tjänsten, som kan distribuera inkommande trafik mellan tjänstinstanser som har definierats i en belastningsutjämnad uppsättning med hög tillgänglighet. Trafik som skickas till belastningsutjämnaren från frontend slutpunkter (offentliga IP-slutpunkter eller privata IP-slutpunkter) får du distribuera med eller utan nätverksadresser till en uppsättning backend-IP-adresspool (exempel som; Virtuella installationer i nätverket eller virtuella datorer).

Azure belastningsutjämnare kan avsökning hälsotillståndet för de olika serverinstanserna samt och när en avsökning inte svarar belastningsutjämnaren slutar att skicka trafik till feltillstånd-instansen. I en vDC har vi förekomsten av en extern belastningsutjämnare i hubben (till exempel balansera trafiken till NVAs) och ekrar (för att utföra åtgärder som belastningsutjämning trafik mellan olika virtuella datorer i en programarkitektur).

[**Programgateway** ] [ AppGW] Programgateway för Microsoft Azure är en dedikerad virtuell installation som ger programmet leverans domänkontrollant (ADC) som en tjänst med olika layer 7 belastningsutjämning funktioner för ditt program. På så sätt kan du optimera web servergruppen produktivitet genom att avlasta CPU beräkningsintensiva SSL-avslutning för programgatewayen. Här finns även andra layer 7-routningsfunktioner, till exempel resursallokeringsdistribution av inkommande trafik, cookiebaserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. En brandvägg för webbaserade program (WAF) ingår också som en del av programgatewayens WAF SKU. Den här SKU ger skydd till webbprogram från vanliga web säkerhetsrisker och trojaner. Application Gateway kan konfigureras som internetuppkopplad gateway, endast intern gateway eller en kombination av båda. 

[**Offentliga IP-adresser** ] [ PIP] vissa Azure-funktionerna kan du associera slutpunkter till en offentlig IP-adress som kan nås från internet till resursen. Den här slutpunkten använder NAT (Network Address Translation) för att dirigera trafik till den interna adressen och porten på virtuella Azure-nätverket. Den här sökvägen är det vanligaste sättet för externa trafik för att skicka till det virtuella nätverket. Offentliga IP-adresser kan konfigureras för att avgöra vilken trafik som skickas och hur och var den översätts till det virtuella nätverket.

#### <a name="component-type-monitoring"></a>Komponenttyp: övervakning
Övervakning komponenter ger synlighet och aviseringar från alla andra komponenter-typer. Alla team som ska ha åtkomst till övervakning för komponenter och tjänster som de har åtkomst till. Om du har en centraliserad help desk eller operations team, skulle de behöva har integrerat åtkomst till de data som tillhandahålls av dessa komponenter.

Azure erbjuder olika typer av loggning och övervakning av tjänster för att spåra beteendet för Azure värdbaserade resurser. Styrning och kontroll av arbetsbelastningar i Azure är baserat inte bara på att samla in loggdata, men också möjligheten att utlösaråtgärder baserat på specifika rapporterade händelser.

Det finns två typer av loggar i Azure:

-   [**Aktivitetsloggar** ] [ ActLog] (kallas även ”arbetsloggen”) ger kunskaper om de åtgärder som utfördes på resurser i Azure-prenumerationen. De här loggarna rapporterar kontroll-plan händelser för dina prenumerationer. Alla resurser i Azure ger granskningsloggar.

-   [**Azure diagnostikloggar** ] [ DiagLog] är loggar som genereras av en resurs som innehåller omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen.

[![9]][9]

Det är mycket viktigt att spåra NSG: er loggar, särskilt informationen i en vDC:

-   [**Händelseloggar**][NSGLog]: innehåller information om vilka NSG-regler tillämpas på virtuella datorer och instans roller baserat på MAC-adress.
-   [**Prestandaräknaren loggar**][NSGLog]: håller reda på hur många gånger varje NSG-regel har körts för att neka eller Tillåt trafiken.

Alla loggar kan lagras i Azure Storage-konton för granskning, statiska analys eller säkerhetskopiering. När loggarna lagras i ett Azure storage-konto, kan kunder använda olika typer av ramverk för att hämta, Förbered dig, analysera och visualisera informationen om du vill rapportera statusen och hälsan på molnresurser.

Stora företag bör redan har fått ett ramverk som standard för att övervaka lokalt system och kan utöka ramverket för att integrera loggar som genereras av distributioner. För organisationer som vill behålla all loggning i molnet, [logganalys] [ LogAnalytics] är ett bra alternativ. Eftersom logganalys implementeras som en molnbaserad tjänst kan du låta det igång snabbt med minimal investering i infrastrukturtjänster. Log Analytics kan också integreras med System Center-komponenter, till exempel System Center Operations Manager för att utöka din befintliga management investeringar i molnet.

Log Analytics är en tjänst i Azure som hjälper dig att samla in, korrelera, söka och fungerar på loggen och prestanda data som genereras av operativsystem, program och infrastrukturkomponenter för molnet. Den ger kunder realtid åtgärdsinformation som använder integrerad sökning och anpassade instrumentpaneler för att analysera alla poster över alla arbetsbelastningar i en vDC.

#### <a name="component-type-workloads"></a>Komponenttyp: arbetsbelastningar
Komponenter för arbetsbelastningen är där dina faktiska program och tjänster finns. Det är också där dina program utvecklingsgrupper tillbringar större delen av tiden.

Möjligheterna arbetsbelastningen är verkligen oändliga. Följande är några av typerna av möjliga arbetsbelastningar:

**Internt LOB-program**

Line-of-business-program är datorn kritiska till den pågående åtgärden på ett företag. LOB-program har vissa gemensamma egenskaper:

-   **Interaktiv**. LOB-program är interaktiva karaktär: data har angetts och returneras resultatet/rapporter.
-   **Datadrivna**. LOB-program är data beräkningsintensiva ofta åtkomst till databaserna eller annat lagringsutrymme.
-   **Integrerad**. LOB-program erbjudande integrering med andra system inom eller utanför organisationen.

**Kundinriktade webbplatser (Internet eller internt inriktad)** de flesta program som kommunicerar med Internet är webbplatser. Azure erbjuder möjligheten att köra en webbplats på en IaaS-VM eller från en [Azure Web Apps] [ WebApps] plats (PaaS). Azure Web Apps stöd för integrering med Vnet som gör att distributionen av Web Apps i ekrar av en vDC. Med VNET-integreringen behöver du inte visa en Internet-slutpunkt för dina program, men kan använda resurser privata icke-dirigerbara Internetadressen från ditt privata virtuella nätverk i stället.

**Stor dataanalys** när data behöver skala upp till en mycket stor volym databaser kan inte skalas upp korrekt. Hadoop-teknik ger ett system för att köra distribuerade frågor parallellt på många noder. Kunder har möjlighet att köra arbetsbelastningar för data i IaaS-VM eller PaaS ([HDInsight][HDI]). HDInsight stöder distribution i en plats-baserade virtuella nätverk, kan distribueras till ett kluster i en talade vDC.

**Händelser och meddelanden**
[Azure Event Hubs] [ EventHubs] är en tjänst för införandet av storskaliga telemetri som samlar in, omvandlar och lagrar miljontals händelser. En distribuerad strömmande plattform som erbjuder låg latens och konfigurerbara tid kvarhållning, vilket gör det lättare att mata in stora mängder telemetri i Azure och läsa data från flera program. Med Händelsehubbar, kan en enda ström stöder både i realtid och batch-baserade pipelines.

Ett mycket pålitlig moln meddelandetjänsten mellan program och tjänster, kan implementeras via [Azure Service Bus] [ ServiceBus] att erbjudanden asynkron asynkron meddelandetjänst mellan klienten och servern, tillsammans med strukturerade första in first out (FIFO) meddelanden och publicera/prenumerera funktioner.

[![10]][10]

### <a name="multiple-vdc"></a>Flera vDC
Hittills har den här artikeln fokuserar på en enda vDC som beskriver de grundläggande komponenter och arkitektur som bidrar till en flexibel vDC. Azure-funktioner, till exempel Azure belastningen belastningsutjämnare, NVAs, tillgänglighetsuppsättningar skalningsuppsättningar, tillsammans med andra mekanismer bidrar till ett system som gör det möjligt att skapa fasta SLA-nivåer i produktions-tjänster.

Men en enda vDC ligger på en enskild region och är sårbart för större avbrott som kan påverka hela regionen. Kunder som vill uppnå hög SLA behöver skydda tjänster via distributioner av samma projekt i två (eller fler) VDC placeras i olika regioner.

Förutom SLA-frågor finns några vanliga scenarier där distribuera flera VDC klokt att:

-   Nationella inställningar/Global närvaro
-   Haveriberedskap
-   Mekanism för att minska trafiken mellan DC

#### <a name="regionalglobal-presence"></a>Nationella inställningar/Global närvaro
Azure-datacenter finns i flera regioner över hela världen. När du väljer flera Azure-datacenter, behöver kunderna tänka två relaterade faktorer: geografiska avstånd och svarstid. Kunder måste utvärdera geografiska avståndet mellan VDC och avståndet mellan vDC och slutanvändare att erbjuda den bästa användarupplevelsen.

Azure-Region där VDC finns också måste överensstämma med regelkrav upprättas av alla juridiska behörighet som din organisation fungerar.

#### <a name="disaster-recovery"></a>Haveriberedskap
Implementeringen av en plan för katastrofåterställning är starkt relaterad till typ av arbetsbelastning berörda och möjlighet att synkronisera tillståndet arbetsbelastningen mellan olika VDC. Vi rekommenderar vill de flesta kunder synkronisera programdata mellan distributioner som körs i två olika VDC att implementera en mekanism för snabb failover. De flesta program är känslig för fördröjning och som kan orsaka potentiella timeout och fördröjning i datasynkronisering.

Synkronisering eller heartbeat-övervakning av program i olika VDC kräver kommunikation mellan dem. Två VDC i olika regioner kan vara ansluten via:

-   ExpressRoute privat peering när vDC hubbarna är anslutna till samma ExpressRoute-krets
-   flera ExpressRoute-kretsar kopplad via din företagets stamnät och vDC-nät för ExpressRoute-kretsar
-   Plats-till-plats VPN-anslutningar mellan vDC-hubbar i varje Azure-Region

ExpressRoute-anslutningen är vanligtvis den förvalda mekanismen på grund av högre bandbredd och konsekvent fördröjning när transporteras via Microsoft-stamnät.

Det finns inga magiskt recept att validera ett program som distribueras mellan två (eller fler) olika VDC i olika områden. Kunder bör köra kriteriet nätverkstester för att verifiera den latens och bandbredden för anslutningar och mål om synkron eller asynkron replikering är rätt och den optimala recovery tid mål för Återställningstid kan vara för din arbetsbelastning.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mekanism för att minska trafiken mellan DC
En effektiv metod att omdirigera trafik inkommande i en Domänkontrollant till en annan baseras på DNS. [Azure Traffic Manager] [ TM] använder Domain Name System (DNS)-mekanism för att dirigera om slutanvändaren trafik till den lämpligaste offentlig slutpunkten i en specifik vDC. Traffic Manager söker regelbundet tjänstens hälsa för offentliga slutpunkter i olika VDC via avsökningar, och vid avbrott för dessa slutpunkter dirigerar automatiskt till den sekundära vDC.

Traffic Manager fungerar på Azure offentliga slutpunkter och kan användas, till exempel att kontrollen/omdirigera trafik till virtuella Azure-datorer och Web Apps i lämplig vDC. Traffic Manager är flexibla även i händelse av en hel Azure-region misslyckas och kan styra distributionen av användartrafik för slutpunkter i olika VDC flera villkor (till exempel fel i en tjänst i en specifik vDC eller välja vDC med den lägsta Nätverksfördröjningen för klienten).

### <a name="conclusion"></a>Sammanfattning
Virtuella Datacenter är en metod för data center migrering till molnet som använder en kombination av funktioner och möjligheter för att skapa en skalbar arkitektur i Azure som maximerar molnet Resursanvändning, minskar kostnaderna och förenkla system styrning. VDC konceptet är baserad på en hub ekrar topologi tillhandahålla vanliga delade tjänster i hubben, så att specifika program/arbetsbelastningar i ekrarna. En vDC matchar strukturen för företagets roller, där olika avdelningar (Central IT, DevOps, drift och underhåll) arbetar tillsammans med en specifik lista över roller och ansvarsområden. En vDC uppfyller kraven för en ”lyfter och SKIFT”-migrering, men också ger många fördelar med att inbyggda distributioner.

## <a name="references"></a>Referenser
Följande funktioner beskrivs i det här dokumentet. Klicka på länkarna om du vill veta mer.

| | | |
|-|-|-|
|Nätverksfunktioner|Belastningsutjämning|Anslutning|
|[Virtuella Azure-nätverk][VNet]</br>[Nätverkssäkerhetsgrupper][NSG]</br>[NSG-loggar][NSGLog]</br>[Användardefinierade Routning][UDR]</br>[Virtuella nätverksenheter][NVA]</br>[Offentliga IP-adresser][PIP]|[Azure belastningsutjämnare (L3) ][ALB]</br>[Programgateway (L7) ][AppGW]</br>[Brandvägg för webbaserade program][WAF]</br>[Azure Traffic Manager][TM] |[VNet-Peering][VNetPeering]</br>[Virtuellt privat nätverk][VPN]</br>[ExpressRoute][ExR]
|Identitet</br>|Övervakning</br>|Metodtips</br>|
|[Azure Active Directory][AAD]</br>[Multifaktorautentisering][MFA]</br>[Rollen grundläggande åtkomstkontroller][RBAC]</br>[Standardroller i AAD][Roles] |[Aktivitetsloggar][ActLog]</br>[Diagnostikloggar][DiagLog]</br>[Log Analytics][LogAnalytics]</br> |[Metodtips för perimeter-nätverk][DMZ]</br>[Prenumerationshantering][SubMgmt]</br>[Hantering av resursgruppen.][RGMgmt]</br>[Azure-prenumerationsbegränsningar][Limits] |
|Andra Azure-tjänster|
|[Azure Web Apps][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Nästa steg
 - Utforska [VNet-Peering][VNetPeering], gäller teknik för vDC NAV och ekrar Designer
 - Implementera [AAD] [ AAD] att komma igång med [RBAC] [ RBAC] undersökning
 - Utveckla en prenumeration och resurs management-modell och RBAC modell för att uppfylla struktur, krav, och principerna för din organisation. Den viktigaste aktiviteten planering. Planera så mycket som praktiska, omorganisering, sammanslagningar, ny rad för produkten och så vidare.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "exempel på komponenten överlappar varandra" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "utförligt exempel på NAV och ekrar vDC"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "kluster med nav och ekrar"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "eker-eker"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "nivå blockdiagram där vDC"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "användare, grupper, prenumerationer och projekt"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "övergripande infrastrukturdiagram"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "övergripande infrastrukturdiagram"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "VNet-Peering och perimeter-nätverk"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "Översiktsdiagram för övervakning"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "Översiktsdiagram för arbetsbelastning"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg 
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is
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
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[LogAnalytics]: https://docs.microsoft.com/azure/log-analytics/log-analytics-overview
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
