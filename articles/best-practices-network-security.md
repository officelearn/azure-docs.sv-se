---
title: Azure-nätverk säkerhetsmetoder | Microsoft Docs
description: Lär dig en del av de viktigaste funktionerna som är tillgängliga i Azure för att skapa säkra nätverksmiljöer
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
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619569"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Microsofts molntjänster och nätverkssäkerhet
Microsoft-molntjänster leverera hyperskalade tjänster och infrastruktur, funktioner i företagsklass och många alternativ för hybridanslutning. Kunderna kan välja att få åtkomst till dessa tjänster via Internet eller med Azure ExpressRoute, som tillhandahåller privata nätverksanslutningar. Microsoft Azure-plattformen kan kunder sömlöst utöka sin infrastruktur till molnet och skapa flera nivåer arkitektur. Tredje part kan dessutom aktivera förbättrade funktioner genom att erbjuda tjänster och virtuella installationer. Detta faktablad innehåller en översikt över säkerhet och arkitektur problem som kunder bör överväga när du använder Microsoft-molntjänster som kan nås via ExpressRoute. Den behandlar också hur du skapar säkrare tjänster i Azure-nätverk.

## <a name="fast-start"></a>Snabbstart
Följande logik-diagram kan be dig ett exempel på de många security teknikerna som är tillgängliga med Azure-plattformen. För snabb referens hittar du i exemplet som bäst passar ditt ärende. Utökade förklaringar fortsätta att läsa igenom artikeln.
[![0]][0]

[Exempel 1: Skapa ett perimeternätverk (även kallat DMZ, demilitariserad zon eller avskärmat undernät) för att skydda program med nätverkssäkerhetsgrupper (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exempel 2: Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG: er.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exempel 3: Skapa ett perimeternätverk för att skydda nätverk med en brandvägg, en användardefinierad väg (UDR) och en NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exempel 4: Lägg till en hybridanslutning med ett plats-till-plats, virtuell installation virtuellt privat nätverk (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exempel 5: Lägg till en hybridanslutning med en plats-till-plats, Azure VPN gateway.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exempel 6: Lägg till en hybridanslutning med ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Exempel för att lägga till anslutningar mellan virtuella nätverk, hög tillgänglighet och tjänstlänkning läggs till det här dokumentet under de kommande månaderna.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Skydd för Microsoft-efterlevnad och infrastruktur
Microsoft erbjuder fler än 40 certifieringar och attesteringar som hjälper företag att uppfylla nationella, regionala och branschspecifika krav för insamling och användning av enskilda personers data. Den mest omfattande uppsättningen av alla molntjänstleverantörer.

Mer information finns i informationen om efterlevnad på den [Microsoft Trust Center][TrustCenter].

Microsoft har en omfattande metod för att skydda moln-infrastruktur som behövs för att köra storskaliga globala tjänster. Microsoft-molninfrastruktur innehåller maskinvara, programvara, nätverk, samt administrativa och personalstyrka, förutom de fysiska datacenter.

![2]

Den här metoden ger en säkrare grund för kunder att distribuera sina tjänster i Microsoft-molnet. Nästa steg är avsedd för kunder att utforma och skapa en Säkerhetsarkitektur för att skydda dessa tjänster.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Traditionell säkerhetsarkitekturer och perimeternätverk
Även om Microsoft investerar kraftigt i skyddar molninfrastrukturen, måste även kunder att skydda sina molntjänster och resursgrupper. En flerskiktiga strategi inom säkerhet ger det bästa skyddet. En säkerhetszon för perimeternätverket nätverket skyddar interna nätverksresurser från ett icke betrott nätverk. Ett perimeternätverk refererar till kanter eller delar av nätverket som finns mellan Internet och skyddade enterprise IT-infrastruktur.

I typiska företagsnätverk spikas kraftigt grundläggande infrastruktur på perimetrar med flera lager av säkerhetsenheter. Gränsen för varje nivå består av enheter och tillämpningspunkter för principen. Varje lager kan innehålla en kombination av följande security nätverksenheterna: brandväggar, Denial of Service (DoS) förebygga, intrångsidentifiering eller Protection system (IDS/IPS) och VPN-enheter. Genomförande av principer kan bestå av Brandväggsprinciper, åtkomstkontrollistor (ACL) eller specifika routning. Den första försvarslinjen i nätverket, direkt tar emot inkommande trafik från Internet, är en kombination av dessa mekanismer för att blockera attacker och skadlig trafik samtidigt som legitima begäranden vidare till nätverket. Den här trafiken dirigerar direkt till resurser i perimeternätverket. Den här resursen kan sedan ”prata” till resurser som är djupare i nätverket, transit nästa gränsen för verifiering första. Det yttersta lagret kallas perimeternätverket eftersom den här delen av nätverket är exponerad för Internet, vanligtvis med någon form av skydd på båda sidorna. Följande bild visar ett exempel på ett enda undernät perimeternätverk i ett företagsnätverk, med två säkerhetsgränser.

![3]

Det finns många arkitekturer som används för att implementera ett perimeternätverk. Dessa arkitekturer kan vara allt från enkla belastningsutjämning till ett perimeternätverk med flera undernät med olika mekanismer vid varje gränsen som blockerar trafiken och skydda djupare lagren i företagsnätverket. Hur perimeternätverket är uppbyggd beror på de specifika behoven i organisationen och dess övergripande risktolerans.

När kunder flytta sina arbetsbelastningar till offentliga moln, är det viktigt att ge stöd för liknande funktioner för perimeternätverket nätverksarkitektur i Azure för att uppfylla efterlevnads- och säkerhetskrav. Det här dokumentet innehåller riktlinjer om hur kunder kan skapa en säker nätverksmiljö i Azure. Det fokuserar på perimeternätverket, men innehåller även en heltäckande beskrivning av många aspekter av nätverkssäkerhet. Följande frågor informera den här diskussionen:

* Hur byggas ett perimeternätverk i Azure?
* Vilka är några av de Azure-funktionerna som kan bygga perimeternätverket?
* Hur kan backend-arbetsbelastningar skyddade?
* Hur styrs Internet-kommunikation för arbetsbelastningar i Azure?
* Hur kan lokala nätverk skyddas från distributioner i Azure?
* När ska jag använda inbyggda säkerhetsfunktioner för Azure jämfört med tredjeparts-utrustning eller tjänster?

Följande diagram visar olika säkerhetsnivåer Azure ger kunderna. Dessa lager är både inbyggd i själva Azure-plattformen och kunddefinierad funktioner:

![4]

Inkommande från Internet, Azure DDoS som hjälper dig att skydda mot storskaliga attacker mot Azure. Nästa lager är kunddefinierad offentliga IP-adresser (slutpunkter), som används för att avgöra vilken trafik kan passera Molntjänsten till det virtuella nätverket. Intern Azure virtuell nätverksisolering säkerställer fullständig isolering från alla andra nätverk och att det bara flödar trafiken genom Användarkonfigurerad sökvägar och metoder. Dessa sökvägar och metoder är nästa lager där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa säkerhetsgränser för att skydda programdistributioner i skyddat nätverk.

Nästa avsnitt innehåller en översikt över Azure-nätverk. Dessa virtuella nätverk har skapats av kunder och är sina distribuerade arbetsbelastningar är anslutna till. Virtuella nätverk utgör grunden för alla de funktioner för nätverkssäkerhet krävs för att upprätta ett perimeternätverk för att skydda distributioner i Azure.

## <a name="overview-of-azure-virtual-networks"></a>Översikt över Azure-nätverk
Innan Internet-trafik kan få till Azure-nätverk finns två nivåer av säkerhet kommer med Azure-plattformen:

1.    **DDoS protection**: DDoS protection är ett lager av Azure fysiska nätverk som skyddar själva Azure-plattformen från storskaliga Internet-baserade. Dessa attacker använda flera ”bot”-noder i ett försök utnyttjandet en Internettjänst. Azure har ett robust DDoS protection nät alla inkommande, utgående och mellan Azure-region-anslutning. Det här lagret för DDoS-skydd har inga konfigurerbara användarattribut och är inte tillgänglig för kunden. Lagret DDoS protection skyddar Azure som en plattform från storskaliga attacker, den övervakar även ut bundna trafik och trafik över flera Azure-regioner. Med virtuell nätverksutrustning på det virtuella nätverket, kan ytterligare skyddslager för återhämtning konfigureras av kunden mot en mindre skala attack som inte utlösas skydd för plattform på nätverksnivå. Ett exempel på DDoS i åtgärden. Om en IP-adress för internetuppkopplad angreps av storskaliga DDoS-attacker, skulle Azure identifiera källorna av attackerna och Skrubba trafiken felaktiga innan den har nått sin destination. I nästan alla fall påverkas inte slutpunkten angripna attack. I sällsynta fall att en slutpunkt påverkas påverkas ingen trafik till andra slutpunkter, angripna slutpunkten. Andra kunder och -tjänster skulle därför se utan att påverka från den attacken. Det är viktigt att notera att Azure DDoS bara söker efter storskaliga attacker. Det är möjligt att din specifika tjänst överbelastas innan plattform skydd på nätverksnivå tröskelvärden överskrids. Till exempel kan en webbplats på en enda A0 IIS-server, kopplas från av en DDoS-attack innan Azure-plattformsnivå DDoS protection registrerat ett hot.

2.  **Offentliga IP-adresser**: Offentliga IP-adresser (aktiverat via tjänstslutpunkter, offentliga IP-adresser, Application Gateway och andra Azure-funktioner som presenterar en offentlig IP-adress på Internet som dirigeras till din resurs) gör cloud services eller resurs grupper har offentliga Internet-IP adresser och portar som visas. Slutpunkten använder Network Address Translation (NAT) för att dirigera trafik till den interna adressen och porten på Azure-nätverket. Den här sökvägen är det vanligaste sättet för extern trafik till virtuella nätverk. Offentliga IP-adresser kan konfigureras för att avgöra vilken trafik som skickas och hur och var den översätts till det virtuella nätverket.

När trafiken når det virtuella nätverket, finns det många funktioner som har betydelse. Azure-nätverk är grunden för kunder att ansluta sina arbetsbelastningar och där grundläggande säkerhet på nätverksnivå gäller. Det är ett privat nätverk (ett virtuellt nätverk överlägg) i Azure för kunder med följande funktioner och egenskaper:

* **Trafik isolering**: Ett virtuellt nätverk är gränsen för isolering av trafik på Azure-plattformen. Virtuella datorer (VM) i ett virtuellt nätverk kan inte kommunicera direkt till virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverken har skapats av samma kund. Isolering är en viktig egenskap som ser till kundens virtuella datorer och kommunikation förblir privata inom ett virtuellt nätverk.

>[!NOTE]
>Trafikisolering avser endast trafik *inkommande* till det virtuella nätverket. Som standard utgående trafik från det virtuella nätverket till internet tillåts, men kan förhindras om du vill med NSG: er.
>
>

* **Topologi i flera skikt**: Virtuella nätverk kan kunderna definiera topologi i flera skikt genom att allokera undernät och bestämma separat adressutrymmen för olika element eller ”nivåer” arbetsbelastningarna. Dessa logiska grupperingar topologier ger kunderna möjlighet att definiera olika åtkomstprincip baserat på typerna av arbetsbelastningar och också styra trafikflödet mellan nivåerna.
* **Flera platsanslutningar**: Kunderna kan upprätta flera platsanslutningar mellan ett virtuellt nätverk och flera lokala platser eller andra virtuella nätverk i Azure. Kunder kan använda VNet-Peering, Azure VPN gateway, fristående nätverkets virtuella installationer eller ExpressRoute för att skapa en anslutning. Azure har stöd för plats-till-plats (S2S) VPN-nätverk via IPsec/IKE-standardprotokoll och privat ExpressRoute-anslutningen.
* **NSG** kan kunderna skapa regler (ACL) på önskad nivå av kornighet: gränssnitt, enskilda virtuella datorer eller virtuella undernät. Kunder kan styra åtkomsten genom att tillåta eller neka kommunikationen mellan arbetsbelastningar inom ett virtuellt nätverk, från system i kundens virtuella nätverk via flera platsanslutningar, eller direkt Internet-kommunikation.
* **UDR** och **IP-vidarebefordran** kan kunderna definiera kommunikationsvägar mellan olika nivåer inom ett virtuellt nätverk. Kunder kan distribuera en brandvägg, IDS/IPS och andra virtuella installationer och dirigerar nätverkstrafik via dessa funktioner för nätverkssäkerhet för tvingande säkerhetsprinciper gräns, granskning och kontroll.
* **Virtuella nätverksinstallationer** på Azure Marketplace: Säkerhetsenheter som brandväggar, belastningsutjämnare och IDS/IPS är tillgängliga i Azure Marketplace och VM-bildgalleriet. Kunder kan distribuera dessa installationer i sina virtuella nätverk, och mer specifikt på sina säkerhetsgränser (inklusive perimeternätverket nätverk undernät) för att slutföra en affärsnivåerna säker nätverksmiljö.

Med dessa funktioner och möjligheter är ett exempel på hur en nätverksarkitektur i perimeternätverket kan konstrueras i Azure i följande diagram:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Egenskaper för perimeter-nätverk och krav
Perimeternätverket är klientdelen nätverkets direkt samverkar kommunikation från Internet. Inkommande paket ska flöda via säkerhetsenheter som brandväggen, ID: N och IP-adresser, innan de når backend servrarna. Internet-bunden paket från arbetsbelastningarna kan också flödar genom säkerhetsenheter i perimeternätverket för genomförande av principer, kontroll och granskning, innan de lämnar nätverket. Perimeternätverket kan dessutom vara värd för lokala VPN-gatewayer mellan kundens virtuella nätverk och lokala nätverk.

### <a name="perimeter-network-characteristics"></a>Perimeter Nätverksegenskaper
Referera till föregående bild, är vissa av egenskaperna för ett bra perimeternätverk följande:

* Internet-ansluten:
  * Själva undernätet för nätverket perimeternätverk är Internet-ansluten, kommunicerar direkt med Internet.
  * Offentliga IP-adresser, virtuella IP-adresser och/eller tjänstslutpunkter skicka Internettrafik till klientnätverk och enheter.
  * Inkommande trafik från Internet passerar genom säkerhetsenheter innan andra resurser på klientnätverk.
  * Om utgående skyddad passerar trafik genom säkerhetsenheter som det sista steget, innan du går till Internet.
* Skyddat nätverk:
  * Det finns ingen direkt väg från Internet till grundläggande infrastruktur.
  * Kanaler till grundläggande infrastruktur måste passera genom den säkerhetsenheter som NSG: er, brandväggar eller VPN-enheter.
  * Andra enheter måste inte överbryggar Internet och grundläggande infrastruktur.
  * Säkerhetsenheter på både Internet-ansluten och skyddat nätverk mot gränserna i perimeternätverket (till exempel de två brandvägg ikoner som visas i föregående bild) kanske faktiskt en enda virtuell installation med differentierade regler eller gränssnitt för varje gräns. Till exempel en fysisk enhet logiskt separerade kan hantera belastningen för både perimeternätverket-gränser.
* Andra vanliga metoder och begränsningar:
  * Arbetsbelastningar måste inte lagra viktiga affärsinformation.
  * Åtkomst och uppdateringar av konfigurationer för perimeter-nätverk och distributioner begränsas till endast auktoriserade administratörer.

### <a name="perimeter-network-requirements"></a>Krav på perimeternätverket
Följ dessa riktlinjer på virtuellt nätverkskrav på att implementera en lyckad perimeternätverk om du vill aktivera följande egenskaper:

* **Arkitektur för undernätet:** Ange det virtuella nätverket så att en hela undernätet är dedikerad som perimeternätverket, separeras från andra undernät i samma virtuella nätverk. Den här separationen innebär trafiken mellan perimeternätverket och andra nivåer flöden i intern eller privat undernät via en brandvägg eller IDS/IPS virtuell installation.  Användardefinierade vägar på gränsen undernät krävs för att vidarebefordra trafiken till den virtuella installationen.
* **NSG:** Själva undernätet för nätverket perimeter ska vara öppet för att tillåta kommunikation med Internet, men som innebär inte kunderna bör kringgår NSG: er. Följ vanliga säkerhetsrutiner för att minimera nätverk Surface-enheter som är exponerade för Internet. Lås remote adressintervallen tillgång till distributioner eller programprotokoll och portar som är öppna. Det kan finnas omständigheter, men som en fullständig låsning inte är möjligt. Till exempel om kunder har en extern webbplats i Azure, perimeternätverket ska tillåta inkommande webbegäranden från alla offentliga IP-adresser, men bara öppna programmet webbportar: TCP på port 80 och/eller TCP på port 443.
* **Routningstabellen:** Själva undernätet för nätverket perimeter ska kunna kommunicera direkt till Internet, men bör inte tillåta direkt kommunikation till och från de tillbaka slut eller lokalt nätverk utan att gå via en brandvägg eller installation.
* **Säkerhetskonfiguration för installation:** Om du vill dirigera och inspektera paket mellan perimeternätverket och resten av de skyddade nätverk, säkerhetsenheter som brandväggen, ID: N och IP-adresser ha enheter flera värdar. De kan ha separata nätverkskort för perimeternätverket och backend-undernät. Nätverkskorten i perimeternätverket kommunicera direkt till och från Internet, med motsvarande NSG: er och routningstabellen för perimeter-nätverk. Nätverkskort som ansluter till backend-undernät har mer begränsade NSG: er och routningstabeller motsvarande backend-undernät.
* **Security enhetens funktioner:** Säkerhetsenheter som distribueras i perimeternätverket vanligtvis utför följande funktioner:
  * Brandvägg: Tillämpa regler för brandväggar eller principer för åtkomstkontroll för inkommande begäranden.
  * Hotidentifiering och skydd: Identifiera och åtgärda skadliga attacker från Internet.
  * Granskning och loggning: Underhålla detaljerade loggar för granskning och analys.
  * Omvänd proxy: Omdirigering av inkommande begäranden till motsvarande backend-servrarna. Den här omdirigering omfattar mappning och vanligtvis översätta måladresser på frontend-enheter, brandväggar, till backend-server-adresser.
  * Proxy för vidarebefordran: Tillhandahåller NAT och utför granskning för kommunikation som initieras från inom det virtuella nätverket till Internet.
  * Router: Vidarebefordran av inkommande och mellan undernät trafik i virtuella nätverk.
  * VPN-enhet: Fungerar som mellan lokala VPN-gatewayer för flera VPN-anslutningen mellan kundens lokala nätverk och virtuella Azure-nätverk.
  * VPN-servern: Accepterar VPN-klienter som ansluter till Azure-nätverk.

> [!TIP]
> Håll följande två grupper separat: enskilda användare behörighet att komma åt perimeter network security gear och individer som auktoriserad som administratörer för utveckling, distribution eller åtgärder. Avgränsa dessa grupper kan en ansvarsfördelning och förhindrar att en enskild person kringgår både program-säkerhet och nätverk säkerhetskontroller.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Frågor för att svara när du skapar nätverksgränser
Om inte uttryckligen nämns i det här avsnittet refererar termen ”nätverk” till privat Azure-nätverk som skapats av en administratör för prenumerationen. Termen refererar inte till de underliggande fysiska nätverken i Azure.

Azure-nätverk används också ofta att utöka traditionella lokala nätverk. Det är möjligt att införliva plats-till-plats eller ExpressRoute-hybridlösningar nätverk med nätverksarkitekturer i perimeternätverket. Den här länken för hybrid är viktigt att skapa nätverksgränser för säkerhet.

Följande tre frågor är viktiga för att svara på när du skapar ett nätverk med ett perimeternätverk och flera säkerhetsgränser.

#### <a name="1-how-many-boundaries-are-needed"></a>(1) hur många gränser krävs?
Den första beslutspunkten är att bestämma hur många säkerhetsgränser behövs i ett visst scenario:

* En enda gräns: En på klientsidan perimeternätverket, mellan det virtuella nätverket och Internet.
* Två gränser: En på Internet-sida i perimeternätverket och en annan mellan undernät perimeter och backend-undernät i Azure-nätverk.
* Tre gränser: En på Internet-sidan i perimeternätverket, en mellan perimeternätverket och backend-undernät och en mellan backend-undernät och det lokala nätverket.
* N-gränserna: Variabelnummer. Beroende på krav på säkerhet finns det ingen gräns för hur många säkerhetsgränser som kan användas i ett givet nätverk.

Antalet och typen av gränser som behövs varierar baserat på ett företags risktolerans och det specifika scenariot som implementerats. Det här beslutet görs ofta tillsammans med flera grupper inom en organisation, inklusive ofta ett team för risk och efterlevnad, ett nätverk och platform-teamet och en programutvecklingsteamet. Personer med kunskap om säkerhet, data som är inblandade och de tekniker som används bör ha en say i det här beslutet att kontrollera effekten av lämplig säkerhet åtgärder för varje implementering.

> [!TIP]
> Använd det lägsta antalet gränser som uppfyller säkerhetskraven för en viss situation. Med mer gränser, åtgärder och felsökning kan vara svårare, samt merarbetet ingår i hantering av flera gräns principer över tid. Otillräcklig gränser kan dock öka risken. Det är viktigt att hitta balansen.
>
>

![6]

I föregående bild visas en översikt över ett nätverk för tre security gräns. Gränserna är mellan perimeternätverket och Internet, Azure frontend och backend-privat undernät, och Azure backend-undernät och lokala företagets nätverk.

#### <a name="2-where-are-the-boundaries-located"></a>2) var finns gränserna?
När antalet gränser är valt, är var du vill implementera dem nästa beslutspunkten. Det finns vanligtvis tre alternativ:

* Med hjälp av en Internet-baserad mellanliggande tjänst (till exempel en molnbaserad Brandvägg för webbaserade program som inte beskrivs i det här dokumentet)
* Med inbyggda funktioner och/eller virtuell nätverksutrustning i Azure
* Med fysiska enheter på det lokala nätverket

Alternativen är helt och hållet Azure nätverk interna Azure-funktioner (till exempel Azure belastningsutjämnare) eller virtuella nätverksinstallationer från omfattande ekosystem av Azure (till exempel brandväggar för Check Point).

Om en gräns mellan Azure och ett lokalt nätverk, kan säkerhetsenheter finnas på endera sidan av anslutningen (eller båda sidorna). Därför måste ett beslut fattas på platsen att placera security gear.

I föregående bild Internet till perimeternätverk fram till backend-server-gränser finns helt i Azure och måste antingen vara interna Azure-funktioner eller nätverket virtuella installationer. Säkerhetsenheter på gränsen mellan Azure (backend-undernät) och företagets nätverk kan vara antingen på Azure-sidan eller den lokala sidan eller till och med en kombination av enheter på båda sidorna. Det kan finnas betydande fördelar och nackdelar med att något av alternativen som måste beaktas allvar.

Till exempel har med befintliga fysisk säkerhet växel på den lokala nätverk sida fördelen att inga nya gear krävs. Den behöver bara omkonfiguration. Nackdelen är dock att all trafik måste hämtas tillbaka från Azure till det lokala nätverket ska vara synlig för kugghjulet säkerhet. Därför Azure till Azure-trafik kan medföra betydande fördröjning och påverkar programmets prestanda och upplevelse, om det tvingades tillbaka till det lokala nätverket för tvingande säkerhetsprinciper.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) hur implementeras gränserna?
Varje säkerhetsgräns sannolikt har olika krav (t.ex, ID: N och brandväggsregler på Internet sida av perimeternätverk, men endast ACL: er mellan perimeternätverket och backend-undernät). Bestämmer där enheten (eller hur många enheter) för att använda beror på scenariot och säkerhetskrav. I följande avsnitt beskrivs vissa alternativ som kan användas i exempel 1, 2 och 3. Granska Azure inbyggda network-funktionerna och enheterna som är tillgängliga i Azure från partnerekosystemet visar de många tillgängliga alternativ för att lösa i stort sett alla scenarier.

En annan nyckel implementering beslutspunkten är att ansluta det lokala nätverket med Azure. Ska du använda Azure virtuell gateway eller en virtuell nätverksinstallation? Dessa alternativ beskrivs mer detaljerat i följande avsnitt (exempel 4, 5 och 6).

Dessutom kan kan trafik mellan virtuella nätverk i Azure behövas. Dessa scenarier kommer att läggas till i framtiden.

När du vet svaren på tidigare frågor i [Fast Start](#fast-start) avsnittet kan hjälpa dig att identifiera vilka exempel är mest lämplig för ett visst scenario.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exempel: Att skapa säkerhetsgränser med virtuella Azure-nätverk
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exempel 1 skapa ett perimeternätverk för att skydda program med NSG: er
[Tillbaka till snabb start](#fast-start) | [detaljerat skapa instruktionerna i det här exemplet][Example1]

[![7]][7]

#### <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet finns det en prenumeration som innehåller följande resurser:

- En enskild resursgrupp
- Ett virtuellt nätverk med två undernät: ”FrontEnd” och ”serverdel”
- En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
- En Windows-server som representerar en program-webbserver (”IIS01”)
- Två Windows-servrar som representerar programmet backend-servrar (”AppVM01”, ”AppVM02”)
- En Windows-server som representerar en DNS-server (”DNS01”)
- En offentlig IP-adress som är associerade med programmet webbservern

Skript och en Azure Resource Manager-mall finns i den [detaljerade instruktioner för build][Example1].

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler.

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först, följt av de mer allmänna ”Deny”-reglerna. Den angivna prioriteten avgör vilka regler utvärderas först. När trafik identifieras som gäller för en specifik regel, utvärderas inga fler regler. NSG-regler kan tillämpa i antingen inkommande eller utgående riktning (ur undernätet).
>
>

Deklarativt, byggs följande regler för inkommande trafik:

1. Interna DNS-trafik (port 53) tillåts.
2. RDP-trafik (port 3389) från Internet till någon virtuell dator tillåts.
3. HTTP-trafik (port 80) från Internet till webbservern (IIS01) tillåts.
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts.
5. All trafik (alla portar) från Internet till hela virtuellt nätverk (både undernät) nekas.
6. All trafik (alla portar) från klientdelens undernät till backend-undernätet nekas.

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande från Internet till webbservern, både regler 3 (Tillåt) och 5 (neka) skulle tillämpas. Men eftersom regel 3 har högre prioritet kan bara den skulle tillämpas och regel 5 skulle inte har betydelse. HTTP-begäran skulle därför tillåts till webbservern. Om samma trafiken försökte nå DNS01 servern, regel 5 (neka) skulle vara först med att tillämpa och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar klientdelsundernätet tala med backend-undernät (förutom tillåten trafik i reglerna 1 och 4). Den här regeluppsättningen skyddar backend-nätverket om en angripare som komprometterar webbprogrammet på klientdelen. Angriparen skulle ha begränsad åtkomst till nätverkets backend-”skyddad” (endast för resurser som visas på AppVM01 servern).

Det finns en utgående standardregel som tillåter trafik ut till Internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Om du vill låsa trafik i båda riktningarna, användardefinierade routning krävs (se exempel 3).

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och enkelt sätt att isolera serverdelsundernätet från inkommande trafik. Mer information finns i den [detaljerade instruktioner för build][Example1]. De här anvisningarna inkluderar:

* Hur du skapar den här perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar den här perimeternätverk med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje NSG-kommando.
* Detaljerad traffic flow scenarier, som visar hur trafik tillåts eller nekas i varje lager.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exempel 2 Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG: er
[Tillbaka till snabb start](#fast-start) | [detaljerat skapa instruktionerna i det här exemplet][Example2]

[![8]][8]

#### <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet finns det en prenumeration som innehåller följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med två undernät: ”FrontEnd” och ”serverdel”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En virtuell nätverksinstallation, i det här fallet en brandvägg, som är anslutna till klientdelsundernätet
* En Windows-server som representerar en program-webbserver (”IIS01”)
* Två Windows-servrar som representerar programmet backend-servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Skript och en Azure Resource Manager-mall finns i den [detaljerade instruktioner för build][Example2].

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler.

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först, följt av de mer allmänna ”Deny”-reglerna. Den angivna prioriteten avgör vilka regler utvärderas först. När trafik identifieras som gäller för en specifik regel, utvärderas inga fler regler. NSG-regler kan tillämpa i antingen inkommande eller utgående riktning (ur undernätet).
>
>

Deklarativt, byggs följande regler för inkommande trafik:

1. Interna DNS-trafik (port 53) tillåts.
2. RDP-trafik (port 3389) från Internet till någon virtuell dator tillåts.
3. Alla Internet-trafik (alla portar) till virtuell nätverksinstallation (brandvägg) tillåts.
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts.
5. All trafik (alla portar) från Internet till hela virtuellt nätverk (både undernät) nekas.
6. All trafik (alla portar) från klientdelens undernät till backend-undernätet nekas.

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande från Internet till brandväggen, både regler 3 (Tillåt) och 5 (neka) skulle tillämpas. Men eftersom regel 3 har högre prioritet kan bara den skulle tillämpas och regel 5 skulle inte har betydelse. HTTP-begäran skulle därför tillåts i brandväggen. Om samma trafiken har försöker komma åt servern IIS01, även om den finns på klientdelsundernätet, regel 5 (neka) är skulle tillämpas, och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar klientdelsundernätet tala med backend-undernät (förutom tillåten trafik i reglerna 1 och 4). Den här regeluppsättningen skyddar backend-nätverket om en angripare som komprometterar webbprogrammet på klientdelen. Angriparen skulle ha begränsad åtkomst till nätverkets backend-”skyddad” (endast för resurser som visas på AppVM01 servern).

Det finns en utgående standardregel som tillåter trafik ut till Internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Om du vill låsa trafik i båda riktningarna, användardefinierade routning krävs (se exempel 3).

#### <a name="firewall-rule-description"></a>Beskrivning av regel för brandvägg
I brandväggen, ska vidarebefordra regler skapas. Regeln krävs eftersom det här exemplet skickar endast Internet-trafik inkommande i brandväggen och sedan till webbservern, endast en vidarebefordran nätverksadressöversättning (NAT).

Vidarebefordringsregel godkänner alla inkommande källadress som kommer till brandväggen försöker nå HTTP (port 80 eller 443 för HTTPS). Den har skickas utanför i brandväggen lokala gränssnitt och omdirigeras till webbservern med IP-adressen för 10.0.1.5.

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkelt sätt att skydda ditt program med en brandvägg och isolera serverdelsundernätet från inkommande trafik. Mer information finns i den [detaljerade instruktioner för build][Example2]. De här anvisningarna inkluderar:

* Hur du skapar den här perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje NSG-regel i kommandot och brandväggen.
* Detaljerad traffic flow scenarier, som visar hur trafik tillåts eller nekas i varje lager.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exempel 3-skapa ett perimeternätverk för att skydda nätverk med en brandvägg, UDR och NSG
[Tillbaka till snabb start](#fast-start) | [detaljerat skapa instruktionerna i det här exemplet][Example3]

[![9]][9]

#### <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet finns det en prenumeration som innehåller följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med tre undernät: ”SecNet”, ”FrontEnd” och ”serverdel”
* En virtuell nätverksinstallation, i det här fallet en brandvägg, som är anslutna till undernätet SecNet
* En Windows-server som representerar en program-webbserver (”IIS01”)
* Två Windows-servrar som representerar programmet backend-servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Skript och en Azure Resource Manager-mall finns i den [detaljerade instruktioner för build][Example3].

#### <a name="udr-description"></a>UDR-beskrivning
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

VNETLocal är alltid en eller flera definierade adressprefix som tillsammans bildar det virtuella nätverket för det specifika nätverket (det vill säga ändras från virtuellt nätverk till virtuellt nätverk, beroende på hur varje specifik virtuellt nätverk har definierats). Återstående systemvägar är statiska och standard som anges i tabellen.

I det här exemplet skapas två routningstabeller ett för frontend och backend-undernät. Varje tabell har lästs in med statiska vägar som är lämpliga för det angivna undernätet. I det här exemplet varje tabell har tre vägar som dirigera all trafik (0.0.0.0/0) genom brandväggen (nästa hopp = virtuell installation IP-adress):

1. Lokal undernätstrafik med inga nästa hopp definierats för att tillåta lokal undernätstrafik passera brandväggen.
2. Trafik i virtuella nätverk med en nästa hopp har definierats som brandvägg. Den här nästa hopp åsidosätter Standardregeln som tillåter lokal trafik i virtuella nätverk att dirigera direkt.
3. Alla återstående trafik (0/0) med en nästa hopp har definierats som brandväggen.

> [!TIP]
> Har inte posten undernätet i UDR radbrytningar undernätet kommunikation.
>
> * I vårt exempel är 10.0.1.0/24 som pekar på VNETLocal viktigt! Utan den misslyckas paket som lämnar webbservern (is 10.0.1.4) är avsedd för en annan lokal server (till exempel) 10.0.1.25, vilket kommer att skickas till NVA. NVA skickar den till undernätet och undernätet kommer skicka det till NVA i en oändlig loop.
> * Risken för en routningsloop är vanligtvis högre på installationer med flera nätverkskort som är anslutna till olika undernät, vilket är ofta av traditionella, lokala installationer.
>
>

När routningstabellerna har skapats, måste vara bundet till sina undernät. Klientdelsundernätet routningstabellen skapas en gång och bunden till undernätet, ser ut som dessa utdata:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR kan nu användas till gateway-undernätet där ExpressRoute-kretsen är ansluten.
>
> Exempel på hur du aktiverar perimeternätverket med ExpressRoute eller nätverk för plats-till-plats visas i exempel 3 och 4.
>
>

#### <a name="ip-forwarding-description"></a>Beskrivning för IP-vidarebefordran
IP-vidarebefordran är en tillhörande funktion till UDR. IP-vidarebefordran är en inställning på en virtuell installation som gör att det kan ta emot trafik som inte är adresserad till installationen sedan vidarebefordrar trafiken till destinationen ultimate.

Om AppVM01 gör en begäran till servern DNS01, skulle UDR exempelvis dirigera trafiken till brandväggen. Med IP-vidarebefordring aktiverad, trafik för DNS01 mål (10.0.2.4) accepteras av installationen (10.0.0.4) och sedan vidarebefordras till dess ultimate mål (10.0.2.4). Utan IP-vidarebefordring aktiverad i brandväggen, skulle trafik inte accepteras av installationen trots routningstabellen har brandväggen som nästa hopp. Om du vill använda en virtuell installation, är det viktigt att komma ihåg att aktivera IP-vidarebefordran tillsammans med UDR.

#### <a name="nsg-description"></a>NSG-beskrivning
I det här exemplet bygger en NSG-grupp och sedan läsa in med en enda regel. Den här gruppen binds sedan bara till frontend och backend-undernät (inte SecNet). Deklarativt följande regel håller på att skapas:

* All trafik (alla portar) från Internet till hela virtuella nätverket (alla undernät) nekas.

Även om NSG: er som används i det här exemplet, är dess huvudsakliga syfte som en sekundär försvarslinje mot manuell felkonfiguration. Målet är att blockera all inkommande trafik från Internet till de klient- eller backend-undernät. Trafik endast ska flöda genom brandväggen SecNet undernätet (och sedan, om det är lämpligt in på de frontend- eller backend-undernät). Dessutom med UDR-regler på plats, skulle all trafik som gjorde det i klient- eller undernät omdirigerad ut till brandväggen (tack vare UDR). Brandväggen ser den här trafiken som en asymmetrisk flöde och skulle släpp den utgående trafiken. Det finns därför tre lager av säkerhet som skyddar undernät:

* Inga offentliga IP-adresser på alla FrontEnd och BackEnd-nätverkskort.
* Nätverkssäkerhetsgrupper som nekar trafik från Internet.
* Brandväggen släpper asymmetrisk trafiken.

En intressant avseende NSG: N i det här exemplet är att den innehåller endast en regel som är att neka Internet-trafik i hela virtuella nätverk, inklusive säkerhet undernätet. Men eftersom NSG: N är bara kopplat till frontend och backend-undernät, regeln inte bearbetas på trafik inkommande till undernätet för säkerhet. Därför flödar trafiken till undernätet för säkerhet.

#### <a name="firewall-rules"></a>Brandväggsregler
I brandväggen, ska vidarebefordra regler skapas. Eftersom brandväggen blockerar eller vidarebefordran alla inkommande, utgående och trafik virtuella nätverkstrafik krävs många brandväggsregler. All inkommande trafik når dessutom säkerhetstjänst offentliga IP-adress (på olika portar), för att bearbetas av brandväggen. Ett bra tips är att diagram logiska flöden innan du konfigurerar undernäten och brandväggsregler för att undvika att omarbeta senare. Följande bild är en logisk vy för brandväggsregler i det här exemplet:

![10]

> [!NOTE]
> Baserat på den virtuella nätverksinstallationen som används kan variera hanteringsportar. I det här exemplet en Barracuda NextGen Firewall refererar till, som använder port 22, 801 och 807. I dokumentationen för installation-leverantör för att hitta exakt portarna som används för hantering av enhet som används.
>
>

#### <a name="firewall-rules-description"></a>Brandväggen regler beskrivning
I det föregående logiska diagrammet visas inte security undernätet eftersom den är den enda resursen i undernätet. Diagrammet visar brandväggsreglerna och hur de logiskt tillåter eller nekar trafikflöden, inte den faktiska routade sökvägen. Dessutom externa portar som valts för RDP-trafik är högre intervallet portar (8014 – 8026) och har valts för löst överensstämmer med de två sista oktetterna i den lokala IP-adressen för enklare läsbarheten (exempelvis lokala serveradress is 10.0.1.4 är associerad med extern i port 8014). Alla högre icke motstridiga portar, men kan användas.

I det här exemplet behöver vi sju typer av regler:

* Externa regler (för inkommande trafik):
  1. Brandväggsregel för hantering: Den här appen omdirigeringsregel tillåter trafik skickas till hanteringsportar på den virtuella nätverksinstallationen.
  2. RDP-regler (för varje Windows-server): Dessa fyra regler (en för varje server) kan hanteringen av de enskilda servrarna via RDP. De fyra RDP-reglerna kan även döljas i en regel, beroende på den virtuella nätverksinstallationen som används.
  3. Regler för trafik för program: Det finns två av de här reglerna, först för klientdelen Internet-trafik och andra för backend-trafik (till exempel webbservern till datanivå). Konfigurationen av dessa regler är beroende av nätverksarkitekturen (där dina servrar placeras) och trafik flöden (vilken riktning trafiken flödar och vilka portar används).
     * Den första regeln kan själva programmet trafiken till programservern. Medan de andra reglerna tillåter för säkerhet och hantering, är programmet trafikregler vad tillåta externa användare eller tjänster att komma åt programmen. I det här exemplet att det finns en webbserver på port 80. Ett program för en enda brandväggsregel omdirigerar därför inkommande trafik till den externa IP-Adressen, web servrar interna IP-adress. Omdirigerad trafik sessionen skulle översättas via NAT till den interna servern.
     * Den andra regeln är backend-regeln för att tillåta att kommunicera med AppVM01 server (men inte AppVM02) via någon annan port.
* Interna regler (för trafik i trafik virtuella nätverk)
  1. Utgående till Internet-regel: Den här regeln tillåter trafik från alla nätverk ska skickas till de valda nätverken. Den här regeln är vanligtvis en standardregel redan i brandväggen, men i ett inaktiverat tillstånd. Den här regeln ska aktiveras för det här exemplet.
  2. DNS-regel: Den här regeln tillåter endast DNS (port 53)-trafik skickas till DNS-servern. De flesta trafik från klientdelen till serverdelen för den här miljön är blockerad. Den här regeln kan särskilt DNS från alla lokala undernätet.
  3. Undernät till undernätet regel: Den här regeln är att låta alla servrar i backend-undernät att ansluta till en server på klientdelsundernätet (men inte tvärtom).
* Felsäker regel (för trafik som inte uppfyller något av föregående):
  1. Neka alla regel för nätverkstrafik: Den här neka-regeln bör alltid vara den sista regeln (när det gäller prioritet) och därför om ett trafikflöde inte matchar någon av de föregående reglerna den tas bort av den här regeln. Den här regeln finns en standardregel och vanligtvis på plats och aktiv. Inga ändringar krävs vanligtvis för att den här regeln.

> [!TIP]
> På den andra program trafik regeln för att förenkla det här exemplet tillåts alla portar. I ett scenario med verkliga användas mest specifika porten och adressintervall till att minska risken för angrepp på den här regeln.
>
>

När de tidigare reglerna har skapats, är det viktigt att granska prioriteten för varje regel för att se till att trafik tillåts eller nekas efter behov. I det här exemplet är reglerna i prioritetsordning.

#### <a name="conclusion"></a>Sammanfattning
Det här exemplet är en mer komplex men slutföra sätt att skydda och isolera nätverk än i föregående exempel. (Exempel 2 skyddar bara program och exempel 1 isolerar bara undernät). Den här designen kan du övervaka trafiken i båda riktningarna och skyddar inte bara inkommande programservern men använder nätverket säkerhetspolicy för alla servrar i det här nätverket. Dessutom beroende på den enhet som används för kan fullständig trafik gransknings- och medvetenhet uppnås. Mer information finns i den [detaljerade instruktioner för build][Example3]. De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeternätverk med klassiska PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerade beskrivningar av varje UDR NSG kommandot och brandväggsregel.
* Detaljerad traffic flow scenarier, som visar hur trafik tillåts eller nekas i varje lager.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exempel 4 Lägg till en hybridanslutning med en plats-till-plats, virtuell installation VPN
[Tillbaka till snabb start](#fast-start) | Detaljerade build-instruktioner som är tillgänglig snart

[![11]][11]

#### <a name="environment-description"></a>Miljö-beskrivning
Hybrid-nätverk med hjälp av en virtuell nätverksinstallation (NVA) kan läggas till någon av följande perimeternätverket nätverk som beskrivs i exempel 1, 2 eller 3.

I föregående bild visas används en VPN-anslutning via Internet (plats-till-plats) för att ansluta ett lokalt nätverk till ett Azure-nätverk via en NVA.

> [!NOTE]
> Om du använder ExpressRoute med alternativet Azure offentlig Peering är aktiverat kan ska en statisk väg skapas. Den här statiska vägen ska vidarebefordra till NVA VPN IP-adressen ut företagets Internet och inte via ExpressRoute-anslutning. NAT som krävs på Azure offentlig Peering i ExpressRoute-alternativet kan bryta VPN-sessionen.
>
>

När VPN-anslutningen är på plats, blir NVA den centrala hubben för alla nätverk och undernät. Bestämma vilka trafikflöden brandväggsregler för vidarebefordran översätts via NAT omdirigeras eller tas bort (även för trafiken flödar mellan det lokala nätverket och Azure).

Trafikflöden bör övervägas noga, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av den miljö som skapats i exempel 3 och sedan lägga till en plats-till-plats VPN-nätverk hybridanslutning resulterar i följande design:

[![12]][12]

Den lokala routern eller annan nätverksenhet som är kompatibel med din NVA för VPN, är den VPN-klienten. Den här fysiska enheten ska ansvara för att initiera och underhållet av VPN-anslutning till din NVA.

Logiskt till NVA ut nätverket som i fyra separata ”säkerhetszoner” med regler på NVA som den primära chef för trafik mellan dessa zoner:

![13]

#### <a name="conclusion"></a>Sammanfattning
Att lägga till en plats-till-plats VPN-hybrid nätverksanslutning till Azure-nätverk kan utöka lokala nätverk till Azure på ett säkert sätt. Använder en VPN-anslutning får trafiken krypteras och dirigerar via Internet. NVA i det här exemplet innehåller en central plats för att tillämpa och hantera säkerhetsprincipen. Mer information finns i instruktionerna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeter-nätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad traffic flow scenarier, som visar hur trafiken flödar genom den här designen.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exempel 5 Lägg till en hybridanslutning med en plats-till-plats, Azure VPN gateway
[Tillbaka till snabb start](#fast-start) | Detaljerade build-instruktioner som är tillgänglig snart

[![14]][14]

#### <a name="environment-description"></a>Miljö-beskrivning
Hybrid-nätverk med en Azure VPN-gateway kan läggas till antingen perimeter nätverkstyp som beskrivs i exempel 1 eller 2.

I bilden ovan visas används en VPN-anslutning via Internet (plats-till-plats) för att ansluta ett lokalt nätverk till ett Azure-nätverk via en Azure VPN-gateway.

> [!NOTE]
> Om du använder ExpressRoute med alternativet Azure offentlig Peering är aktiverat kan ska en statisk väg skapas. Den här statiska vägen ska vidarebefordra till NVA VPN IP-adressen ut företagets Internet och inte via ExpressRoute-WAN. NAT som krävs på Azure offentlig Peering i ExpressRoute-alternativet kan bryta VPN-sessionen.
>
>

Följande bild visar två nätverk kanter i det här exemplet. På den första edge NVA och NSG: er kan du styra trafikflödet för trafik inom Azure-nätverk och mellan Azure och Internet. Andra edge är Azure VPN-gateway, vilket är en separat och isolerade nätverk kant mellan lokala och Azure.

Trafikflöden bör övervägas noga, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av den miljö som skapats i exempel 1, och sedan lägga till en plats-till-plats VPN-nätverk hybridanslutning resulterar i följande design:

[![15]][15]

#### <a name="conclusion"></a>Sammanfattning
Att lägga till en plats-till-plats VPN-hybrid nätverksanslutning till Azure-nätverk kan utöka lokala nätverk till Azure på ett säkert sätt. Med inbyggda Azure VPN-gatewayen kan trafiken är IPSec-krypterad och dirigerar via Internet. Med hjälp av Azure VPN-gatewayen kan dessutom ge ett kostnadseffektivt alternativ (inga ytterligare licenser kostnad precis som med tredjeparts-nva: er). Det här alternativet är mest ekonomiska i exempel 1, där inga NVA används. Mer information finns i instruktionerna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeter-nätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad traffic flow scenarier, som visar hur trafiken flödar genom den här designen.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exempel 6 Lägg till en hybridanslutning med ExpressRoute
[Tillbaka till snabb start](#fast-start) | Detaljerade build-instruktioner som är tillgänglig snart

[![16]][16]

#### <a name="environment-description"></a>Miljö-beskrivning
Hybrid-nätverk med hjälp av en ExpressRoute privat peering-anslutningen kan läggas till antingen perimeter nätverkstyp som beskrivs i exempel 1 eller 2.

I bilden ovan visas ExpressRoute privat peering innehåller en direkt anslutning mellan ditt lokala nätverk och virtuella Azure-nätverket. Trafik transits endast service provider-nätverk och Microsoft Azure-nätverket, aldrig röra Internet.

> [!TIP]
> Med ExpressRoute ser till att företagets trafik borta från Internet. Du kan också servicenivåavtal från din ExpressRoute-provider. Azure Gateway kan överföra upp till 10 Gbit/s med ExpressRoute, med plats-till-plats-VPN, Azure Gateway maximalt dataflöde är 200 Mbit/s.
>
>

Som visas i följande diagram med det här alternativet har miljön nu två nätverk kanter. NVA och NSG styra trafikflöden för trafik inom Azure-nätverk och mellan Azure och Internet, även om gatewayen är en separat och isolerade nätverk kant mellan lokala och Azure.

Trafikflöden bör övervägas noga, eftersom de kan optimeras eller försämrad av det här designmönstret beroende på specifikt användningsfall.

Med hjälp av den miljö som skapats i exempel 1, och sedan lägga till en ExpressRoute hybrid nätverksanslutning, skapar du följande design:

[![17]][17]

#### <a name="conclusion"></a>Sammanfattning
Att lägga till en ExpressRoute privat Peering nätverksanslutning utöka lokala nätverk till Azure i en säker, lägre latens, högre presterande sätt. Med hjälp av interna Azure-gatewayen, som i följande exempel tillhandahåller också ett kostnadseffektivt alternativ (inte ytterligare Licensieringskrav precis som med tredjeparts-nva: er). Mer information finns i instruktionerna detaljerad build (kommande). De här anvisningarna inkluderar:

* Hur du skapar det här exemplet perimeter-nätverk med PowerShell-skript.
* Hur du skapar det här exemplet med en Azure Resource Manager-mall.
* Detaljerad traffic flow scenarier, som visar hur trafiken flödar genom den här designen.

## <a name="references"></a>Referenser
### <a name="helpful-websites-and-documentation"></a>Användbara webbplatser och dokumentation
* Åtkomst till Azure med Azure Resource Manager:
* Åtkomst till Azure med PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Dokumentation för virtuella nätverk: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Network security group-dokumentationen: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Användardefinierad routning dokumentation: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Virtuella Azure-gatewayer: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Plats-till-plats-VPN: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* ExpressRoute-dokumentation (inte glöm att checka ut avsnitten ”komma igång” och ”How To”): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "flödesschema för säkerhetsalternativ"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Azure Security Features"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A DMZ i ett företagsnätverk"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "azure Security-arkitektur"
[5]: ./media/best-practices-network-security/dmzazure.png "en DMZ i Azure-nätverk"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Hybridnätverk med tre säkerhetsgränser"
[7]: ./media/best-practices-network-security/example1design.png "inkommande DMZ med NSG"
[8]: ./media/best-practices-network-security/example2design.png "inkommande DMZ med NVA och NSG"
[9]: ./media/best-practices-network-security/example3design.png "dubbelriktat perimeternätverk med NVA, NSG och UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "logisk vy för brandväggsreglerna"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ med NVA anslutna Hybridnätverk"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ med NVA som är anslutna via ett plats-till-plats-VPN"
[13]: ./media/best-practices-network-security/example4networklogical.png "logiskt nätverk från perspektivet NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ med Azure-Gateway ansluten plats-till-plats-Hybridnätverk"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ med Azure med hjälp av plats-till-plats-VPN-Gateway"
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
