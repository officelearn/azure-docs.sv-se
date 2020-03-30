---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789723"
---
Sakernas Internet (IoT) innebär unika utmaningar för säkerhet, sekretess och efterlevnad för företag över hela världen. Till skillnad från traditionell cyberteknik där dessa frågor kretsar kring programvara och hur den implementeras, handlar IoT om vad som händer när cyber- och de fysiska världarna konvergerar. För att skydda IoT-lösningar krävs säker etablering av enheter, säker anslutning mellan dessa enheter och molnet och säkert dataskydd i molnet under bearbetning och lagring. Att arbeta mot sådana funktioner är dock resursbegränsade enheter, geografisk distribution av distributioner och ett stort antal enheter i en lösning.

I den här artikeln beskrivs hur IoT-lösningsacceleratorer tillhandahåller en säker och privat Internet of Things-molnlösning. Lösningsacceleratorerna levererar en komplett helhetslösning, med säkerhet inbyggd i varje steg från grunden. På Microsoft är utveckling av säker programvara en del av programvaran engineering praxis, rotad i Microsofts decennier lång erfarenhet av att utveckla säker programvara. För att säkerställa detta är Security Development Lifecycle (SDL) den grundläggande utvecklingsmetoden, tillsammans med en mängd säkerhetstjänster på infrastrukturnivå som Operational Security Assurance (OSA) och Microsoft Digital Crimes Unit, Microsoft Security Response Center och Microsoft Malware Protection Center.

Lösningsacceleratorerna erbjuder unika funktioner som gör etablering, anslutning till och lagring av data från IoT-enheter enkelt och transparent och, framför allt, säkert. I den här artikeln undersöks säkerhetsfunktionerna för Azure IoT-lösningsacceleratorer och distributionsstrategier för att säkerställa att säkerhet, sekretess och efterlevnadsutmaningar åtgärdas.

## <a name="introduction"></a>Introduktion

Sakernas Internet (IoT) är framtidens våg och erbjuder företag omedelbara och verkliga möjligheter att minska kostnaderna, öka intäkterna och förändra sin verksamhet. Många företag är dock tveksamma till att distribuera IoT i sina organisationer på grund av oro för säkerhet, sekretess och efterlevnad. En viktig orosmoment kommer från det unika i IoT-infrastrukturen, som sammanför cyber- och fysiska världar, vilket förvärrar enskilda risker som är förknippade med dessa två världar. Säkerheten för IoT gäller att säkerställa integriteten hos kod som körs på enheter, tillhandahålla enhets- och användarautentisering, definiera tydligt ägande av enheter (samt data som genereras av dessa enheter) och vara motståndskraftig mot cyber- och fysiska attacker.

Sedan har vi frågan om integritet. Företag vill ha insyn i datainsamlingen, som i det som samlas in och varför, vem som kan se det, vem som kontrollerar åtkomst och så vidare. Slutligen finns det allmänna säkerhetsfrågor av utrustningen tillsammans med de människor som driver dem, och frågor om att upprätthålla branschstandarder för efterlevnad.

Med tanke på säkerhets-, sekretess-, transparens- och efterlevnadsproblem är det fortfarande en utmaning att välja rätt IoT-lösningsleverantör. Att sy ihop enskilda delar av IoT-programvara och tjänster som tillhandahålls av en mängd olika leverantörer introducerar luckor i säkerhet, sekretess, transparens och efterlevnad, vilket kan vara svårt att upptäcka, än mindre åtgärda. Valet av rätt IoT-programvara och tjänsteleverantör bygger på att hitta leverantörer som har lång erfarenhet av att driva tjänster, som sträcker sig över vertikaler och geografiska områden, men som också kan skalas på ett säkert och transparent sätt. På samma sätt hjälper det för den utvalda leverantören att ha årtionden av erfarenhet av att utveckla säker programvara som körs på miljarder maskiner över hela världen, och har förmågan att uppskatta hotet landskapet som denna nya värld av Sakernas Internet.

## <a name="secure-infrastructure-from-the-ground-up"></a>Säker infrastruktur från grunden

[Microsoft Cloud-infrastrukturen](https://azure.microsoft.com) stöder mer än en miljard kunder i 127 länder/regioner. Med hjälp av Microsofts årtionden långa erfarenhet av att bygga företagsprogramvara och köra några av de största onlinetjänsterna i världen, erbjuder Microsoft Cloud högre nivåer av förbättrade metoder för säkerhet, sekretess, efterlevnad och hotreducering än de flesta kunder skulle kunna uppnå på egen hand.

[SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/) tillhandahåller en obligatorisk företagsomfattande utvecklingsprocess som bäddar in säkerhetskraven i hela programvarulivscykeln. För att säkerställa att operativa aktiviteter följer samma säkerhetspraxis använder SDL strikta säkerhetsriktlinjer som anges i Microsofts OSA-process (Operational Security Assurance). Microsoft samarbetar också med granskningsföretag från tredje part för pågående verifiering av att det uppfyller sina efterlevnadsskyldigheter, och Microsoft ägnar sig åt breda säkerhetsinsatser genom att skapa kompetenscentrum, inklusive Microsoft Digital Crimes Unit. Microsoft Security Response Center och Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – säker IoT-infrastruktur för ditt företag

Microsoft Azure erbjuder en komplett molnlösning som kombinerar en ständigt växande samling integrerade molntjänster – analys, maskininlärning, lagring, säkerhet, nätverk och webb – med ett branschledande engagemang för skydd och sekretessen för dina uppgifter. Microsofts [anta brott](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategi använder ett dedikerat *rött team* av experter programvara säkerhet som simulerar attacker, testa möjligheten för Azure att identifiera, skydda mot nya hot, och återhämta sig från överträdelser. Microsofts [globala incidenthanteringsteam](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) arbetar dygnet runt för att mildra effekterna av attacker och skadlig aktivitet. Teamet följer etablerade procedurer för incidenthantering, kommunikation och återställning och använder upptäckbara och förutsägbara gränssnitt med interna och externa partner.

Microsofts system tillhandahåller kontinuerlig intrångsidentifiering och förebyggande, förebyggande av serviceattacker, regelbundna penetrationstester och kriminaltekniska verktyg som hjälper till att identifiera och minska hot. [Multifaktorautentisering](../articles/active-directory/authentication/multi-factor-authentication.md) ger ett extra säkerhetslager för slutanvändare att komma åt nätverket. Och för programmet och värdleverantören erbjuder Microsoft åtkomstkontroll, övervakning, anti-malware, sårbarhetsskanning, korrigeringsfiler och konfigurationshantering.

Lösningsacceleratorerna utnyttjar den säkerhet och sekretess som är inbyggd i Azure-plattformen tillsammans med SDL- och OSA-processerna för säker utveckling och drift av all Microsoft-programvara. Dessa procedurer ger infrastrukturskydd, nätverksskydd och identitets- och hanteringsfunktioner som är grundläggande för säkerheten för alla lösningar.

[Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) inom [IoT-lösningsacceleratorerna](../articles/iot-fundamentals/iot-introduction.md) erbjuder en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan IoT-enheter och Azure-tjänster som [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) och Azure [Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) med hjälp av säkerhetsautentiseringsuppgifter per enhet och åtkomstkontroll.

För att bäst kommunicera säkerhets- och sekretessfunktioner som är inbyggda i Azure IoT-lösningsacceleratorer, delar den här artikeln upp sviten i de tre primära säkerhetsområdena.

![Azure IoT-lösningsacceleratorer](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Säker etablering och autentisering av enheter

Lösningen acceleratorer säkra enheter medan de är ute på fältet genom att tillhandahålla en unik identitetsnyckel för varje enhet, som kan användas av IoT-infrastrukturen för att kommunicera med enheten medan den är i drift. Processen är snabb och enkel att ställa in. Den genererade nyckeln med ett användarvalt enhets-ID utgör grunden för en token som används i all kommunikation mellan enheten och Azure IoT Hub.

Enhets-ID:n kan associeras med en enhet under tillverkningen (det vill ha blixtrade till i en maskinvaruförtroendemodul) eller använda en befintlig fast identitet som proxy (till exempel CPU-serienummer). Eftersom det inte är enkelt att ändra den här identifierande informationen i enheten är det viktigt att införa logiska enhets-ID:n om den underliggande enhetsmaskinvaran ändras men den logiska enheten förblir densamma. I vissa fall kan associationen av en enhetsidentitet ske vid enhetsdistributionen (till exempel konfigurerar en autentrad fälttekniker fysiskt en ny enhet när du kommunicerar med lösningens serverda). [IoT Hub-identitetsregistret](../articles/iot-hub/iot-hub-devguide.md) tillhandahåller säker lagring av enhetsidentiteter och säkerhetsnycklar för en lösning. Enskilda eller grupper av enhetsidentiteter kan läggas till i en tillåt-lista, eller en blockeringslista, vilket möjliggör fullständig kontroll över enhetsåtkomst.

Azure IoT Hub-åtkomstkontrollprinciper i molnet aktiverar och inaktiverar en enhetsidentitet, vilket ger ett sätt att ta bort en enhet från en IoT-distribution när det behövs. Den här associationen och disassociation av enheter baseras på varje enhetsidentitet.

Andra säkerhetsfunktioner för enheten är:

* Enheter accepterar inte oönskade nätverksanslutningar. De upprättar alla anslutningar och rutter på ett utgående sätt. För att en enhet ska kunna ta emot ett kommando från backend måste enheten initiera en anslutning för att söka efter väntande kommandon som ska bearbetas. När en anslutning mellan enheten och IoT Hub är säkert etablerad kan meddelanden från molnet till enheten och enheten till molnet skickas transparent.

* Enheter ansluter bara till eller upprättar vägar till välkända tjänster som de är peer-spelare med, till exempel en Azure IoT Hub.

* Auktorisering och autentisering på systemnivå använder identiteter per enhet, vilket gör åtkomstautentiseringsuppgifter och behörigheter nästan omedelbart återkallade.

### <a name="secure-connectivity"></a>Säker anslutning

Hållbarhet för meddelanden är en viktig funktion i alla IoT-lösningar. Behovet av att på ett sätt leverera kommandon och/eller ta emot data från enheter understryks av det faktum att IoT-enheter är anslutna via Internet eller andra liknande nätverk som kan vara otillförlitliga. Azure IoT Hub erbjuder hållbarhet för meddelanden mellan molnet och enheter via ett system av bekräftelser som svar på meddelanden. Ytterligare hållbarhet för meddelanden uppnås genom cachelagring av meddelanden i IoT Hub i upp till sju dagar för telemetri och två dagar för kommandon.

Effektivitet är viktigt för att säkerställa bevarande av resurser och drift i en resursbegränsad miljö. HTTPS (HTTP Secure), den säkra versionen av det populära http-protokollet av branschstandard, stöds av Azure IoT Hub, vilket möjliggör effektiv kommunikation. Advanced Message Queuing Protocol (AMQP) och Message Queuing Telemetry Transport (MQTT), som stöds av Azure IoT Hub, är utformade inte bara för effektivitet när det gäller resursanvändning utan även tillförlitlig meddelandeleverans. 

Skalbarhet kräver möjligheten att säkert samverka med ett brett utbud av enheter. Azure IoT-hubb möjliggör säker anslutning till både IP-aktiverade och icke-IP-aktiverade enheter. IP-aktiverade enheter kan ansluta och kommunicera direkt med IoT Hub via en säker anslutning. Icke-IP-aktiverade enheter är resursbegränsade och ansluter endast över korta kommunikationsprotokoll, till exempel Zwave, ZigBee och Bluetooth. En fältgateway används för att aggregera dessa enheter och utför protokollöversättning för att möjliggöra säker dubbelriktad kommunikation med molnet.

Ytterligare säkerhetsfunktioner för anslutning är:

* Kommunikationssökvägen mellan enheter och Azure IoT Hub, eller mellan gateways och Azure IoT Hub, skyddas med hjälp av TLS (Transport Layer Security) av branschstandard med Azure IoT Hub autentiserat med X.509-protokollet.

* För att skydda enheter från oönskade inkommande anslutningar öppnar Azure IoT Hub ingen anslutning till enheten. Enheten initierar alla anslutningar.

* Azure IoT Hub lagrar vederbörligen meddelanden för enheter och väntar på att enheten ska ansluta. Dessa kommandon lagras i två dagar, vilket gör att enheter som ansluter sporadiskt, på grund av ström- eller anslutningsproblem, kan ta emot dessa kommandon. Azure IoT Hub underhåller en kö per enhet för varje enhet.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Säker bearbetning och lagring i molnet

Från krypterad kommunikation till bearbetning av data i molnet hjälper lösningsacceleratorerna till att skydda data. Det ger flexibilitet att implementera ytterligare kryptering och hantering av säkerhetsnycklar.

Med Hjälp av Azure Active Directory (AAD) för användarautentisering och auktorisering kan Azure IoT-lösningsacceleratorer tillhandahålla en principbaserad auktoriseringsmodell för data i molnet, vilket möjliggör enkel åtkomsthantering som kan granskas och granskas. Den här modellen möjliggör också nästan omedelbar återkallning av åtkomst till data i molnet och av enheter som är anslutna till Azure IoT-lösningsacceleratorer.

När data finns i molnet kan de bearbetas och lagras i alla användardefinierade arbetsflöden. Åtkomst till varje del av data styrs med Azure Active Directory, beroende på vilken lagringstjänst som används.

Alla nycklar som används av IoT-infrastrukturen lagras i molnet i säker lagring, med möjlighet att rulla över om nycklar måste återetableras. Data kan lagras i [Azure Cosmos DB](../articles/cosmos-db/introduction.md) eller i [SQL-databaser](../articles/sql-database/sql-database-faq.md), vilket möjliggör definition av den säkerhetsnivå som önskas. Dessutom är Azure ett sätt att övervaka och granska all åtkomst till dina data för att varna dig om intrång eller obehörig åtkomst.

## <a name="conclusion"></a>Slutsats

Sakernas Internet börjar med dina saker – de saker som betyder mest för företagen. IoT kan leverera fantastiskt värde till ett företag genom att minska kostnaderna, öka intäkterna och omvandla affärer. Framgången för denna omvandling beror till stor del på att välja rätt IoT-programvara och tjänsteleverantör. Det innebär att hitta en leverantör som inte bara katalyserar denna omvandling genom att förstå företagens behov och krav, men också tillhandahåller tjänster och programvara byggd med säkerhet, integritet, öppenhet och efterlevnad som viktiga designöverväganden. Microsoft har lång erfarenhet av att utveckla och distribuera säker programvara och tjänster och fortsätter att vara ledande i denna nya tidsålder av Sakernas Internet.

Lösningsacceleratorerna bygger in säkerhetsåtgärder genom design, vilket möjliggör säker övervakning av tillgångar för att förbättra effektiviteten, driva operativa prestanda för att möjliggöra innovation och använda avancerad dataanalys för att omvandla företag. Med sin metod i flera lager för säkerhet, flera säkerhetsfunktioner och designmönster hjälper lösningsacceleratorerna till att distribuera en infrastruktur som kan lita på att omvandla alla företag.

## <a name="additional-information"></a>Ytterligare information

Varje lösningsaccelerator skapar instanser av Azure-tjänster, till exempel:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): Din gateway som ansluter molnet till enheter. Du kan skala till miljontals anslutningar per hubb och bearbeta stora mängder data med stöd för autentisering per enhet som hjälper dig att skydda din lösning.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): En skalbar, fullt indexerad databastjänst för halvstrukturerade data som hanterar metadata för de enheter som du etablerar, till exempel attribut, konfiguration och säkerhetsegenskaper. Azure Cosmos DB erbjuder högpresterande och högdataflödesbearbetning, schemaaoberoende indexering av data och ett omfattande SQL-frågegränssnitt.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Realtidsströmbearbetning i molnet som gör att du snabbt kan utveckla och distribuera en billig analyslösning för att upptäcka insikter i realtid från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till valfri volym samtidigt som du uppnår hög dataflöde, låg latens och återhämtning.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): En molnplattform för att skapa kraftfulla webb- och mobilappar som ansluter till data var som helst. i molnet eller lokalt. Skapa engagerande mobilappar för iOS, Android och Windows. Integrera med din SaaS (Software as a Service) och företagsprogram med direktanslutning till dussintals molnbaserade tjänster och företagsprogram. Kod på ditt favoritspråk och IDE –.NET, Node.js, PHP, Python eller Java – för att skapa webbappar och API:er snabbare än någonsin.

* [**Logikappar**](https://azure.microsoft.com/services/app-service/logic/): Logic Apps-funktionen i Azure App Service hjälper dig att integrera din IoT-lösning till dina befintliga affärssystem och automatisera arbetsflödesprocesser. Logic Apps gör det möjligt för utvecklare att utforma arbetsflöden som startar från en utlösare och sedan utföra en rad steg – regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps erbjuder direktanslutning till ett stort ekosystem av SaaS-, molnbaserade och lokala program.

* [**Azure Blob-lagring:**](https://azure.microsoft.com/services/storage/)Tillförlitlig, ekonomisk molnlagring för de data som dina enheter skickar till molnet.