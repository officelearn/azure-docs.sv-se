---
title: Azure-nätverk säkerhetsmetoder | Microsoft Docs
description: Lär dig några av de viktigaste funktionerna som är tillgängliga i Azure för att skapa säkra nätverksmiljöer
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: cf015f4857a22b755813d0be1af5a55a8b7b6535
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft cloud services och nätverkssäkerhet
Microsoft-molntjänster leverera storskaliga tjänster och infrastruktur, funktioner i företagsklass och många alternativ för hybridanslutning. Kunder kan välja att använda dessa tjänster via Internet eller med Azure ExpressRoute som tillhandahåller anslutningar privata nätverk. Microsoft Azure-plattformen gör att kunder vill sömlöst utöka sin infrastruktur till molnet och skapa flera nivåer arkitekturer. Tredje part kan dessutom aktivera förbättrade funktioner genom att erbjuda tjänster och virtuella installationer. Den här rapporten innehåller en översikt över arkitekturen problem som kunder bör tänka på när du använder Microsoft-molntjänster som nås via ExpressRoute och säkerhet. Den omfattar också skapa säkrare tjänster i virtuella Azure-nätverk.

## <a name="fast-start"></a>Snabb start
Följande logik diagram kan leda till ett exempel på de många säkerhetstekniker med Azure-plattformen. Snabbreferens hitta i exemplet som bäst passar ditt ärende. Fortsätt läsa igenom dokumentet expanderade förklaringar.
[![0]][0]

[Exempel 1: Skapa ett perimeternätverk (även kallat DMZ, demilitariserad zon och avskärmat undernät) för att skydda program med nätverkssäkerhetsgrupper (NSG: er).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exempel 2: Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG: er.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exempel 3: Skapa ett perimeternätverk för att skydda nätverk med en brandvägg, användardefinierad väg (UDR) och NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exempel 4: Lägga till en hybridanslutning med ett plats-till-plats, virtuella installation virtuellt privat nätverk (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exempel 5: Lägg till en hybridanslutning med en plats-till-plats, Azure VPN-gateway.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exempel 6: Lägg till en hybridanslutning med ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Exempel för att lägga till anslutningar mellan virtuella nätverk, hög tillgänglighet och tjänsten länkning läggs till det här dokumentet kommande månaderna.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft skydd för efterlevnad och infrastruktur
Microsoft erbjuder än 40 certifieringar och intyg som hjälper företag att uppfylla nationell, regional och branschspecifika krav för insamling och användning av data för enskilda användare har. Den mest omfattande uppsättningen eventuella molntjänstleverantören.

Mer information finns i kompatibilitetsinformationen på den [Microsoft Trust Center][TrustCenter].

Microsoft har en omfattande metod för att skydda moln-infrastruktur som behövs för att köra storskaliga globala tjänster. Microsoft-molninfrastruktur innehåller maskinvara, programvara, nätverk, och administrativa och driftspersonal, förutom de fysiska datacenter.

![2]

Den här metoden ger en säkrare grunden för kunder att distribuera sina tjänster i Microsoft-molnet. Nästa steg är för kunderna att utforma och skapa en säkerhetsarkitekturen för att skydda dessa tjänster.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Traditionell säkerhetsarkitekturer och perimeternätverk
Även om Microsoft investerar kraftigt för att skydda molninfrastrukturen, måste också kunder att skydda sina molntjänster och resursgrupper. En metod som har flera lager för säkerhet ger bäst skydd. En perimeter-nätverk säkerhetszon skyddar interna nätverksresurser från ett icke betrott nätverk. Ett perimeternätverk refererar till kanter eller delar av nätverket mellan Internet och skyddade företagets IT-infrastruktur.

I typiska företagsnätverk spikas kraftigt grundläggande infrastruktur på göras med flera lager av säkerhetsenheter. Gränsen för varje lager består av enheter och principen tvingande punkter. Varje lager kan innehålla en kombination av följande inställningar för säkerhet nätverksenheter: brandväggar, Denial of Service (DoS) förebyggande, intrångsidentifiering eller skyddssystem (ID: N/IP-adresser) och VPN-enheter. Tvingande principer kan utgöras av Brandväggsprinciper åtkomstkontrollistor (ACL) eller specifika routning. Den första försvarslinjen i nätverket, direkt acceptera inkommande trafik från Internet, är en kombination av dessa mekanismer för att blockera attacker och skadlig trafik samtidigt ytterligare legitima begäranden till nätverket. Den här trafiken dirigerar direkt till resurser i perimeternätverket. Den här resursen kan sedan ”prata” med resurser som är djupare i nätverket, transit nästa gränsen för verifiering första. Yttersta lagret kallas perimeternätverket eftersom den här delen av nätverket utsätts för Internet, normalt med någon form av skydd på båda sidor. Följande bild visar ett exempel på ett enda undernät perimeternätverk i ett företagsnätverk, med två säkerhetsgränser.

![3]

Det finns många arkitekturer som används för att implementera ett perimeternätverk. Dessa arkitekturer kan röra sig om en enkel belastningsutjämnare till ett perimeternätverk i flera undernät med olika metoder på varje gräns för att blockera trafik och skydda djupare lager i företagsnätverket. Hur perimeternätverket byggs beror på de specifika behoven i organisationen och den övergripande risktolerans.

Som kunder flytta arbetsbelastningen till offentliga moln, är det viktigt att stödja liknande funktioner för perimeter nätverksarkitekturen i Azure för att uppfylla efterlevnads- och säkerhetskrav. Det här dokumentet innehåller riktlinjer om hur kunder kan skapa en säker nätverksmiljö i Azure. Den fokuserar på perimeternätverket, men innehåller också en fullständig beskrivning av flera olika aspekter av nätverkssäkerhet. Följande frågor informerar den här diskussionen:

* Hur byggas ett perimeternätverk i Azure?
* Vilka är några av de Azure funktionerna som är tillgängliga för att skapa perimeternätverket?
* Hur kan backend-arbetsbelastningar skyddade?
* Hur styrs Internet-kommunikation till arbetsbelastningar i Azure?
* Hur kan de lokala nätverk skyddas från distributioner i Azure?
* När ska interna Azure säkerhetsfunktioner användas jämfört med tredjeparts-utrustning eller tjänster?

Följande diagram visar olika säkerhetsnivåer som Azure tillhandahåller till kunder. Dessa lager är både intern i själva Azure-plattformen och kunddefinierade funktioner:

![4]

Inkommande från Internet, Azure DDoS hjälper dig att skydda mot storskaliga attacker mot Azure. Nästa säkerhetslager är kunddefinierade offentliga IP-adresser (slutpunkter) som används för att avgöra vilken trafik kan passera Molntjänsten till det virtuella nätverket. Interna Azure virtuell nätverksisolering garanterar fullständig isolering från andra nätverk och att trafiken endast flödar via konfigurerade användaren sökvägar och metoder. Dessa sökvägar och metoder är nästa säkerhetslager där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa säkerhetsgränser för att skydda application-distributioner i det skyddade nätverket.

Nästa avsnitt innehåller en översikt över virtuella Azure-nätverk. Dessa virtuella nätverk skapas av kunder och är de distribuerade arbetsbelastningarna är anslutna till. Virtuella nätverk utgör grunden för alla de funktioner för nätverkssäkerhet krävs för att upprätta ett perimeternätverk för att skydda kundens distributioner i Azure.

## <a name="overview-of-azure-virtual-networks"></a>Översikt över virtuella Azure-nätverk
Innan Internettrafik får åtkomst till de virtuella Azure-nätverk, finns två säkerhetsnivåer ingår i Azure-plattformen:

1.    **DDoS-skydd**: DDoS-skydd är ett lager för det Azure fysiska nätverk som skyddar Azure-plattformen sig själv från storskaliga Internet-baserade attacker. Angreppen använder flera ”bot” noder i ett försök för att överbelasta en Internet-tjänst. Azure har en robust DDoS-skydd nät alla inkommande, utgående och över flera Azure-region-anslutning. Det här lagret för DDoS-skydd har inga konfigurerbara användarattribut och är inte tillgänglig för kunden. DDoS-skyddslager skyddar Azure som en plattform från storskaliga attacker, övervakar även ut bunden trafik och trafik över flera Azure-region. Med virtuella nätverksenheter på VNet, kan ytterligare lager av återhämtning konfigureras av kunden mot en mindre skala angrepp som inte utlösas skyddet på nätverksnivå plattform. Ett exempel på DDoS i åtgärden. Om en IP-adress för internetuppkopplad angripna av en storskalig DDoS-attack skulle Azure identifiera datakällor för attacker och Skrubba felaktiga trafiken innan den har nått sin destination. I nästan alla fall påverkas inte angripna slutpunkten av angrepp. I sällsynta fall att en slutpunkt påverkas påverkas ingen trafik till andra slutpunkter angripna slutpunkten. Därmed visas andra kunder och tjänster utan att påverka från den attacker. Det är viktigt att Observera att Azure DDoS endast söker efter storskaliga attacker. Det är möjligt att en specifik tjänst kan vara för många innan plattform skydd på nätverksnivå tröskelvärden överskrids. Till exempel kan en webbplats på en enskild A0 IIS-server vara offline av en DDoS-attack innan Azure-plattformsnivå DDoS-skydd registrerat ett hot.

2.  **Offentliga IP-adresser**: offentliga IP-adresser (aktiverat via slutpunkter för offentliga IP-adresser, Programgateway och andra Azure-funktioner som presenterar en offentlig IP-adress till internet vidarebefordras till resursen) låter molntjänster eller resurs grupper om du vill ha offentliga Internet IP-adresser och portar som visas. Slutpunkten använder NAT (Network Address Translation) för att dirigera trafik till den interna adressen och porten på virtuella Azure-nätverket. Den här sökvägen är det vanligaste sättet för externa trafik för att skicka till det virtuella nätverket. Offentliga IP-adresser kan konfigureras för att avgöra vilken trafik som skickas och hur och var den översätts till det virtuella nätverket.

När trafiken når det virtuella nätverket, finns det många funktioner som kommer till användning. Virtuella Azure-nätverk är grunden för kunder att ansluta sina arbetsbelastningar och där grundläggande behörighet gäller. Det är ett privat nätverk (ett virtuellt nätverk överlägg) i Azure för kunder med följande funktioner och egenskaper:

* **Trafik isolering**: ett virtuellt nätverk är isoleringsgräns trafik på Azure-plattformen. Virtuella datorer (VM) i ett virtuellt nätverk kan inte kommunicera direkt med virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverken skapas av samma kund. Isolering är en kritisk egenskap som garanterar kundens virtuella datorer och kommunikation förblir privat inom ett virtuellt nätverk.

>[!NOTE]
>Trafikisolering avser endast trafik *inkommande* till det virtuella nätverket. Som standard utgående trafik från VNet till internet tillåts, men kan förhindras om du vill av NSG: er.
>
>

* **Topologi i flera skikt**: virtuella nätverk att kunder kan definiera topologi i flera skikt genom att allokera undernät och bestämma separat adressutrymmen för olika element eller ”nivåerna” för deras arbetsbelastning. Dessa logiska grupperingar topologier möjligt för kunder att definiera olika åtkomstprincip baserat på typerna av arbetsbelastning och också kontrollera trafikflödet mellan nivåerna.
* **Korsanslutningar**: kunder kan upprätta en anslutning mellan ett virtuellt nätverk och flera lokala platser eller andra virtuella nätverk i Azure. Kunder kan använda VNet-Peering Azure VPN-gatewayer, från tredje part virtuella installationer eller ExpressRoute för att skapa en anslutning. Azure har stöd för plats-till-plats (S2S) VPN-anslutningar med IPsec/IKE standardprotokoll och privata ExpressRoute-anslutning.
* **NSG** kan kunderna skapa regler (ACL) granularitet önskad nivå: gränssnitt, enskilda virtuella datorer eller virtuella undernät. Kunder kan styra åtkomsten genom att tillåta eller neka kommunikation mellan arbetsbelastningar inom ett virtuellt nätverk från system i kundens nätverk via anslutning, eller dirigera Internet-kommunikation.
* **UDR** och **IP-vidarebefordran** att kunder kan definiera Kommunikationssökvägar för mellan olika nivåer inom ett virtuellt nätverk. Kunder kan distribuera en brandvägg, ID: N/IP-adresser och andra virtuella installationer och dirigera nätverkstrafik via hushållsapparater säkerhet för tvingande säkerhetsprinciper gräns, granskning och kontroll.
* **Nätverks-virtuella installationer** i Azure Marketplace: säkerhetsenheter som brandväggar, belastningsutjämnare och ID: N/IP-adresser som är tillgängliga i Azure Marketplace och galleriet VM-avbildning. Kunderna kan distribuera dessa enheter till sina virtuella nätverk, och särskilt på deras säkerhetsgränser (inklusive perimeter undernät) för att slutföra en flera nivåer säker nätverksmiljö.

Med dessa funktioner och möjligheter är ett exempel på hur en perimeter nätverksarkitektur kan konstrueras i Azure i följande diagram:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Egenskaper för perimeter-nätverk och krav
Perimeternätverket är klientdelen för nätverket, samverkar direkt kommunikation från Internet. Inkommande paket ska passera säkerhetsenheter som brandväggen, ID: N och IP-adresser, innan det nådde backend-servrar. Internet-bunden paket från arbetsbelastningarna kan också gå genom säkerhetsenheter i perimeternätverket för tvingande principer, kontroll och granskning, innan de lämnar nätverket. Perimeternätverket kan dessutom vara värd för anslutningar mellan lokala VPN-gatewayer mellan kundens virtuella nätverk och lokala nätverk.

### <a name="perimeter-network-characteristics"></a>Egenskaper för perimeter-nätverk
Refererar till den föregående bilden är vissa av egenskaperna hos ett bra perimeternätverk följande:

* Mot Internet:
  * Perimeter-undernät själva är Internet-riktade direkt kommunicerar med Internet.
  * Offentliga IP-adresser, virtuella IP-adresser och/eller Tjänsteslutpunkter skickar Internet-trafik till klientnätverk och enheter.
  * Inkommande trafik från Internet passerar säkerhetsenheter innan andra resurser på klientnätverk.
  * Om utgående Säkerhetsaktiverade passerar trafik säkerhetsenheter som det sista steget innan till Internet.
* Skyddat nätverk:
  * Det finns ingen direkt väg från Internet till grundläggande infrastruktur.
  * Kanaler till grundläggande infrastruktur måste passera via säkerhetsenheter som NSG: er, brandväggar eller VPN-enheter.
  * Andra enheter måste inte överbrygga Internet och grundläggande infrastruktur.
  * Säkerhetsenheter både Internet-riktade och skyddat nätverk mot gränserna i perimeternätverket (till exempel två brandväggen ikonerna visas i bilden ovan) kanske faktiskt en enda virtuell installation med differentierade regler eller gränssnitt för varje gräns. Till exempel en fysisk enhet, logiskt avgränsade hantera belastningen för båda gränserna i perimeternätverket.
* Andra vanliga metoder och begränsningar:
  * Arbetsbelastningar måste inte lagra viktig affärsinformation.
  * Åtkomst och uppdateringar av perimeternätverk nätverkskonfigurationer och distributioner begränsas till enbart auktoriserade administratörer.

### <a name="perimeter-network-requirements"></a>Perimeternätverk nätverkskrav
Följ dessa riktlinjer på virtuella nätverkskraven för att implementera en lyckad perimeternätverk om du vill aktivera följande egenskaper:

* **Arkitektur för undernätet:** ange det virtuella nätverket så att en hela undernätet dedicerade som perimeternätverket, avskild från andra undernät i samma virtuella nätverk. Den här separationen innebär trafiken mellan perimeternätverket och andra interna eller privata undernät nivåer flöden via en brandvägg eller en virtuell installation av ID/IP-adresser.  Användardefinierade vägar på gräns undernät krävs för att vidarebefordra trafiken till virtuell enhet.
* **NSG:** perimeter-undernät själva bör vara öppna för att tillåta kommunikation med Internet, men som innebär inte kunder ska kringgås NSG: er. Följ vanliga säkerhetsåtgärder för att minimera de nätverk hämtar exponerad mot Internet. Lås remote adressintervallen tillgång distributioner eller specifika programprotokoll och portar som är öppna. Det kan finnas omständigheter, men som en fullständig låsning inte är möjligt. Till exempel om kunder har en extern webbplats i Azure, perimeternätverket ska tillåta inkommande webbegäranden från offentliga IP-adresser, men bara öppna web application portar: TCP på port 80 och/eller TCP på port 443.
* **Routningstabellen:** perimeter-undernät själva ska kunna kommunicera direkt till Internet, men ska inte direkt kommunikation till och från de bakre slutet eller lokalt nätverk utan att gå via en brandvägg eller installation.
* **Installation av säkerhetskonfiguration:** att vidarebefordra och inspektera paket mellan perimeternätverket och resten av det skyddade nätverk, säkerhetsenheter som brandvägg, ID: N och IP-adresser enheter kanske multihomed. De kan ha separata nätverkskort för perimeternätverket och backend-undernät. Nätverkskort i perimeternätverket kommunicera direkt till och från Internet med motsvarande NSG: er och routningstabellen för perimeter-nätverk. Nätverkskort som ansluter till backend-undernät har mer begränsade NSG: er och routningstabeller motsvarande backend-undernät.
* **Säkerhet enhetens funktioner:** säkerhetsenheter som distribueras i perimeternätverket vanligtvis utför följande funktioner:
  * Brandvägg: Framtvinga brandväggsregler eller principer för åtkomstkontroll för inkommande begäranden.
  * Hot upptäcka och förhindra: identifiera och minska skadliga attacker från Internet.
  * Granskning och loggning: underhålla detaljerade loggar för granskning och analys.
  * Omvänd proxy: omdirigering av inkommande begäranden till motsvarande backend-servrar. Omdirigeringen innebär mappning och översätta måladresser på frontend-enheterna vanligtvis brandväggar till backend-server-adresser.
  * Vidarebefordra proxy: tillhandahåller NAT och utföra granskning för kommunikation som startas från det virtuella nätverket till Internet.
  * Router: Vidarebefordrar inkommande och mellan undernät trafik i det virtuella nätverket.
  * VPN-enhet: fungerar som mellan lokala VPN-gateways för VPN-anslutning mellan kundens lokala nätverk och virtuella Azure-nätverk.
  * VPN-servern: acceptera VPN-klienter som ansluter till virtuella Azure-nätverk.

> [!TIP]
> Behåll följande två grupper separat: till personer som har behörighet att komma åt perimeter network security växel och till personer som godkänts som administratörer för utveckling, distribution eller drift. Avgränsa dessa grupper gör det möjligt för en ansvarsfördelning och förhindrar att en enskild person kringgå både program och säkerhetskontroller i nätverket.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Frågor för att svara när du skapar nätverkets gränser
Om inte uttryckligen nämns i det här avsnittet refererar termen ”nätverk” till privata virtuella Azure-nätverk skapas av en administratör för prenumerationen. Termen refererar inte till de underliggande fysiska nätverk i Azure.

Virtuella Azure-nätverk används också ofta att utöka traditionella lokala nätverk. Det är möjligt att få plats-till-plats eller ExpressRoute hybrid nätverkslösningar med nätverksarkitekturer i perimeternätverket. Den här länken hybrid är viktigt i att skapa nätverk säkerhetsgränser.

Följande tre frågor är kritiska för svaret när du utvecklar ett nätverk med ett perimeternätverk och flera säkerhetsgränser.

#### <a name="1-how-many-boundaries-are-needed"></a>1) hur många gränser behövs?
Den första punkten beslut är att bestämma hur många säkerhetsgränser behövs i ett visst scenario:

* En enda gräns: en på frontend perimeternätverket, mellan det virtuella nätverket och Internet.
* Två gränser: en på Internet-sida om perimeternätverket och en annan mellan undernät perimeternätverket och backend-undernät i de virtuella Azure-nätverk.
* Tre gränser: en på Internet-sida i perimeternätverket, mellan perimeternätverket och backend-undernät och ett mellan backend-undernät och det lokala nätverket.
* N gränser: en variabel nummer. Beroende på säkerhetskrav finns det ingen gräns för antalet säkerhetsgränser som kan användas i ett givet nätverk.

Antalet och typen av gränser som behövs varierar baserat på företagets risktolerans och det specifika scenario implementeras. Det här beslutet görs ofta tillsammans med flera grupper inom en organisation, inklusive ofta ett team för risk och efterlevnad, ett nätverk och plattform team och en programutvecklingsteamet. Personer med kunskap om säkerhet, data ingår och tekniker som används måste ha en Säg beslutet att säkerställa rätt säkerhetsbehörigheter behöver för varje implementering.

> [!TIP]
> Använd det lägsta antalet gränser som uppfyller säkerhetskraven för en viss situation. Med flera gränser, åtgärder och felsökning kan vara svårare, samt hanteringskostnader som ingår i hantering av flera principer för gräns över tid. Otillräcklig gränserna ökar dock risken. Det är viktigt att hitta balansen.
>
>

![6]

I föregående bild visas en övergripande bild av ett tre säkerhet gräns nätverk. Gränser är mellan perimeternätverket och Internet, Azure frontend och backend-privata undernäten, och Azure backend-undernät och lokala företagets nätverk.

#### <a name="2-where-are-the-boundaries-located"></a>2) där finns gränserna?
När antalet gränser är valt är var implementeras nästa Beslutspunkt. Det finns vanligtvis tre alternativ:

* Med hjälp av en Internet-baserade mellanhandstjänster (till exempel en molnbaserad Brandvägg för webbaserade program, som inte beskrivs i det här dokumentet)
* Med inbyggda funktioner och/eller nätverket virtuella installationer i Azure
* Med hjälp av fysiska enheter i det lokala nätverket

Alternativen är intern Azure-funktioner (till exempel Azure belastningsutjämnare) eller virtuella nätverksinstallationer från omfattande partner-ekosystemet i Azure (till exempel brandväggar för Check Point) i rent Azure-nätverk.

Om en gräns krävs mellan Azure och ett lokalt nätverk kan på säkerhetsenheter finnas på endera sidan av anslutningen (eller båda sidorna). Därför måste ett beslut fattas på platsen för att placera säkerhet växeln.

I föregående bild Internet till perimeternätverk framför-till-backend-gränserna som ingår helt i Azure och måste vara intern Azure-funktioner eller nätverket virtuella installationer. Säkerhetsenheter på kantlinjen mellan Azure (backend-undernät) och företagets nätverk kan vara antingen på Azure-sidan eller den lokala sidan eller till och med en kombination av enheter på båda sidor. Det kan finnas betydande fördelar och nackdelar med att båda alternativen måste betraktas som allvarligt.

Till exempel har med befintlig fysisk säkerhet växel på den lokala nätverkssidan fördelen att det krävs inga nya växeln. Den måste bara omkonfiguration. Nackdelen, är dock att all trafik måste komma tillbaka från Azure till lokala nätverket för att ses av den säkerhet växeln. Azure-Azure-trafik kan därför innebära betydande fördröjning och påverkar programmets prestanda och användaren får, om det tvingades tillbaka till det lokala nätverket för tvingande säkerhetsprinciper.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) hur implementeras gränserna?
Varje säkerhetsgräns troligen har olika krav (t.ex, ID: N och brandväggsregler på Internet-sida i perimeternätverket, men endast ACL: er mellan perimeternätverket och backend-undernät). Bestämmer som enheten (eller hur många enheter) för att använda beroende på kraven för scenariot och säkerhet. I följande avsnitt beskrivs vissa alternativ som kan användas i exempel 1, 2 och 3. Granska funktionerna för Azure interna nätverk och enheterna i Azure från partner-ekosystemet innehåller många olika alternativ som är tillgängliga för att lösa valfri scenario.

En annan är nyckel implementering beslut hur du ansluter lokalt nätverk med Azure. Bör du använda Azure virtuella gateway eller en virtuell nätverksenhet? Dessa alternativ beskrivs mer detaljerat i följande avsnitt (exempel 4, 5 och 6).

Dessutom kan kan trafik mellan virtuella nätverk i Azure behövas. Dessa scenarier läggs i framtiden.

När du väl vet svaren på föregående frågorna den [snabb Start](#fast-start) avsnitt kan hjälpa dig att identifiera vilka exempel är mest lämpliga för ett visst scenario.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exempel: Skapa säkerhetsgränser med virtuella Azure-nätverk
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exempel 1 skapa ett perimeternätverk för att skydda program med NSG: er
[Tillbaka till snabb start](#fast-start) | [detaljerad skapa instruktioner för det här exemplet][Example1]

[![7]][7]

#### <a name="environment-description"></a>Beskrivning av miljö
I det här exemplet finns det en prenumeration som innehåller följande resurser:

- En enskild resursgrupp
- Ett virtuellt nätverk med två undernät: ”FrontEnd” och ”BackEnd”
- En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
- En Windows-server som representerar en program-webbserver (”IIS01”)
- Två Windows-servrar som representerar backend-programservrar (”AppVM01”, ”AppVM02”)
- En Windows-server som representerar en DNS-server (”DNS01”)
- En offentlig IP-adress som är kopplade till webbservern program

Skript och en Azure Resource Manager-mall finns i [detaljerade instruktioner build][Example1].

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan in med sex regler.

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först, följt av de mer allmänna ”Deny” reglerna. Den angivna prioriteten bestämmer vilka regler som utvärderas först. När du har hittat trafik ska gälla för en specifik regel utvärderas inga ytterligare regler. NSG-regler kan tillämpas i antingen inkommande eller utgående riktning (ur undernätet).
>
>

Deklarativt, byggs följande regler för inkommande trafik:

1. Intern DNS-trafik (port 53) tillåts.
2. RDP-trafik (port 3389) från Internet till en virtuell dator är tillåtet.
3. HTTP-trafik (port 80) från Internet till webbservern (IIS01) tillåts.
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts.
5. All trafik (alla portar) från Internet till hela virtuella nätverket (båda undernäten) nekas.
6. All trafik (alla portar) från undernätet frontend till backend-undernät nekas.

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande trafik från Internet till webbservern, både regler 3 (Tillåt) och 5 (neka) skulle tillämpas. Men eftersom regel 3 har högre prioritet kan bara den skulle tillämpas och regel 5 inte skulle komma till användning. HTTP-begäran skulle därför tillåtas till webbservern. Om samma trafiken försökte nå servern DNS01 regel 5 (neka) skulle vara först att tillämpa och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar frontend undernätet från kommunicerar med backend-undernät (förutom tillåten trafik i regler 1 och 4). Den här regeluppsättningen skyddar backend-nätverket om en angripare komprometterar webbprogram på klientdelen. Angriparen skulle ha begränsad åtkomst till backend-”skyddad” nätverk (endast för resurser som visas på AppVM01 servern).

Det finns en utgående Standardregeln som tillåter trafik ut till Internet. I det här exemplet vi att tillåta utgående trafik och inte ändra de utgående reglerna. Om du vill låsa i båda riktningarna användardefinierade routning krävs (se exempel 3).

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och enkelt sätt att isolera backend-undernät från inkommande trafik. Mer information finns i [detaljerade instruktioner build][Example1]. De här anvisningarna inkluderar:

* Hur du skapar den här perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar den här perimeternätverk med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje NSG-kommando.
* Detaljerad trafik flödet scenarier visar hur trafik tillåts eller nekas i varje lager.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exempel 2 Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG: er
[Tillbaka till snabb start](#fast-start) | [detaljerad skapa instruktioner för det här exemplet][Example2]

[![8]][8]

#### <a name="environment-description"></a>Beskrivning av miljö
I det här exemplet finns det en prenumeration som innehåller följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med två undernät: ”FrontEnd” och ”BackEnd”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En virtuell nätverksenhet, i det här fallet en brandvägg, som är anslutna till undernätet frontend
* En Windows-server som representerar en program-webbserver (”IIS01”)
* Två Windows-servrar som representerar backend-programservrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Skript och en Azure Resource Manager-mall finns i [detaljerade instruktioner build][Example2].

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan in med sex regler.

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först, följt av de mer allmänna ”Deny” reglerna. Den angivna prioriteten bestämmer vilka regler som utvärderas först. När du har hittat trafik ska gälla för en specifik regel utvärderas inga ytterligare regler. NSG-regler kan tillämpas i antingen inkommande eller utgående riktning (ur undernätet).
>
>

Deklarativt, byggs följande regler för inkommande trafik:

1. Intern DNS-trafik (port 53) tillåts.
2. RDP-trafik (port 3389) från Internet till en virtuell dator är tillåtet.
3. All Internettrafik (alla portar) till virtuell nätverksenhet (brandvägg) tillåts.
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts.
5. All trafik (alla portar) från Internet till hela virtuella nätverket (båda undernäten) nekas.
6. All trafik (alla portar) från undernätet frontend till backend-undernät nekas.

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande trafik från Internet till brandväggen, både regler 3 (Tillåt) och 5 (neka) skulle tillämpas. Men eftersom regel 3 har högre prioritet kan bara den skulle tillämpas och regel 5 inte skulle komma till användning. HTTP-begäran skulle därmed kunna brandväggen. Om samma trafiken försökte nå servern IIS01 trots att det är i undernätet frontend, regel 5 (neka) är skulle ha använts, och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar frontend undernätet från kommunicerar med backend-undernät (förutom tillåten trafik i regler 1 och 4). Den här regeluppsättningen skyddar backend-nätverket om en angripare komprometterar webbprogram på klientdelen. Angriparen skulle ha begränsad åtkomst till backend-”skyddad” nätverk (endast för resurser som visas på AppVM01 servern).

Det finns en utgående Standardregeln som tillåter trafik ut till Internet. I det här exemplet vi att tillåta utgående trafik och inte ändra de utgående reglerna. Om du vill låsa i båda riktningarna användardefinierade routning krävs (se exempel 3).

#### <a name="firewall-rule-description"></a>Beskrivning av regel för brandvägg
I brandväggen ska vidarebefordran av regler skapas. Eftersom det här exemplet endast dirigerar trafik för Internet-bunden till brandväggen och sedan på webbservern endast en vidarebefordran network address translation (NAT) behövs regeln.

Vidarebefordringsregel för godkänner alla inkommande källadress som träffar i brandväggen som försöker komma åt HTTP (port 80 eller 443 för HTTPS). Det har skickas utanför i brandväggen lokala gränssnittet och omdirigeras till webbservern med IP-adressen för 10.0.1.5.

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkelt sätt att skydda ditt program med en brandvägg och isolera backend-undernät från inkommande trafik. Mer information finns i [detaljerade instruktioner build][Example2]. De här anvisningarna inkluderar:

* Hur du skapar den här perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje NSG kommandot och brandväggen regel.
* Detaljerad trafik flödet scenarier visar hur trafik tillåts eller nekas i varje lager.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exempel 3 skapa ett perimeternätverk för att skydda nätverk med en brandvägg och UDR och NSG
[Tillbaka till snabb start](#fast-start) | [detaljerad skapa instruktioner för det här exemplet][Example3]

[![9]][9]

#### <a name="environment-description"></a>Beskrivning av miljö
I det här exemplet finns det en prenumeration som innehåller följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med tre undernät: ”SecNet”, ”FrontEnd” och ”BackEnd”
* En virtuell nätverksenhet, i det här fallet en brandvägg, som är anslutet till undernätet för SecNet
* En Windows-server som representerar en program-webbserver (”IIS01”)
* Två Windows-servrar som representerar backend-programservrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Skript och en Azure Resource Manager-mall finns i [detaljerade instruktioner build][Example3].

#### <a name="udr-description"></a>UDR beskrivning
Som standard definieras följande systemvägar som:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal är alltid en eller flera definierade adressprefix som utgör det virtuella nätverket för det specifika nätverket (det vill säga ändras från virtuellt nätverk till virtuellt nätverk, beroende på hur varje specifik virtuellt nätverk har definierats). De återstående systemvägarna är statiska och standard som anges i tabellen.

I det här exemplet skapas två routningstabeller en vardera för frontend och backend-undernät. Varje tabell har lästs in med statiska vägar som är lämpliga för det angivna undernätet. I det här exemplet har varje tabell tre vägar som dirigera om all trafik (0.0.0.0/0) via brandväggen (nästa hopp = virtuell installation IP-adress):

1. Lokal undernätstrafik med ingen nästa hopp definierats för att tillåta lokal undernätstrafik passera brandväggen.
2. Trafik i virtuella nätverk med en nästa hopp har definierats som brandväggen. Den här nästa hopp åsidosätter Standardregeln som tillåter lokala virtuella nätverkstrafik att dirigera direkt.
3. Alla återstående trafik (0/0) med en nästa hopp har definierats som brandväggen.

> [!TIP]
> Har inte lokala undernät posten i UDR radbrytningar undernätet kommunikation.
>
> * I vårt exempel är 10.0.1.0/24 pekar på VNETLocal viktigt! Utan den misslyckas paket lämnar webbservern (10.0.1.4) till en annan lokal server (till exempel) 10.0.1.25, vilket kommer att skickas till en NVA. En NVA skickar den till undernätet och undernätet skicka det till en NVA i en oändlig loop.
> * Risken för en routningsslinga är vanligtvis högre på installationer med flera nätverkskort som är anslutna till olika undernät, som ofta är av traditionell, lokala installationer.
>
>

När routningstabeller skapas måste vara bunden till sina undernät. Frontend undernät routningstabellen skapas en gång och bunden till undernät, ser ut som den här utdata:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR kan nu tillämpas på gateway-undernätet som ExpressRoute-kretsen är ansluten.
>
> Exempel på hur du aktiverar perimeternätverket med ExpressRoute eller plats-till-plats-nätverk som visas i exempel 3 och 4.
>
>

#### <a name="ip-forwarding-description"></a>Beskrivning av IP-vidarebefordran
IP-vidarebefordran är en tillhörande funktion som UDR. IP-vidarebefordran är en inställning på en virtuell installation som gör att det kan ta emot trafik som inte är adresserad till enhet och vidarebefordra trafiken till destinationen ultimate.

Om AppVM01 gör en förfrågan till servern DNS01, skulle UDR till exempel vidarebefordra trafiken till brandväggen. Med IP-vidarebefordran aktiverad, är trafiken för DNS01 mål (10.0.2.4) accepteras av installation (10.0.0.4) och sedan vidarebefordras till dess slutliga destinationen (10.0.2.4). Utan IP-vidarebefordran aktiverad i brandväggen, kan trafik inte godkännas av anordningen även om routningstabellen har brandväggen som nexthop. Om du vill använda en virtuell installation är det viktigt att komma ihåg att aktivera IP-vidarebefordring tillsammans med UDR.

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan in med en enskild regel. Den här gruppen binds sedan bara till frontend och backend-undernät (inte SecNet). Deklarativt skapas följande regel:

* All trafik (alla portar) från Internet till hela virtuella nätverket (alla undernät) nekas.

Även om NSG: er som används i det här exemplet är dess huvudsakliga syfte som en sekundär försvarslinje mot manuell felaktig konfiguration. Målet är att blockera all inkommande trafik från Internet till de frontend- eller undernät. Trafik bör endast flödar genom brandväggen SecNet undernätet (och därefter, vid behov, in frontend- eller undernät). Dessutom med UDR regler och skulle all trafik som har gjort i frontend- eller undernät dirigeras ut till brandväggen (tack vare UDR). Brandväggen ser den här trafiken som en asymmetrisk dataflöde och vill ta bort utgående trafik. Det finns därför tre lager av säkerhet som skyddar undernät:

* Inga offentliga IP-adresser på alla FrontEnd och BackEnd-nätverkskort.
* NSG: er nekar trafik från Internet.
* Brandväggen släppa asymmetriska trafiken.

En intressant avseende NSG: N i det här exemplet är att den innehåller endast en regel som ska neka Internet-trafik till hela virtuella nätverk, inklusive säkerhet undernätet. Men eftersom NSG: N är bara bunden till frontend och backend-undernät, regeln inte bearbetas på trafik inkommande till undernätet för säkerhet. Detta innebär att trafiken flödar till undernätet för säkerhet.

#### <a name="firewall-rules"></a>Brandväggsregler
I brandväggen ska vidarebefordran av regler skapas. Eftersom brandväggen blockerar eller vidarebefordran alla inkommande, utgående och inom virtuella nätverkstrafik, behövs många brandväggsregler. All inkommande trafik träffar också Security Service offentliga IP-adress (olika portar) för att kunna bearbetas av brandväggen. Bästa praxis är att diagram logiska flöden innan du konfigurerar undernäten och brandväggsregler för att undvika omarbeta senare. Följande bild är en logisk vy för brandväggsregler för det här exemplet:

![10]

> [!NOTE]
> Baserat på den virtuella nätverksenhet som används variera av hanteringsportar. I det här exemplet refereras Barracuda nästa generation brandvägg, som använder port 22, 801 och 807. I dokumentationen installation leverantören för att hitta exakt vilka portar som används för hantering av den enhet som används.
>
>

#### <a name="firewall-rules-description"></a>Brandväggen regler beskrivning
I föregående logiskt diagram visas säkerhet undernätet inte eftersom brandväggen är den enda resursen i undernätet. Diagrammet visas brandväggsreglerna och hur de logiskt tillåta eller neka trafikflöden, inte den faktiska routade sökvägen. Dessutom externa portar som valts för RDP-trafik är högre låg portar (8014 – 8026) och har valts för löst överensstämmer med de två sista oktetterna i lokal IP-adress för att lättare att läsa (exempelvis lokala serveradress 10.0.1.4 är associerad med externa porten 8014). Högre icke motstridig portar, men kan användas.

I det här exemplet behöver vi sju typer av regler:

* Externa regler (för inkommande trafik):
  1. Hantering av brandväggsregel: den här appen omdirigera regeln tillåter trafik skickas till hanteringsportar för virtuell nätverksenhet.
  2. RDP-regler (för varje Windows server): de här fyra reglerna (en för varje server) kan hantera enskilda servrar via RDP. Fyra RDP-regler kan också vara dolda i en regel, beroende på den virtuella nätverksenhet som används.
  3. Regler för nätverkstrafik för programmet: det finns två reglerna kan först för frontwebbservern trafik och andra för backend-trafik (t.ex, webbservern till datanivå). Konfigurationen av dessa regler är beroende av den nätverksarkitekturen (där servrarna placeras) och trafiken flöden (vilken riktning trafikflöde och vilka portar som används).
     * Den första regeln tillåter faktiska programtrafik till programservern. Medan de andra reglerna tillåter för säkerhet och hantering, är program-trafik vad tillåta externa användare eller tjänster att få åtkomst till program. I det här exemplet att det finns en enda webbserver på port 80. Ett program för en enda brandväggsregel omdirigerar därför inkommande trafik till externa IP, web servrar interna IP-adress. Den omdirigerade trafik sessionen skulle översättas via NAT till den interna servern.
     * Den andra regeln är backend-regeln för att tillåta att webbservern ska kommunicera med AppVM01 server (men inte AppVM02) via alla portar.
* Interna regler (för trafik i inom virtuella nätverk)
  1. Utgående till regel för Internet: den här regeln tillåter trafik från alla nätverk ska skickas till de valda nätverken. Den här regeln är vanligtvis en standardregel redan i brandväggen, men i ett inaktiverat tillstånd. Den här regeln ska aktiveras för det här exemplet.
  2. DNS-regel: den här regeln kan endast DNS (port 53) trafik skickas till DNS-servern. De flesta trafik från klienten till serverdelen för den här miljön är blockerad. Den här regeln kan särskilt DNS från alla lokala undernätet.
  3. Undernät för regeln för undernätet: den här regeln är att låta alla servrar på backend-undernät kan ansluta till alla servrar på frontend-undernät (men inte omvänt).
* Felsäker regel (för trafik som inte uppfyller något av föregående):
  1. Neka alla trafikregel: neka-regeln ska alltid vara sista regeln (vad gäller prioritet), och som sådan om ett trafikflöde inte matchar någon av de föregående regler tas den bort av den här regeln. Den här regeln är en standardregel och vanligtvis på plats och vara aktiv. Inga ändringar krävs vanligtvis för att den här regeln.

> [!TIP]
> På den andra program trafik regeln för att förenkla det här exemplet är tillåts alla portar. I ett verkligt scenario ska mest specifika portar och adressintervall användas för att minska risken för angrepp på den här regeln.
>
>

När tidigare regler skapas, är det viktigt att granska prioriteten för varje regel för att se till att trafik tillåts eller nekas efter behov. I det här exemplet är reglerna i prioritetsordning.

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är en mer komplex men slutföra sätt att skydda och isolering av nätverk än i föregående exempel. (Exempel 2 skyddar bara programmet och exempel 1 isolerar bara undernät). Den här designen kan för övervakning i båda riktningarna och skyddar inte bara inkommande programservern men tillämpar nätverkets säkerhetsprincip för alla servrar på nätverket. Även, beroende på den enhet som används för fullständig trafik gransknings- och medvetenhet kan uppnås. Mer information finns i [detaljerade instruktioner build][Example3]. De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje UDR NSG kommandot och brandväggsregel.
* Detaljerad trafik flödet scenarier visar hur trafik tillåts eller nekas i varje lager.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exempel 4 lägga till en hybridanslutning med en plats-till-plats, virtuella installation VPN
[Tillbaka till snabb start](#fast-start) | Detaljerade build instruktioner tillgänglig snart

[![11]][11]

#### <a name="environment-description"></a>Beskrivning av miljö
Hybridnätverk med hjälp av en virtuell nätverksenhet (NVA) kan läggas till någon av de typer av perimeternätverket beskrivs i exempel 1, 2 eller 3.

I föregående bild visas en VPN-anslutning via Internet (plats-till-plats) används för att ansluta ett lokalt nätverk till Azure-nätverk via en NVA.

> [!NOTE]
> Om du använder ExpressRoute med alternativet Azure offentlig Peering är aktiverat kan ska en statisk väg skapas. Den här statiska vägen ska vidarebefordra till NVA VPN IP-adressen i ditt företags Internet och inte via ExpressRoute-anslutningen. NAT krävs på ExpressRoute Azure offentlig Peering-alternativet kan bryta VPN-sessionen.
>
>

När VPN-anslutningen är på plats blir en NVA central knutpunkt för alla nätverk och undernät. Bestämma vilka trafikflöden brandväggsregler för vidarebefordran översätts via NAT omdirigeras eller tas bort (även för trafikflödet mellan lokala nätverk och Azure).

Trafikflöden bör beaktas noggrant, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av inbyggda exempel 3 miljön och sedan lägga till en plats-till-plats VPN-hybrid nätverksanslutning, skapar du följande design:

[![12]][12]

Den lokala routern eller annan nätverksenhet som är kompatibel med din NVA för VPN, skulle vara VPN-klienten. Fysiska enheten ska ansvara för att initiera och underhålla din NVA VPN-anslutningen.

Logiskt att en NVA nätverket ser ut som fyra separata ”säkerhetszoner” med regler på en NVA som primär chef för trafik mellan dessa zoner:

![13]

#### <a name="conclusion"></a>Sammanfattning
För att lägga till en plats-till-plats VPN-hybrid nätverksanslutning till Azure-nätverk kan utöka det lokala nätverket till Azure på ett säkert sätt. Med en VPN-anslutning trafiken krypteras och skickar via Internet. En NVA i det här exemplet innehåller en central plats för att tillämpa och hantera säkerhetsprincipen. Mer information finns i anvisningarna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeternätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad trafik flödet scenarier visar hur trafiken flödar via den här designen.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exempel 5 lägga till en hybridanslutning med en plats-till-plats, Azure VPN-gateway
[Tillbaka till snabb start](#fast-start) | Detaljerade build instruktioner tillgänglig snart

[![14]][14]

#### <a name="environment-description"></a>Beskrivning av miljö
Hybridnätverk med hjälp av en Azure VPN-gateway kan läggas till antingen nätverkstyp för perimeter som beskrivs i exempel 1 eller 2.

I föregående bild visas en VPN-anslutning via Internet (plats-till-plats) används för att ansluta ett lokalt nätverk till Azure-nätverk via en Azure VPN-gateway.

> [!NOTE]
> Om du använder ExpressRoute med alternativet Azure offentlig Peering är aktiverat kan ska en statisk väg skapas. Den här statiska vägen ska vidarebefordra till NVA VPN IP-adressen i ditt företags Internet och inte via ExpressRoute WAN. NAT krävs på ExpressRoute Azure offentlig Peering-alternativet kan bryta VPN-sessionen.
>
>

Följande bild visar två nätverk kanter i det här exemplet. På den första kanten NVA och NSG: er kontroll av trafikflöden för inom Azure-nätverk och mellan Azure och Internet. Den andra kanten är Azure VPN-gateway, vilket är en separat och isolerade nätverk kant mellan lokala och Azure.

Trafikflöden bör beaktas noggrant, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av inbyggda i exempel 1 miljön och sedan lägga till en plats-till-plats VPN-hybrid nätverksanslutning, skapar du följande design:

[![15]][15]

#### <a name="conclusion"></a>Sammanfattning
För att lägga till en plats-till-plats VPN-hybrid nätverksanslutning till Azure-nätverk kan utöka det lokala nätverket till Azure på ett säkert sätt. Med inbyggda Azure VPN-gatewayen kan trafiken är IPSec-krypterad och dirigerar via Internet. Dessutom kan använder Azure VPN-gatewayen ge en lägre kostnad alternativet (inga ytterligare licenser kostnad som NVAs från tredje part). Det här alternativet är mest ekonomiska i exempel 1, där ingen NVA används. Mer information finns i anvisningarna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeternätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad trafik flödet scenarier visar hur trafiken flödar via den här designen.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exempel 6 Lägg till en hybridanslutning med ExpressRoute
[Tillbaka till snabb start](#fast-start) | Detaljerade build instruktioner tillgänglig snart

[![16]][16]

#### <a name="environment-description"></a>Beskrivning av miljö
Hybridnätverk med hjälp av en ExpressRoute-privat peering anslutning kan läggas till antingen nätverkstyp för perimeter som beskrivs i exempel 1 eller 2.

I föregående bild visas privat peering i ExpressRoute innehåller en direkt anslutning mellan ditt lokala nätverk och virtuella Azure-nätverket. Trafik transits endast service provider nätverks- och Microsoft Azure-nätverk, som aldrig vidrör Internet.

> [!TIP]
> Med hjälp av ExpressRoute behåller företagets nätverkstrafik från Internet. Dessutom kan serviceavtal från ExpressRoute-providern. Azure-Gateway kan klara upp till 10 Gbit/s med ExpressRoute, med plats-till-plats-VPN, Azure Gateway maximalt dataflöde är 200 Mbit/s.
>
>

Som det visas i följande diagram med det här alternativet har miljön nu två nätverk kanter. NVA och NSG styr trafikflöde för inom Azure-nätverk och mellan Azure och Internet, medan gatewayen är en separat och isolerade nätverk kant mellan lokala och Azure.

Trafikflöden bör beaktas noggrant, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av inbyggda i exempel 1 miljön och sedan lägga till en ExpressRoute hybrid nätverksanslutning, skapar du följande design:

[![17]][17]

#### <a name="conclusion"></a>Sammanfattning
Lägga till en privat ExpressRoute-Peering-nätverksanslutning utöka lokala nätverk till Azure i en säker, lägre latens, högre utför sätt. Med hjälp av inbyggda Azure Gateway, som i följande exempel tillhandahåller också en lägre kostnad alternativet (ingen ytterligare licensiering som NVAs från tredje part). Mer information finns i anvisningarna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeternätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad trafik flödet scenarier visar hur trafiken flödar via den här designen.

## <a name="references"></a>Referenser
### <a name="helpful-websites-and-documentation"></a>Användbara webbplatser och dokumentation
* Åtkomst till Azure med Azure Resource Manager:
* Åtkomst till Azure med PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Virtuella nätverk dokumentation: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Network security group-dokumentationen: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Användardefinierade routning dokumentation: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Azure virtuella gateways: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Plats-till-plats-VPN: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* ExpressRoute dokumentation (inte glöm att checka ut avsnitten ”komma igång” och ”How To”): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "flödesschema för säkerhetsalternativ"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "azure säkerhetsfunktioner"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A DMZ i ett företagsnätverk"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "azure säkerhetsarkitekturen"
[5]: ./media/best-practices-network-security/dmzazure.png "en DMZ i Azure-nätverk"
[6]: ./media/best-practices-network-security/dmzhybrid.png "hybrid nätverk med tre säkerhetsgränser"
[7]: ./media/best-practices-network-security/example1design.png "inkommande DMZ med NSG"
[8]: ./media/best-practices-network-security/example2design.png "inkommande DMZ med NVA och NSG"
[9]: ./media/best-practices-network-security/example3design.png "dubbelriktat perimeternätverk med NVA, NSG och UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "logisk vy för brandväggsregler"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ med NVA som är anslutna Hybrid"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ med NVA som är anslutna via ett plats-till-plats-VPN"
[13]: ./media/best-practices-network-security/example4networklogical.png "logiskt nätverk från NVA perspektiv"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ med Azure-Gateway som är anslutna plats-till-plats-Hybrid"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ med Azure med hjälp av plats-till-plats VPN-Gateway"
[16]: ./media/best-practices-network-security/example6designoptions.png "ExpressRoute Hybrid är anslutna till perimeternätverket med Azure-Gateway"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ med Azure-Gateway med hjälp av en ExpressRoute-anslutning"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
