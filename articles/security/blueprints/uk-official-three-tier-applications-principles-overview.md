---
title: "Azure utkast Automation - National Cyber Security Centre molnet säkerhetsprinciper: översikt"
description: "Azure utkast Automation - National Cyber Security Centre molnet säkerhetsprinciper: översikt"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: bbf58215a4d236c70bf988cbfa1c8491055b5183
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Nationella säkerhetsprinciper för Cyber Security Centre moln-översikt


> [!NOTE]
> Dessa säkerhetsprinciper har definierats av National Cyber Security Centre (NCSC). Hittar du information om testprocedurer NCSC dokumentationen och riktlinjer för varje säkerhetsprincipen.



## <a name="ncsc-cloud-security-principle-1"></a>Säkerhetsprincipen för NCSC moln 1
### <a name="data-in-transit-protection"></a>Data i överföringen skydd
Användardata transit nätverk bör vara tillräckligt skyddade mot manipulering och avlyssning.

Detta bör uppnås genom en kombination av:

- nätverksskydd - och neka din angriparen möjlighet att fånga upp data
- kryptering - och neka din angriparen möjlighet att läsa data


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Den här Azure-modell konfigurerar resurser för att kommunicera med hjälp av endast säkra protokoll. Komponenten Brandvägg i Programgatewayen har konfigurerats att acceptera kommunikationen från externa användare via HTTPS/TLS och kommunicera med serverdelspoolen endast över HTTPS/TLS. Fjärrskrivbordstjänster är konfigurerade för att använda säkra anslutningar. VPN för att skydda trafik mellan AppGateway och Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Azure använder branschstandardiserade Transport Layer Security (TLS) 1.2-protokollet med 2048-bitars RSA/SHA256 krypteringsnycklar som rekommenderas av CESG/NCSC för att kryptera kommunikationen mellan kunden och molnet, såväl internt mellan Azure och Datacenter. När administratörer använda Microsoft Azure-portalen för att hantera tjänsten för organisationen, till exempel skickas de data som överförs mellan portalen och administratörens enhet via en krypterad TLS-kanal. När en användare ansluter till Outlook.com med hjälp av en vanlig webbläsare, ger en säker kanal för att ta emot och skicka e-post i HTTPS-anslutning.<br /> <br /> Azure erbjuder sina kunder ett antal alternativ för att skydda egna data och trafik. Certifikatet hanteringsfunktionerna är inbyggda i Azure ger administratörer flexibilitet för att konfigurera certifikat och krypteringsnycklar för hanteringssystem, enskilda tjänster, secure shell (SSH) sessioner, virtuellt privat nätverk (VPN) anslutningar anslutningar till fjärrskrivbord (RDP) och andra funktioner. <br /><br /> Utvecklare kan använda kryptografiproviders (CSP: er) som är inbyggda i Microsoft .NET Framework för att komma åt Advanced Encryption Standard (AES) algoritmer, tillsammans med funktioner för Secure Hash Algorithm (SHA-2) att hantera uppgifter såsom verifierar digitala signaturer. Azure Key Vault hjälper kunder att skydda krypteringsnycklar och hemligheter genom att lagra dem i maskinvarusäkerhetsmoduler (HSM). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Säkerhetsprincipen för NCSC moln 2
### <a name="asset-protection-and-resilience"></a>Återhämtning och skydd av tillgångsinformation
Användardata och tillgångar lagras eller bearbetning, bör skyddas mot obehöriga, förlust eller övertagande.

Aspekter att tänka på är:

1. Fysisk plats och juridiska behörighet
2. Datacenter-säkerhet
3. Data på Rest-skydd
4. Data Sanitisation
5. Utrustning avyttring
6. Fysiska återhämtning och tillgänglighet


 ## <a name="ncsc-cloud-security-principle-21"></a>Säkerhetsprincipen för NCSC moln 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Fysisk plats och juridiska behörighet
Du måste identifiera de platser där den lagras, bearbetas och hanteras för att förstå de juridiska omständigheter som data kan användas utan ditt medgivande.
Du kommer även behovet av att förstå hur datahantering kontroller i tjänsten tillämpas i förhållande till Storbritannien lagstiftning. Olämpliga skydd av användardata kan resultera i juridisk och regelmässig sanktionera eller reputational skada.


**Ansvarsområden:**`Customer`

> [!NOTE]
> Azure-tjänster distribueras regionalt och kunder kan konfigurera vissa Azure-tjänster för att lagra kundinformation endast i en region. Microsoft Azure tillhandahåller en lista över globalt tillgänglig Datacenter för att ge tillgänglighet och tillförlitlighet på global nivå. Alla Azure-Datacenter har certifierats mot den ISO/IEC 27001: 2013. Storbritannien Geo består av 2 regioner: Storbritannien, Syd och Storbritannien, Väst.

|||
|---|---|
| **Kunden** | Den här Azure-modell frågar vilken region att distribuera Azure-resurser till administratör. De rekommenderade regionerna för distribution är Storbritannien, Syd eller Storbritannien, Väst. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Inte tillämpligt |


 ## <a name="ncsc-cloud-security-principle-22"></a>Säkerhetsprincipen för NCSC moln 2.2
### <a name="datacentre-security"></a>Datacenter-säkerhet
Platser som används för att tillhandahålla molntjänster måste fysiska skydd mot obehörig åtkomst, manipulering, stjäls eller omkonfiguration av system. Otillräckligt skydd kan resultera i avslöjande, ändring eller förlust av data.


**Ansvarsområden:**`Microsoft Azure`


|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure Datacenter; skydd av datacentret säkerhetsåtgärder implementeras och hanteras av Microsoft Azure. Denna princip har ärvts från Microsoft Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementerar denna princip dina kunders räkning. Microsoft Azure körs i geografiskt distribuerade Microsoft lokaler dela utrymme och verktyg med andra Microsoft online services. Varje anläggning är avsedd att köras 24 x 7 x 365 och använder olika åtgärder som är branschstandard för att skydda åtgärder från strömavbrott, fysiska intrång och avbrott i nätverket. Dessa Datacenter följa branschstandarder (till exempel ISO 27001) för fysisk säkerhet och tillgänglighet. De är hanteras, övervakas och hanteras av Microsoft operations-personal. <br /> <br /> Azure-kunder kan vara säker på att fysiska säkerhetsåtgärder som finns på plats på alla Azure Datacenter på grund av Azure med certifikat på alla Datacenter för standarden ISO/IEC 27001: 2013. Storbritannien Geo består av 2 regioner: Storbritannien, Syd och Storbritannien, Väst. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Säkerhetsprincipen för NCSC moln 2.3
### <a name="data-at-rest-protection"></a>Data på Rest-skydd
För att säkerställa att data inte är tillgängligt för obehöriga parter med fysisk åtkomst till infrastruktur, bör användardata som lagras i tjänsten skyddas oavsett lagringsmedia som äger rum. Utan lämpliga åtgärder på plats kan data av misstag lämnas på förkastade, borttappade eller stulna media.


**Ansvarsområden:**`Shared`

> [!NOTE]
> Alla krypteringslösningar för som Microsoft Azure erbjuder sina kunder enkelt integreras med Azure Key Vault som möjliggör enkel hantering av krypteringsnycklar.

|||
|---|---|
| **Kunden** | Sekretess och integritet för alla blob-lagring som distribueras med den här lösningen Azure utkast skyddas med hjälp av Azure Storage Service kryptering (SSE). SSE skyddar data i vila i Azure storage-konton med hjälp av 256-bitars AES-kryptering. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Azure erbjuder en mängd olika krypteringsfunktioner för, vilket ger kunderna möjlighet att välja den lösning som bäst uppfyller deras behov. Azure Key Vault hjälper kunder enkelt och kostnaden effektivt behålla kontrollen över nycklar som används av molnprogram och tjänster för att kryptera data. Azure Disk Encryption kan kunder kryptera virtuella datorer. Azure Storage Service-kryptering gör det möjligt att kryptera alla data som placeras i en kund storage-konto. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Säkerhetsprincipen för NCSC moln 2.4
### <a name="data-sanitisation"></a>Data Sanitisation
Processen för etablering, migrera och frigör etablering resurser bör inte resultera i obehörig åtkomst till användardata.

Otillräckliga rensning av data kan resultera i att:

- Användardata finns kvar tjänsteleverantören på obestämd tid
- Användardata som tillgänglig för andra användare av tjänsten som återanvänds resurser
- Användardata går förlorade eller lämnas på förkastade, borttappade eller stulna media.


**Ansvarsområden:**`Microsoft Azure`


|||
|---|---|
| **Kunden** | Microsoft Azure tillhandahåller avtal garantier avseende permanent borttagning i Azure. Denna princip är därför ärvd från Microsoft Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure följer avfallshantering NIST SP800-88r1 med dataklassificering justeras till FIPS-199 Måttlig. NIST ger säker radering metod (via hårddisken inbyggd programvara) för enheter som stöder den. För hårddiskar som inte kan rensas Microsoft förstör du dem och återger återställning av information som är omöjlig (t.ex. splittras, hjälp, pulverisera dem eller förbränna). Lämpligt sätt avyttra bestäms av tillgångstyp. Posterna för destruktion bevaras. Alla Microsoft Azure-tjänster använda godkända media lagring och avyttring hanteringstjänster. <br />  <br /> Förfallotid eller avslutning av en prenumeration på tjänsten kunden kan kontakta Microsoft och be dem om du vill: <br /><br /> (1) inaktivera på kundens konto och ta sedan bort kundinformation; eller <br /> (2) behålla de data som lagras i online tjänsten i en begränsad funktion för minst 90 dagar efter förfallotid eller avslutning av kundens prenumeration (”kvarhållningsperioden”) för kontot så att kunden kan extrahera data. Microsoft kommer följande kvarhållningsperioden upphör att gälla kan inaktivera på kundens konto och ta bort alla data. Cachelagras eller säkerhetskopior som ska rensas inom 30 dagar från slutet av kvarhållningsperioden. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Säkerhetsprincipen för NCSC moln 2,5
### <a name="equipment-disposal"></a>Utrustning avyttring
När utrustning som används för att leverera en tjänst når slutet av dess livslängd, bör det avyttras på ett sätt som inte kompromissar säkerheten för tjänsten eller data som lagras i tjänsten.


**Ansvarsområden:**`Microsoft Azure`


|||
|---|---|
| **Kunden** | Kunder har inte tillgång till alla systemresurser i Azure Datacenter; utrustning avyttring procedurer implementeras och hanteras av Microsoft Azure. Denna princip har ärvts från Microsoft Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementerar denna princip dina kunders räkning. När ett system slutet av-livscykeln görs Microsoft operativa personal följer rigorösa datahantering procedurer och maskinvara avyttring processer för att se till att ingen maskinvara som kan innehålla kundinformation tillgängligt för obetrodda parter. Microsoft Azure följer avfallshantering NIST SP800-88r1 med dataklassificering justeras till FIPS-199 Måttlig. NIST ger säker radering metod (via hårddisken inbyggd programvara) för enheter som stöder den. För hårddiskar som inte kan rensas Microsoft förstör du dem och återger återställning av information som är omöjlig (t.ex. splittras, hjälp, pulverisera dem eller förbränna). Lämpligt sätt avyttra bestäms av tillgångstyp. Posterna för destruktion bevaras. Alla Microsoft Azure-tjänster använda godkända media lagring och avyttring hanteringstjänster. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Säkerhetsprincipen för NCSC moln 2.6
### <a name="physical-resilience-and-availability"></a>Fysiska återhämtning och tillgänglighet
Tjänsterna har olika nivåer av återhämtning som kommer att påverka möjligheten att fungera normalt vid fel, incidenter eller attacker. En tjänst utan garantier för tillgänglighet kan bli otillgängliga, potentiellt under långa perioder oavsett påverkan på verksamheten.


**Ansvarsområden:**`Shared`

> [!NOTE]
> Om kunden konfigurerar Microsoft Azure korrekt genom att aktivera Azure Site Recovery och alternativa lagring av data på en annan geo grafiskt finns datacentret, kan Microsoft Azure stödja fortsatt drift av kunden distribuerade resurser.

|||
|---|---|
| **Kunden** | Kunden ansvarar för att upprätta en plats för alternativa lagringsgruppen. Instruktionen kunden kontrollen implementering bör hantera kundens möjlighet att fungera om en incident. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure har Storbritannien National Cyber Security Centre (NCSC) godkända Datacenter på olika geografiska platser (Storbritannien, Syd och Storbritannien, Väst) för att kunna tillhandahålla återhämtning och tillgänglighet. Kommer att vara kundens ansvar att reserverad kapacitet i en annan region med hjälp av Azure Site Recovery-tjänsten. När de har konfigurerat Azure Site Recovery ska Azure starta och stoppa kundens tjänster i en sömlös övergång till den alternativa platsen. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Säkerhetsprincipen för NCSC moln 3
### <a name="separation-between-users"></a>Åtskillnad mellan användare
En skadlig eller avslöjade användare av tjänsten ska inte påverkar tjänsten eller data på en annan.

Faktorer som påverkar användare uppdelning inkluderar:

- där uppdelning kontroller har implementerats – detta påverkas kraftigt av tjänstmodellen (t.ex. IaaS, PaaS, SaaS)
- som du delar tjänsten med – detta beror distributionsmodell (t.ex. public, private eller community-moln)
- säkerhetsnivån som är tillgängliga i implementeringen av uppdelning kontroller

> [!NOTE]
> I en IaaS-tjänst bör du uppdelning som tillhandahålls av bearbetning, lagring och nätverkskomponenter. Dessutom kan SaaS och PaaS-tjänster som bygger på IaaS ärver vissa uppdelning egenskaperna för den underliggande IaaS-infrastrukturen.

**Ansvarsområden:**`Microsoft Azure`


|||
|---|---|
| **Kunden** | Microsoft Azure garanterar isolering för varje användare till att förhindra att skadlig eller avslöjade användare påverkar tjänsten eller data på en annan. Denna princip är därför ärvd från Microsoft Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | På grund av kunden molnservrar som virtuella, gäller fysisk avgränsning paradigmet inte längre. Microsoft Azure har utformats för att identifiera och prestandaräknaren riskerna i en flerklientsmiljö. Bearbetning och lagring av data är logiskt åtskild mellan användare i Azure med hjälp av Active Directory och utvecklats specifikt för flera tjänster som syftar till att säkerställa att data som lagras i delade Azure Datacenter är inte tillgänglig för en annan organisation. <br /> <br /> Grundläggande till någon delad molnarkitektur är isolering som angetts för varje användare att förhindra att skadlig eller avslöjade användare påverkar tjänsten eller data på en annan. <br /> <br /> Mer information om Microsoft klient uppdelning finns den fullständiga beskrivningen i den [Azure modell - säkerhetsprinciper NCSC moln - kund ansvarsområden matrisen](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Säkerhetsprincipen för NCSC moln 4
### <a name="governance-framework"></a>Styrning Framework
Tjänstprovidern ska ha ett ramverk för styrning av säkerhet som samordnar och dirigerar hanteringen av tjänsten och informationen i den. Alla tekniska kontroller som distribueras utanför det här ramverket kommer försämras avsevärt.
Med ett ramverk för effektiv styrning säkerställer att proceduren personal, fysiska och tekniska kontroller fortsätter att arbeta med livslängden för en tjänst. Det bör också svara på förändringar i tjänsten, tekniska utvecklingen och utseende på nya hot.


**Ansvarsområden:**`Microsoft Azure`


|||
|---|---|
| **Kunden** | Microsoft Azure upprätthåller ett ramverk för styrning av dokumenterade security för Azure-tjänster. Denna princip är därför ärvd från Microsoft Azure. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft efterlevnad framework innehåller en standardmetod för att definiera efterlevnad domäner, bestämma vilka mål som gäller för en viss grupp eller tillgången och samla in hur domän kontrollen mål behandlas tillräckligt detaljerat som de gäller för en given uppsättning standarder, föreskrifter eller affärsbehov. Ramen kontroller mappar till flera standarder, vilket hjälper Microsoft att utforma och bygg tjänster med hjälp av en gemensam uppsättning kontroller, vilket effektiviserar efterlevnad över en mängd förordningar idag och allteftersom de utvecklas i framtiden. <br /> <br /> Microsoft efterlevnad processer gör det också lättare för kunder att uppnå kompatibilitet över flera tjänster och uppfyller deras behov av ändra effektivt. Tillsammans säkerhetsinställningarna som förbättrar teknik och efterlevs processer för att Microsoft ska underhålla och expandera en omfattande uppsättning certifikat från tredje part. Dessa certifikat hjälper kunder att visa kompatibiliteten beredskap för sina kunder, granskare och regulatorer. <br /> <br />  Azure överensstämmer med en bred uppsättning internationella samt regionala och branschspecifika standarder, till exempel ISO 27001, FedRAMP, SOC 1 och SOC 2. Kompatibilitet med strikt säkerhetsåtgärder som ingår i dessa normer verifieras av rigorösa från tredje part revisioner som visar Azure-tjänster fungerar med och uppfylla världsklass branschstandarder, certifikat, intyg och tillstånd. <br /> <br /> Azure är utformat med en strategi för kompatibilitet som hjälper kunder adress affärsmål samt branschstandarder och förordningar. Kompatibilitet säkerhetsramverk innehåller test och granska faser, säkerhet analytics, risk management metodtips och benchmark säkerhetsanalys kan få certifikat och intyg. <br /> <br /> Mer information om Microsofts följer efterlevnad ramverk finns den fullständiga beskrivningen i den [Azure modell - säkerhetsprinciper NCSC moln - kund ansvarsområden matrisen](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Säkerhetsprincipen för NCSC moln 5
### <a name="operational-security"></a>Operativ säkerhet
Tjänsten måste drivs och hanteras på ett säkert sätt för att hindra, identifiera eller förhindra attacker. Bra operativ säkerhet bör inte kräver komplexa byråkratiskt, tidskrävande eller dyr processer.

Det finns fyra element att överväga:

- Konfigurations- och ändringshantering - bör du kontrollera att ändringar i systemet har korrekt testats och godkänts. Ändringarna bör inte ändra egenskaper för säkerhetsbehörighet oväntat
- Säkerhetsproblem Management – ska du identifiera och åtgärda säkerhetsproblem i dessa komponenter
- Skyddande övervakning - bör du vidta åtgärder för att identifiera attacker och obehörig aktivitet på tjänsten
- Hantering av incidenter - Kontrollera att du kan svara på händelser och återställa en säker, tillgänglig tjänst




 ## <a name="ncsc-cloud-security-principle-51"></a>Säkerhetsprincipen för NCSC moln 5.1
### <a name="configuration-and-change-management"></a>Konfigurations- och ändringshantering
Du bör ha en korrekt bild av tillgångar som utgör tjänsten, tillsammans med sina konfigurationer och beroenden.
Ändringar som kan påverka säkerheten för tjänsten ska identifieras och hanteras. Obehöriga ändringar identifieras.
Om ändringen inte effektivt hanteras införas säkerhetsproblem ovetande till en tjänst. Och även om det inte avslöjar säkerhetsrisken, den kan inte helt undvikas.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Representerar en ”som kod” konfigurationsbaslinje för distribuerad arkitektur Azure Resource Manager-mallar och tillhörande resurser som utgör den här Azure-modell. Lösningen tillhandahålls via GitHub, som kan användas för av konfigurationskontrollen. <br /> <br /> Azure Active Directory kontot behörighet implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till roller som tillhandahåller strikt kontroll över vilka användare kan visa och kontroll distribuerade resurser. Behörighet för Active Directory implementeras med hjälp av rollbaserad åtkomstkontroll genom att tilldela användare till säkerhetsgrupper. Dessa säkerhetsgrupper styr åtgärder som användarna kan utföra med avseende på operativsystemets konfiguration. Dessa rollbaserade scheman kan utökas av kunden uppdrag behov. <br /> <br /> För att vara kompatibel med den här principen, krävs ytterligare konfiguration av customer för användning i produktion. Dessa konfigurationer måste därför vara en del av processen för kundens ändringshantering. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure granskar och uppdaterar konfigurationsinställningar och grundläggande konfigurationer av maskinvara, programvara och nätverk enheter per år. Ändringar utvecklas, testas och godkänts före anger produktionsmiljön från en miljö för utveckling och test. <br /> <br />Microsoft Azure gäller baslinjen konfigurationer med process för ändrings- och versionen för Microsoft Azure-programkomponenter (t.ex. OS, Fabric, RDFE, XStore, etc.) och bootstrap konfigurationsprocessen för maskin- och komponenter att skriva Microsoft Azure-produktionsmiljö som beskrivs nedan. <br /> <br /> Baslinje-konfigurationer som krävs för Azure-baserade tjänster granskas genom Azure-säkerhet och efterlevnad teamet och tjänsten team som en del av testning innan distributionen av produktionstjänsten. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Säkerhetsprincipen för NCSC moln 5.2
### <a name="vulnerability-management"></a>Hantering av säkerhetsrisk
Leverantörer ska ha en hanteringsprocesser för att identifiera, hantera och minska säkerhetsrisker. Tjänster som inte gör att snabbt bli sårbar för attacker med hjälp av verktyg och metoder som är allmänt kända.


**Ansvarsområden:**`Customer`


|||
|---|---|
| **Kunden** | Kunden är ansvarig för säkerhetsproblem genomsökning av kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). Instruktionen kunden implementering bör ta itu med de verktyg som används för att utföra sårbarhetsgenomsökningar. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Hantering av säkerhetsuppdateringar skyddar system från kända säkerhetsrisker. Azure använder integrerad distribution system för att hantera distribution och installation av uppdateringar för programvara från Microsoft. Azure kan också dra på resurser av den Microsoft Security Response Center (MSRC), som identifierar, övervakar, svarar och löser säkerhetsincidenter och molnet säkerhetsriskerna runt klockan, varje dag på året. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Säkerhetsprincipen för NCSC moln 5.3
### <a name="protective-monitoring"></a>Skyddande övervakning
En tjänst som inte övervakar effektivt för attack, missbruk och funktionsfel är inte troligt att identifiera attacker (både slutförda och misslyckade). Därför kan det inte att snabbt svara på potentiella kompromisser miljöer och data.


**Ansvarsområden:**`Customer`


|||
|---|---|
| **Kunden** | Kunden ansvarar för att övervaka kunden distribuerade resurser (om du vill inkludera program, operativsystem, databaser och programvara). Instruktionen kunden kontrollen implementering bör adressera mekanismer för att övervaka, identifiera och svara på attacker, missbruk och fel. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure-säkerhet har definierats krav för aktiv övervakning. Tjänsten team konfigurera active övervakningsverktyg i enlighet med dessa krav. Aktiva övervakningsverktyg inkluderar Monitoring Agent (MA) och System Center Operations Manager (SCOM), som är konfigurerad för att tillhandahålla realtid aviseringar till Microsoft Azure-säkerhet personal i situationer som kräver omedelbar åtgärd. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Säkerhetsprincipen för NCSC moln 5.4
### <a name="incident-management"></a>Hantering av incidenter
Om inte före välplanerad incidenthantering rutiner finns, sannolikt dåliga beslut kommer att göras när incidenter sker potentiellt exacerbating den övergripande effekten på användare.
Dessa processer behöver inte vara komplex eller kräver att stora mängder beskrivning, men bra incidenthantering kommer minimera påverkan för användare av säkerhet, tillförlitlighet och miljöproblem med en tjänst.


**Ansvarsområden:**`Shared`

> [!NOTE]
> I fall där kunden säkerhetsincidenter kan påverka säkerhetsstatusen för Microsoft Azure ansvarar kunden för att meddela Microsoft Azure.

|||
|---|---|
| **Kunden** | Kunden ansvarar för att upprätta en process för hantering av incidenter för kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). Instruktionen kunden implementering bör hantera incidenter och aviseringar, stöder rimlig incident svar och vidarebefordran av information till PGA och andra HMG organisationer efter behov. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft har implementerat en säkerhetsprocess incidenthantering för att underlätta ett samordnat svar vid händelser ska ske en. <br /> <br /> Om Microsoft inser otillåten åtkomst till all kundinformation som lagras på utrustningen eller i dess verksamhet eller obehörig åtkomst till sådana utrustning eller verksamhet, vilket resulterar i förlust, avslöjande eller ändring av kundinformation, har Microsoft angetts som kommer det att: <br /> <br />   -Omedelbart meddela kunden om säkerhetsincident; <br /> -Snabbt kan undersöka säkerhetsincident och förse kunden med detaljerad information om säkerhetsincident; och <br /> – Ta rimligt och fråga steg för att minimera effekterna och minimera skador som härrör från säkerhetsincident.  <br />  <br /> Ett ramverk för incidenthantering har upprättats med definierade-roller och ansvarsområden som allokerats. Windows Azure Security Incident Management (WASIM)-teamet ansvarar för att hantera säkerhetsincidenter, inklusive eskalering och garantera specialist team vid behov. Azure-verksamhetschefer som ansvarar för att övervaka undersökningar och lösning av säkerhet och sekretess incidenter med stöd från andra funktioner. <br /> <br /> Mer information om Microsofts Incident svar processer finns den fullständiga beskrivningen i den [Azure modell - säkerhetsprinciper NCSC moln - kund ansvarsområden matrisen](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Säkerhetsprincipen för NCSC moln 6
### <a name="personnel-security"></a>PERSONALSÄKERHET
Där service provider personal har tillgång till dina data och datorer måste en hög grad av säkerhet i sin trovärdighet. Stöd för fullständig kontroll av lämplig utbildning minskar sannolikheten för oavsiktliga eller skadliga röjande av service provider personal.
Tjänstprovidern bör omfattas personal säkerhet kontrollen och vanliga säkerhetsutbildning. Personal i rollerna bör förstå deras ansvarsområden. Leverantörer bör klargöra hur de skärmen och hantera personalen i Privilegierade roller.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Kunden är ansvarig för filgallring enskilda användare och ge regelbundna säkerhetsutbildning för enskilda användare åtkomst till kunden distribuerade resurser. Instruktionen kunden implementering bör hantera kontrollen kriterierna för roller och frekvensen för säkerhetsutbildning. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure-personal som fungerar Azure-tjänster och ge kundsupporten (eller Microsoft underleverantörer som hjälper med plattformen operations, felsökning och teknisk support) genomgå en Microsoft standard bakgrund (eller motsvarande) kontrollera att utvärdera medarbetare utbildning, anställning och straffrättsliga historik. Bakgrundskontroller är brett i enlighet med kraven för Storbritannien regering BPSS/BS7858. De omfattar inte specifikt formella identitetskontroll.  <br /> <br /> Microsoft inkluderar enligt bestämmelserna i dess anställda och underleverantörer kontrakt. Alla lämpliga Microsofts anställda och underleverantörer har delta i ett Microsoft Azure sponsrad säkerhetsutbildning program som informerar personal av deras ansvarsområden för informationssäkerhet. <br /> <br /> Microsoft Azure-tjänster personal eller underleverantörer misstänks genomför säkerhetsöverträdelser och/eller brott mot säkerhetsprincip Information regleras av en undersökningsprocessen och lämpliga disciplinåtgärder fram till och inklusive avslutning. Om omständigheterna så kräver, kan Microsoft läser frågan för rättsliga uppföljningen av en juridisk myndighet. <br /> <br /> För att komplettera systemet av bakgrundskontroller säkerhet distribuerar Microsoft kombinationer av förebyggande defensiva och reaktiv kontroller för att skydda mot obehörig utvecklare och/eller administrativ aktivitet, däribland mekanismer: <br />  <br /> -Nära åtkomstkontroller på känsliga data, inklusive ett krav för smartkort-baserade tvåfaktorsautentisering utföra känsliga åtgärder. <br /> -Kombinationer av kontroller som förbättrar oberoende upptäcka skadlig programvara. <br /> – Flera nivåer för övervakning, loggning och rapportering. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Säkerhetsprincipen för NCSC moln 7
### <a name="secure-development"></a>Säkerhet i utvecklingsarbetet
Tjänster ska utformas och utvecklats för att identifiera och åtgärda hot mot användarens säkerhetsfrågor. De som inte kan vara utsatt för säkerhetsproblem som kan påverka dina data, orsaka förlust av tjänsten eller aktivera andra skadliga aktiviteter.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här Azure-modell kör Windows-operativsystem. Windows tillhandahåller realtid filen integritet verifiering, skydd och återställning av viktiga systemfiler som installeras som en del av Windows eller auktoriserade systemuppdateringar via Windows Resource Protection WRP ()-funktion, vilket gör att realtid integritetskontroll. <br /> <br /> Windows har skydd för förhindrar körning av kod i begränsade minnesplatser: Nej köra (NX), adress utrymme Layout slumpmässig (ASLR) och dataexekveringsskydd (DEP). <br /> <br /> Den här Azure-modell distribuerar värdbaserade program mot skadlig kod skydd för alla distribuerade virtuella Windows-datorer implementeras med hjälp av Microsoft Windows Defender. Windows Defender konfigureras för att automatiskt uppdatera båda antimalware-motorn och skydd signaturer som blir tillgängliga versionen. <br /> <br /> För att vara kompatibel med den här principen, krävs ytterligare konfiguration av customer för användning i produktion. Dessa konfigurationer måste därför vara en del av kundens säker utvecklingsprocessen. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Security Development Lifecycle (SDL) ger en effektiv hot modellering process för att identifiera hot och säkerhetsrisker i programvara och tjänster. Hot modellering är en team-övning omfattar operations manager, program/projektledare, utvecklare och testare, och representerar en aktivitet för analys av nyckelsäkerhet utföras för en lösningsdesign. Gruppmedlemmar verktyget SDL hot modellering modellera alla tjänster och projekt, både när de skapas och när de uppdateras med nya funktioner. Hot modeller omfattar all kod som visas på risken för angrepp och all kod som skrivits av eller licensierad från tredje part och överväga alla förtroendegränser. STRIDE-system (Spoofing, Tampering, Repudiation, avslöjande av Information, Denial of service och rättighetsökning) används för att identifiera och lösa säkerhetshot tidigt i designprocessen innan de kan påverka kunder. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Säkerhetsprincipen för NCSC moln 8
### <a name="supply-chain-security"></a>Ange kedjan säkerhet
Tjänstprovidern bör se till att dess leveranskedja tillfredsställande sätt stöder alla säkerhetsprinciper som tjänsten utger sig för att implementera.
Molntjänster är ofta beroende av ett tredjepartsprodukter och tjänster. Därför, om denna princip inte är implementerad, ange kedjan kan underminera säkerheten för tjänsten och påverka genomförandet av andra säkerhetsprinciper.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Kunden ansvarar för att tillhandahålla säker leverans kedjan dokumentationen för en tredje part har köpt programvara och operativsystem som används i sina Azure-prenumeration. Instruktionen kunden implementering bör hantera undantaget för att följa processer som identifieras av den här ange kedjan dokumentationen. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Gruppen Microsoft Cloud ange kedjan (MCSC) består av sex unika team varje bidrar till att skydda Azure från hot mot ange kedjan.  <br />  <br /> -Inköp <br /> -Kund åtgärder <br /> -Distributionen kvalitet <br /> -Hantering av leverantör <br /> -Besparar <br />  <br /> Mer information om Microsofts MCSC grupp finns i den fullständiga beskrivningen i den [Azure modell - säkerhetsprinciper NCSC moln - kund ansvarsområden matrisen](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Säkerhetsprincipen för NCSC moln 9
### <a name="secure-user-management"></a>Skydda användarhantering
Leverantören för att frigöra Verktyg att på ett säkert sätt hantera din användning av tjänsten. Hantering av gränssnitt och förfaranden är en viktig del av säkerhetsbarriär, förhindra obehörig åtkomst och ändring av dina resurser, program och data.

Aspekter att tänka på är:

- Autentisering av användare hanteringsgränssnitt och supportkanaler
- Uppdelning och åtkomstkontroll inom hanteringsgränssnitt



 ## <a name="ncsc-cloud-security-principle-91"></a>Säkerhetsprincipen för NCSC moln 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Autentisering av användare inom Supportkanaler och hanteringsgränssnitt
För att upprätthålla en säker tjänst behöver autentiseras på rätt sätt att få tillåtelse att utföra hanteringsaktiviteter, ändrar rapporten fel eller begäran till tjänsten.
Dessa aktiviteter kan utföras via en webbportal för service management eller via andra kanaler, till exempel telefon eller e-post. Det är troligt att inkludera sådana funktioner som etablera nya faktorer, hantera användarkonton och hantera användardata.
Leverantörer måste du se till att alla hanteringsbegäranden som kan påverka säkerheten utförs i säker och autentiserad kanaler. Om användare inte har autentiserats starkt sedan kanske en falska instansen att kunna utföra Privilegierade åtgärder, vilket skulle underminera säkerheten för tjänsten eller data.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | När administratörer har åtkomst till Microsoft Azure Portal för att hantera Azure-resurser för organisationen, skickas de data som överförs mellan portalen och administratörens enhet via en krypterad kanal för Transport Layer Security (TLS) med 2048-bitars RSA / SHA256 krypteringsnycklar som rekommenderas av CESG/NCSC.  <br /> <br /> Den här Azure-modell använder Windows-autentisering, fjärrskrivbord och BitLocker. Dessa komponenter kan konfigureras för att förlita sig på FIPS 140 validerade kryptografiska modulerna. <br /> <br /> Den här Azure-modell tillämpar logisk åtkomst tillstånd med hjälp av rollbaserad åtkomstkontroll som tillämpas av Azure Active Directory genom att tilldela användare till roller, Active Directory genom att tilldela användare till säkerhetsgrupper, och kontrollerar Windows OS-nivå. Azure Active Directory-roller har tilldelats till användare eller grupper kontrollera logiska åtkomsten till resurser i Azure på nivån resurs, grupp eller prenumeration. Active Directory-säkerhetsgrupper åtkomstkontroll logiska till resurser för OS-nivå och funktioner. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Kunder administrera sina Azure-resurser via Azure portal, som ger åtkomst till alla virtuella datorer, databaser, molntjänster och andra resurser som konfigurerats för kundens konto. Webbåtkomst till Azure portal är skyddad av branschstandard Transport Layer Security (TLS) 1.2 anslutningar med 2048-bitars RSA/SHA256 krypteringsnycklar som rekommenderas av CESG/NCSC. Rollbaserad åtkomstkontroll som möjligt för kunder att ge begränsad åtkomst till Azure management-resurser för specifika användare och grupper. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Säkerhetsprincipen för NCSC moln 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Uppdelning och åtkomstkontroll inom hanteringsgränssnitt
Många molntjänster hanteras via API: erna eller webbprogram. Dessa gränssnitt är en viktig del av tjänstens säkerhet. Om användarna inte är tillräckligt åtskilda inom hanteringsgränssnitt, kanske en användare kan påverka tjänsten eller ändra data på en annan.
Dina administrativa Privilegierade konton ha förmodligen tillgång till stora mängder data. Begränsa behörigheterna för enskilda användare att de absolut nödvändigt kan hjälpa för att begränsa skadan som orsakas av obehöriga användare, avslöjade autentiseringsuppgifter eller manipulerade enheter.
Rollbaserad åtkomstkontroll ger dig möjlighet att uppnå detta och kan förväntas vara en funktion som är särskilt viktigt för användare som hanterar större distributioner.
Exponera hanteringsgränssnitt till mindre tillgängliga nätverk (t.ex. community istället offentliga nätverk) gör det svårare för angripare att nå och angrepp dem, så måste de först att få åtkomst till ett av dessa nätverk. I princip 11 finns riktlinjer för att bedöma risken för exponering av gränssnitt för olika typer av nätverk.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar en Programgateway, belastningsutjämnare och konfigurerar regler för nätverkssäkerhetsgrupper för att styra commutations på externa gränser och mellan internt undernät. Funktioner för användarnamn är skild från system management-funktion via verkställandet av logiska åtkomstkontroller och systemarkitektur. Gränssnitt för system management-funktioner är separat från användargränssnitt. Alla anslutning är via en säker skyddsmiljö värd (jumpbox) finns i ett undernät för hantering med regler för nätverkssäkerhetsgrupper att begränsa åtkomsten till produktionsresurser efter behov. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Som beskrivs i är åtskillnad mellan användarens uppdelning inbyggd i Azure kärnan. Azure Active Directory (Azure AD eller AAD) kan användas för att ge varje användare som autentiserar till Azure portal med åtkomst till de resurser som de har rätt att se och hantera. Därför är olika kundkonton strikt åtskilda från varandra när hanteras via vanliga Azure-portalen. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Säkerhetsprincipen för NCSC moln 10
### <a name="identity-and-authentication"></a>Enhetsidentitet samt autentisering
All åtkomst till tjänsten gränssnitt bör begränsas till autentiserade och auktoriserade personer.
Svag autentisering till dessa gränssnitt kan aktivera obehörig åtkomst till systemet, vilket resulterar i stöld eller ändring av dina data, ändringar i din tjänst eller DOS-attacker.
Allt ska autentisering ske i säkra kanaler. E-post, HTTP eller telefon är sårbara för attacker avlyssning och social manipulation.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Den här Azure-modell använder Active Directory för hantering. Åtkomst till resurser som distribueras med den här Azure-modell är skyddat från repetitionsattacker av inbyggda Kerberos-funktionerna i Azure Active Directory, Active Directory och Windows-operativsystemet. I Kerberos-autentisering innehåller autentiseraren skickades av klienten ytterligare data, till exempel en krypterad IP-lista och klienten tidsstämplar biljett livslängd. Om ett paket spelas kontrolleras tidsstämpel. Om tidsstämpeln är tidigare än, eller samma som en tidigare authenticator paketet avvisas. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Azure tillhandahåller tjänster för att spåra identitet samt integrera den med identitetslagringar som kanske redan används. Azure AD är en omfattande identitets- och hanteringstjänsten för molnet som säker åtkomst till data i lokala program och molnprogram. Azure AD förenklar också hantering av användare och grupper genom att kombinera core katalogtjänster, avancerad identitet styrning, säkerhet och hantering av åtkomst. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Säkerhetsprincipen för NCSC moln 11
### <a name="external-interface-protection"></a>Externa gränssnitt skydd
Alla externa eller mindre betrodda gränssnitt för tjänsten ska identifieras och försvaras på lämpligt sätt.
Om några av de gränssnitt som används är privat (till exempel hanteringsgränssnitt) kan effekterna av komprometterande vara större.
Du kan använda olika modeller för att ansluta till molntjänster som exponerar dina affärssystem på olika nivåer av risk.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och undernät för hantering. Undernät logiskt avgränsade med regler för nätverkssäkerhetsgrupper används för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner individuella undernät (t.ex. externa trafiken kan inte komma åt databasen, hantering, eller Active Directory-undernät).  <br /> <br /> En Programgateway distribueras för att hantera externa anslutningar till ett kunden distribuerade program. Externa anslutningar för av hanteringsåtkomst begränsas till jumpbox (skyddsmiljö host) distribueras i ett undernät för hantering med Nätverkssäkerhetsregler som används för att begränsa externa anslutningar till auktoriserade IP-adresser. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft använder en metod som anropas ”Red Teamindelning” för att förbättra Azure säkerhetskontroller och processer genom regelbundna intrång tester. Röd Team är en grupp av ständig personal på Microsoft som fokuserar på utför mål och beständiga attacker mot Microsoft infrastruktur, plattformar och program, men inte end-kunders program eller data. <br /> <br /> Mer information om Microsofts Red Teaming samt en beskrivning av blå Teaming ansträngningar finns den fullständiga beskrivningen i den [Azure modell - säkerhetsprinciper NCSC moln - kund ansvarsområden matrisen](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Säkerhetsprincipen för NCSC moln 12
### <a name="secure-service-administration"></a>Säker Service Administration
System som används för administration av molntjänster kommer hög privilegierad åtkomst till tjänsten. Deras röjande skulle ha betydande påverkan, inklusive möjlighet att kringgå säkerhetskontroller och stjäla eller ändra stora mängder data.
Design, implementering och hantering av administration system bör följa enterprise god praxis, samtidigt som deras högt värde för angripare som inser.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Kunden ansvarar för att säkerställa en säker arbetsstation för administration av sina Azure-prenumeration och kunden distribuerade resurser (till exempel program, operativsystem, databaser och programvara). Instruktionen kunden implementering bör ta itu med de metoder som används för att minska risken för utnyttjandet av kunden distribuerade resurser. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure operations personal krävs för att använda säkra administrativa arbetsstationer (sågar, även kallade arbetsstationer med privilegierad åtkomst eller PAWs). Metoden SETT är en utökning av väletablerade rekommenderas att använda separata admin och användarkonton för administrativa personal. Den här övningen använder ett enskilt tilldelade administrativa konto som är separat från användarens konto. SETT bygger på att kontot uppdelning praxis genom att tillhandahålla en säker arbetsstation för de känsliga kontona. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Säkerhetsprincipen för NCSC moln 13
### <a name="audit-information-for-users"></a>Granskningsinformation om användare
Du bör få med revisionen poster som behövs för att övervaka åtkomsten till tjänsten och data som lagras i den. Typ av du granskningsinformation har en direkt inverkan på din möjlighet att upptäcka och svara på olämpliga eller skadliga aktiviteter inom en rimlig tidsplan.


**Ansvarsområden:**`Shared`


|||
|---|---|
| **Kunden** | Händelser som granskas av den här Azure-modell är de granskas av Azure aktivitetsloggar för distribuerade resurser, OS-nivå loggar och loggar för Active Directory. Dessa händelseloggarna innehåller information som är tillräckligt för att avgöra när händelser inträffar, källan för händelsen, resultatet av händelsen och annan information som har stöd för undersökning av säkerhetsincidenter. Kunder kan välja ytterligare händelser som ska granskas för att uppfylla uppdrag behov. Alla Azure-resurser har granskningsloggar som är tillgängliga i Azure Portal. |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Azure logganalys samlar in poster för de händelser som inträffar inom en organisation och nätverk som de sker, innan någon kan manipulera dem och gör att olika typer av analys genom att sammanföra data över flera datorer. Azure ger kunder möjlighet att utföra security händelse genereras och samlas in från Azure IaaS och PaaS-roller till en central lagringsplats i sina prenumerationer. Dessa insamlade händelser kan exporteras till lokala säkerhetsinformation och händelse-hanteringssystem (SIEM) för pågående övervakning. När data överförs till lagring, finns det många alternativ för att visa diagnostikdata. <br /> <br /> Azure inbyggd diagnostik kan hjälpa dig med felsökning. För program som distribueras i Azure är en uppsättning operativsystemet säkerhetshändelser aktiverade som standard. Kunder kan lägga till, ta bort eller ändra händelser som ska granskas genom att anpassa granskningsprincip för operativsystemet. <br /> <br /> Det är ganska enkelt på en hög nivå och enkel att börja samla in loggar med hjälp av Windows Event Forwarding (WEF) eller mer avancerade Azure-diagnostik när Windows-baserade virtuella datorer distribueras med IaaS i Azure. Azure-diagnostik kan också konfigureras för att samla in loggar och händelser från PaaS-rollinstanser. När du använder IaaS-baserade virtuella datorer, konfigurerar bara en kund och gör önskade säkerhetshändelser på samma sätt som de aktiverar Windows-servrar för att logga granskningar i sina lokala datacenter. För webbprogram är det också möjligt att aktivera IIS-loggningen om detta är det primära programmet och distribution i Azure. Kunder kan alltid lagra säkerhetsdata i storage-konton i stöds geoplatser att uppfylla kraven för suveränitet. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Säkerhetsprincipen för NCSC moln 14
### <a name="secure-use-of-the-service"></a>Säker användning av tjänsten
Säkerheten för molntjänster och de data som lagras i dem kan försämras om du använder tjänsten dåligt. Det innebär har du visst ansvar när du använder tjänsten för att dina data skyddas på lämpligt sätt.
Omfattningen av ditt ansvar varierar beroende på distributionsmodeller för Molntjänsten och det scenario som du tänker använda tjänsten. Specifika funktioner i enskilda tjänster kan också ha betydelse. Till exempel är hur ett nätverk för innehållsleverans skyddar din privata nyckel eller hur en molnet betalningsförmedlare identifierar olagliga transaktioner viktig säkerhetsinformation utöver allmänna överväganden som omfattas av säkerhetsprinciper för molnet.  
Du är ansvarig för viktiga aspekter på säkerheten för dina data och arbetsbelastningar med IaaS och PaaS-erbjudanden. Till exempel om du skaffa en IaaS compute-instans kan du normalt ansvarar för att installera moderna operativsystem, konfigurera operativsystemet på ett säkert sätt, på ett säkert sätt distribuera program och även underhålla instansen genom att använda korrigeringsfiler eller utföra underhåll som krävs.


**Ansvarsområden:**`Customer`

> [!NOTE]
> Kunden kan använda Azure Security Center för att förebygga, upptäcka och åtgärda hot med bättre synlighet och kontroll över säkerheten för Azure-resurser. Azure Security Center innehåller integrerad säkerhet övervaka och hantera principer för Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

|||
|---|---|
| **Kunden** | Azure Resource Manager-mallar och tillhörande resurser som utgör den här Azure-modell kan du följa en strategi för skydd på djupet för säkerhet. För att vara kompatibel med den här principen, krävs ytterligare konfiguration av customer för användning i produktion (t.ex. programvara för hantering av databasen, distribuera webbprogram). |
| **Providern&nbsp;(Microsoft&nbsp;Azure)** | Inte tillämpligt |