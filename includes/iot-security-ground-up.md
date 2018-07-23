---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 181432c3050bbc614898b1ddf779bc90239a35be
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39189408"
---
# <a name="internet-of-things-security-from-the-ground-up"></a>IOT-säkerhet från grunden

Sakernas Internet (IoT) utgör unika utmaningar för företag över hela världen som säkerhet, sekretess och efterlevnad. Till skillnad från traditionella cyberhot teknik där problemen kretsar kring programvara och hur det implementeras, gäller IoT vad som händer när cyberhot och de fysiska världarna Konvergera. Skydda IoT-lösningar kräver att säkerställa säker etablering av enheter, säkra anslutningar mellan dessa enheter och molnet och säkert dataskydd i molnet under bearbetning och lagring. Arbeta mot sådana funktioner, men är begränsad resurs enheter, geografisk fördelning av distributioner och ett stort antal enheter i en lösning.

Den här artikeln handlar om hur IoT-Lösningsacceleratorer ger en säker och privat IOT-lösning i molnet. Lösningsacceleratorer leverera en komplett lösning för slutpunkt till slutpunkt med skydd inbyggt i utvecklingens från grunden. På Microsoft och utveckla säkra program är delen av övningen programvaruingenjör, rotad decennierna Microsofts länge upplevelse av att utveckla säkra program. För att säkerställa att detta är Security Development Lifecycle (SDL) den grundläggande utvecklingsmetodiken, tillsammans med en mängd infrastrukturnivå säkerhetstjänster som Operational Security Assurance (OSA) och i Microsoft Digital Crimes Unit, Microsoft Security Response Center och Microsoft Malware Protection Center.

Lösningsacceleratorer erbjuder unika funktioner som gör etablering, ansluta till och lagrar data från IoT-enheter enkelt och transparent och de flesta av allt är säkra. Den här artikeln undersöker säkerhetsfunktioner för Azure IoT-Lösningsacceleratorer och distributionsstrategier att säkerställa säkerhet, sekretess och efterlevnad utmaningar behandlas.

## <a name="introduction"></a>Introduktion

Sakernas Internet (IoT) är mobilteknik var framtiden, erbjuder företag omedelbar och verkliga möjligheter att minska kostnaderna, öka intäkterna och transformera verksamheten. Många företag är dock tveka att distribuera IoT i sina organisationer på grund av frågor om säkerhet, sekretess och efterlevnad. En större punkt rör kommer från unikhet för IoT-infrastruktur, vilket sammanfogar cyberhot och fysiska världar tillsammans sammanslagning enskilda risker i dessa två världar. Säkerheten hos IoT gäller till att säkerställa integriteten hos kod som körs på enheter, enhets- och autentisering, definiera Rensa ägarskapet för enheter (samt data som genereras av dessa enheter) och vara motståndskraftiga mot cyberhot och fysiska attacker.

Det är problemet sekretess. Företag vill transparens om insamling av data, till exempel i vad som samlas in och varför, som kan se den, som kontrollerar åtkomst och så vidare. Slutligen finns allmänna säkerhetsfrågor utrustning tillsammans med de personer som fungerar dem, och problem med att underhålla branschstandarder för efterlevnad.

Med säkerhet, sekretess, transparens och efterlevnad frågor kan förblir välja rätt IoT-lösningsleverantör en utmaning. Kombinera enskilda delar av IoT-programvara och tjänster från olika leverantörer introducerar luckor i säkerhet, sekretess, transparens och efterlevnad, vilket kan vara svårt att identifiera, och ännu svårare att åtgärda. Val av rätt IoT-programvara och service provider baseras på att söka efter leverantörer som har omfattande erfarenhet av att köra tjänster som kan omfatta flera branscher och geografiska områden, men kan även skala på ett säkert och transparent sätt. Det hjälper på samma sätt för den valda providern har lång erfarenhet av med utveckling av säker programvara som körs på miljontals datorer över hela världen och möjlighet att uppskatta hotbilden i grunden som den här nya världen av Sakernas Internet.

## <a name="secure-infrastructure-from-the-ground-up"></a>Säker infrastruktur från grunden

Den [Microsoft Cloud](https://azure.microsoft.com) infrastruktur som stöder mer än en miljard kunder i 127 länder/regioner. Rita på Microsofts långa erfarenhet av företagsprogramvara att bygga och driva några av världens största onlinetjänster i världen, ger Microsoft Cloud högre nivåer av förbättrad säkerhet, sekretess, efterlevnad och threat kraftfulla metoder än de flesta kunder kan få på egen hand.

Den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) en utvecklingsprocess för företagsomfattande process som säkerhetsmekanismer i hela programmets livscykel. För att säkerställa att driftaktiviteter följer samma nivå av säkerhetsrutiner finns använder SDL rigorösa säkerhetsriktlinjerna i processen för Microsofts Operational Security Assurance (OSA). Microsoft samarbetar även med tredje parts revisionsbyråer för pågående verifiering att den uppfyller sina åtaganden för efterlevnad och Microsoft använder i bred security arbete genom att skapa expertcenter, inklusive Microsoft Digital Crimes Unit, för Microsoft Security Response Center och Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – säker IoT-infrastruktur för ditt företag

Microsoft Azure erbjuder en komplett molnlösning som kombinerar en ständigt växande samling integrerade molntjänster – analyser, maskininlärning, lagring, säkerhet, nätverk och webb – med vårt arbete med branschledande skydd och sekretessen för dina data. Microsofts [förmoda överträdelse](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategin använder en dedikerad *red team* av programvara säkerhetsexperter som simulerar attacker, testa möjligheten för Azure att identifiera och skydda mot nya hot och återställa från intrång. Microsofts [global incidenthantering](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) team fungerar dygnet runt för att minimera effekterna av angrepp och skadlig aktivitet. Teamet följer etablerade procedurer för hantering av incidenter, kommunikation och återställning och använder gränssnitt för att upptäcka och förutsägbara med interna och externa partners.

Microsofts system ger kontinuerlig intrångsidentifiering och dataförlustskydd, tjänsten skydd mot attacker, regelbundna penetrationstester och kriminaltekniska verktyg för att upptäcka och avhjälpa hot. [Multifaktorautentisering](../articles/active-directory/authentication/multi-factor-authentication.md) ger ett extra lager av säkerhet för slutanvändarna att ansluta till nätverket. Och för programmet och värd-providern, erbjuder Microsoft åtkomstkontroll, övervakning, mot skadlig kod, säkerhetsriskssökning, korrigeringar och konfigurationshantering.

Lösningsacceleratorer dra nytta av säkerhet och sekretess är inbyggt i Azure-plattformen tillsammans med SDL-processen och OSA processer för säker utveckling och drift av alla Microsoft-programvara. De här procedurerna tillhandahålla infrastrukturen skydd, nätverksskydd och identitet och hantering av funktioner som är fundamentalt för säkerheten för alla lösningar.

Den [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) inom den [IoT-Lösningsacceleratorer](../articles/iot-fundamentals/iot-introduction.md) erbjuder en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan IoT-enheter och Azure-tjänster som [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) och [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) genom att använda säkerhetsreferenser för varje enhet och åtkomstkontroll.

För att kommunicera bäst säkerhet och sekretess som finns inbyggda i Azure IoT-Lösningsacceleratorer, eliminerar den här artikeln suite till tre primära säkerhetsområden.

![Azure IoT-lösningsacceleratorer](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Säker enhetsetablering och autentisering

Lösningsacceleratorer skydda enheter när de är ut i fältet genom att tillhandahålla en unik identitet nyckel för varje enhet som kan användas av IoT-infrastruktur för att kommunicera med enheten medan den är i drift. Processen går snabbt och enkelt att konfigurera. Den genererade nyckeln med en användarvalda enhets-ID utgör grunden för en token som används i all kommunikation mellan enheten och Azure IoT Hub.

Enhets-ID kan associeras med en enhet under tillverkning (som är, blinkade i en modul för maskinvara förtroende) eller använda en befintlig fast identitet som en proxy (till exempel CPU serienummer). Eftersom ändrar den här identifieringsinformation i enheten inte är enkel, är det viktigt att introducera logiska enhets-ID om underliggande maskinvara ändras till enheten men den logiska enheten förblir densamma. I vissa fall kan associationen mellan en enhetsidentitet kan inträffa vid tidpunkten för distribution av enheten (till exempel en autentiserad fältet tekniker fysiskt konfigurerar en ny enhet vid kommunikation med lösningen serverdel). Den [Azure IoT Hub-identitetsregistret](../articles/iot-hub/iot-hub-devguide.md) ger säker lagring av enhetsidentiteter och säkerhetsnycklar för en lösning. En eller flera enhetsidentiteter kan läggas till en lista över tillåtna eller en Blockeringslista aktivera fullständig kontroll över enhetsåtkomsten.

Azure IoT Hub åtkomstkontrollprinciper i molnet aktivera aktivering och inaktivering av alla enhetsidentitet möjlighet att ta bort koppling till en enhet från en IoT-distribution vid behov. Den här associationen och disassociation enheternas baseras på varje enhetsidentitet.

Ytterligare säkerhetsfunktioner:

* Enheter accepterar inte oönskad nätverksanslutningar. De upprättar alla anslutningar och vägar endast utgående dirigering. För en enhet ska ta emot ett kommando från serverdelen måste enheten initiera en anslutning för att söka efter kommandon som väntar på att bearbeta. När upprättas en anslutning mellan enheten och IoT Hub på ett säkert sätt, meddelanden från molnet till enheten och enheten till molnet kan skickas transparent.
* Enheter kan du bara ansluta till eller upprätta vägar till välkända tjänster som de är peerkopplade, till exempel en Azure IoT-hubb.
* På systemnivå auktorisering och autentisering använder enhetsspecifika identiteter, vilket gör autentiseringsuppgifter och behörigheter nära-direkt kan återkallas.

### <a name="secure-connectivity"></a>Säker anslutning

Tillförlitlighet gällande meddelanden är en viktig funktion i alla IoT-lösningar. Behovet av att varaktigt leverera kommandon och/eller ta emot data från enheter är understruket av det faktum att IoT-enheter är anslutna via Internet eller andra liknande nätverk som kan vara osäkra. Azure IoT Hub har tillförlitlighet gällande meddelanden mellan molnet och enheter via ett system för bekräftelser som svar på meddelanden. Ytterligare tillförlitlighet för meddelanden uppnås genom att cachelagra meddelanden i IoT Hub i upp till sju dagar för telemetri och två dagar för kommandon.

Effektivitet är viktigt att skydda resurser och åtgärden i en miljö med begränsad resurs. HTTPS (http-säkerhet), den vanliga säkra versionen av populära http-protokollet som stöds av Azure IoT Hub möjliggör effektiv kommunikation. Avancerade Message Queuing Protocol (AMQP) och Message Queuing Telemetry Transport (MQTT), stöds av Azure IoT Hub är utformade inte bara för effektivitet när det gäller Resursanvändning men tillförlitlig meddelandeleverans. 

Skalbarhet kräver möjligheten att på ett säkert sätt samverka med en mängd olika enheter. Azure IoT hub tillhandahåller säker anslutning till både IP-aktiverad och icke-IP-aktiverade enheter. IP-aktiverade enheter kan ansluta direkt och kommunicera med IoT Hub via en säker anslutning. Icke-IP-aktiverade enheter är begränsad av resursen och Anslut endast med korta avstånd kommunikationsprotokoll som Zwave, ZigBee och Bluetooth. En fält-gateway används för att aggregera enheterna och utför protokollöversättning om du vill aktivera säker dubbelriktad kommunikation med molnet.

Säkerhetsfunktioner för ytterligare en anslutning är:

* Kommunikationsvägen mellan enheter och Azure IoT Hub, eller mellan gateway och Azure IoT Hub, skyddas med hjälp av branschstandard säkerhet TLS (Transport Layer) med Azure IoT Hub med X.509-protokollet.
* För att skydda enheter från oönskade inkommande anslutningar, öppnas Azure IoT Hub inte någon anslutning till enheten. Enheten initierar alla anslutningar.
* Azure IoT Hub varaktigt lagrar meddelanden för enheter och väntar på att enheten ansluter. Dessa kommandon lagras i två dagar att aktivera enheter som ansluter bara ibland, på grund av kapacitets- eller frågor, att ta emot dessa kommandon. Azure IoT Hub underhåller en kö för varje enhet för varje enhet.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Säker bearbetning och lagring i molnet

Från krypterad kommunikation att bearbetningsdata i molnet att i lösningsacceleratorerna skydda data. Det ger flexibilitet för att implementera ytterligare kryptering och hantering av säkerhetsnycklar.

Med Azure Active Directory (AAD) för användarautentisering och auktorisering, utgör Azure IoT-Lösningsacceleratorer en modell för principbaserad auktorisering för data i molnet, som möjliggör enkel åtkomsthantering som kan granskas och granskas. Den här modellen kan också nästan omedelbar återkallelse av åtkomst till data i molnet och är ansluten till Azure IoT-Lösningsacceleratorer.

När data i molnet, kan de bearbetas och lagras i alla anpassade arbetsflöden. Åtkomst till varje del av informationen som styrs med Azure Active Directory, beroende på storage-tjänsten används.

Alla nycklar som används av IoT-infrastruktur lagras i molnet i säker lagring, med möjlighet att rulla över om nycklar behöver etableras om. Data kan lagras i [Azure Cosmos DB](../articles/cosmos-db/introduction.md) eller i [SQL-databaser](../articles/sql-database/sql-database-faq.md), aktivering av definitionen av säkerhetsnivån som önskas. Azure tillhandahåller också ett sätt att övervaka och granska all åtkomst till dina data att varna dig om eventuella intrång och obehörig åtkomst.

## <a name="conclusion"></a>Sammanfattning

Sakernas Internet börjar med dina saker – de saker som är viktigast för företag. IoT kan leverera otroliga värde till ett företag genom att minska kostnaderna, öka intäkter och omvandla verksamhet. Den här omvandlingen beror huvudsakligen på att välja rätt IoT program- och providern. Det innebär att hitta en leverantör som inte bara catalyzes transformationen genom att förstå företagets behov och krav, men innehåller även tjänster och program som skapats med säkerhet, sekretess, transparens och efterlevnad som större beaktanden vid utformning. Microsoft har omfattande med utveckling och distribution av säker programvara och tjänster och fortsätter att vara ledande i den här ny epok för Sakernas Internet.

Lösningsacceleratorer skapa i säkerhetsåtgärder avsiktligt, aktivera säker övervakning av tillgångar för att förbättra effektiviteten, enhet operativa prestanda som möjliggör innovation och använda avancerad dataanalys att omvandla företag. Med dess lager för säkerhet, flera säkerhetsfunktioner och designmönster hjälpa Lösningsacceleratorer distribuera en infrastruktur som kan användas för att omvandla alla företag.

## <a name="additional-information"></a>Ytterligare information

Varje lösningsaccelerator skapar instanser av Azure-tjänster, till exempel:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): din gateway som ansluter molnet till enheter. Du kan skala till miljoner anslutningar per hubb och bearbeta enorma mängder data med stöd för autentisering per enhet vilket hjälper dig att skydda din lösning.
* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): en skalbar och fullständigt indexerade databastjänst för halvstrukturerade data som hanterar metadata för enheterna som du etablerar, till exempel attribut, konfiguration och säkerhetsegenskaper för. Azure Cosmos DB erbjuder hög prestanda och hög genomströmning bearbetning, schemaoberoende indexering av data och ett omfattande SQL-gränssnitt.
* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): strömningsbearbetning i molnet som hjälper dig att snabbt utveckla och distribuera en analyslösning med låg kostnad för att hämta information i realtid från enheter, sensorer, infrastruktur och program . Data från den här fullständigt hanterade tjänsten kan skalas till alla volymer samtidigt som du får hög genomströmning, Låg fördröjning och återhämtning.
* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): en plattform för att skapa avancerade webb- och mobilappar som ansluter till data överallt; i molnet eller lokalt. Skapa spännande mobilappar för iOS, Android och Windows. Integrera med programvara som en tjänst (SaaS) och företagsprogram med out-of the box anslutningen till massor av molnbaserade tjänster och företagsprogram. Koda på önskat språk och IDE – .NET, Node.js, PHP, Python eller Java – att skapa webbappar och API: er snabbare än någonsin.
* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): Logic Apps-funktionen i Azure App Service hjälper dig att integrera din IoT-lösning till dina befintliga line-of-business-system och automatisera arbetsflödesprocesser. Logic Apps kan utvecklare utforma arbetsflöden som startar från en utlösare och sedan köra en serie steg, regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps ger ut nyckelfärdig anslutning till ett enormt ekosystem med SaaS, molnbaserade och lokala program.
* [**Azure-bloblagring**](https://azure.microsoft.com/services/storage/): pålitlig, ekonomisk molnlagring för de data som enheterna skickar till molnet.