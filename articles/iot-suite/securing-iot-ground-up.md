---
title: "Att säkra din Sakernas Internet från grunden in | Microsoft Docs"
description: "Den här artikeln beskrivs de inbyggda säkerhetsfunktionerna i Microsoft Azure IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: YuriDio
manager: timlt
editor: 
ms.assetid: 10252dfa-8313-4a97-9bd6-a3f1345dd3be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 5979e5e4baa385cc0aaff5f1277f2a0f7492c426
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Sakernas Internet security från grunden
Sakernas Internet (IoT) utgör unika säkerhet, sekretess och kompatibilitet utmaningar för företag över hela världen. Till skillnad från traditionella cyber teknik där problemen omfångsfasen handlar om programvara och hur den har implementerats gäller IoT vad som händer när cyber och fysiska arbetslivet Konvergera. Skydda IoT-lösningar kräver att säkerställa säker etablering av enheter, säker anslutning mellan dessa enheter och molnet och säkert dataskydd i molnet under bearbetning och lagring. Arbeta mot dessa funktioner är dock begränsad resurs enheter, geografisk fördelning av distributioner och ett stort antal enheter i en lösning.

Den här artikeln innehåller hur Microsoft Azure IoT Suite ger en säker och privat molnlösning för Sakernas Internet. Azure IoT Suite ger en komplett lösning för slutpunkt till slutpunkt med säkerhet som är inbyggda i varje steg från grunden. Microsoft säker programutveckling är en del av programvara tekniker tips är rotad i vår åren länge upplevelse av att utveckla säkra program. För att säkerställa att detta, är Security Development Lifecycle (SDL) den grundläggande development metod, tillsammans med en mängd infrastruktur säkerhetsnivå tjänster som operativa säkerhet försäkran OSA och Microsoft Digital Crimes Unit i Microsoft Security Response Center och Microsoft Malware Protection Center. 

Azure IoT Suite erbjuder unika funktioner som gör etablering, anslutning till och lagra data från IoT-enheter enkelt och transparent och de flesta av alla, säker. Vi granska säkerhetsfunktioner Azure IoT Suite och distributionsmetoder för säkerhet, sekretess och kompatibilitet utmaningar behandlas i den här artikeln. 

## <a name="introduction"></a>Introduktion
Sakernas Internet (IoT) är wave i framtiden kan erbjuda företag omedelbara och verkliga möjligheter att minska kostnaderna, öka intäkter och transformera verksamheten. Många företag är dock tveka att distribuera IoT i organisationen på grund av frågor om säkerhet, sekretess och kompatibilitet. En större punkt rör kommer från IoT-infrastrukturen, vilken sammanfogar cyber och fysiska världar tillsammans sammanslagning enskilda inneboende risker i dessa fördelarna unika. Säkerheten för IoT som rör säkerställer integriteten hos kod som körs på enheter, enhets- och autentisering, definiera Rensa ägarskap för enheter (samt data som genereras av enheterna) och vara motståndskraftiga mot fysiska angrepp och cyber. 

Det är problemet sekretess. Företag vill genomskinlighet för insamling av data, till exempel i vad samlas in och varför, som kan se den, som styr åtkomst och så vidare. Slutligen finns allmänna säkerhetsfrågor utrustningens tillsammans med de personer som arbetar dem, och problem med att underhålla branschstandarder för kompatibilitet.

Säkerhet, sekretess, genomskinlighet och efterlevnad av säkerhetsskäl får förblir välja rätt IoT lösningsleverantören en utmaning. Gå till ihop enskilda delar av IoT-programvara och tjänster som tillhandahålls av olika leverantörer introducerar luckor i säkerhet, sekretess, genomskinlighet och kompatibilitet som kan vara svåra att möjligt identifiera alone åtgärda. Val av rätt IoT programvara och service provider baseras på att söka efter leverantörer som har omfattande erfarenhet av tjänster som omfattar över lodräta annonser och geografiska områden, men kan även skala på en säker och transparent sätt. På liknande sätt hjälper för den markerade providern att ha åren erfarenhet av att utveckla säkra program som körs på datorer över hela världen miljarder och har möjlighet att uppskatta hotbild med den här nya världen Sakernas Internet.

## <a name="secure-infrastructure-from-the-ground-up"></a>Säker infrastruktur från grunden
Den [Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) infrastrukturen stöder mer än en miljard kunder i 127 länder. Ritning på vår åren lång erfarenhet bygga företagsprogram och köra några av de största onlinetjänsterna i världen, ge vi högre nivåer av förbättrad säkerhet, sekretess, efterlevnad och hot minskning praxis än de flesta kunder kan uppnå på egen hand.

Vår [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) ger en obligatorisk företagsomfattande utvecklingsprocessen som bäddar in säkerhetskrav i livscykeln för hela programmet. För att säkerställa att driften följer samma nivå av säkerhetspraxis, använder vi rigorösa säkerhetsriktlinjer utvecklas i vår operativa säkerhet försäkran OSA process. Vi arbetar också tillsammans med tredje parts audit företag för pågående verifiering att vi uppfyller våra efterlevnad skyldigheter och vi bedriva bred säkerhet arbete genom att skapa mittpunkter utmärkt, inklusive Microsoft Digital Crimes Unit i Microsoft Security Response Center och Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - säker IoT-infrastruktur för ditt företag
Microsoft Azure erbjuder en komplett molnlösning som kombinerar en ständigt växande samling integrerade molntjänster – analytics, machine learning, lagring, säkerhet, nätverk och web – med ett åtagande branschledande skydd och säkerheten för dina data. Vår [förutsätter intrång](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategi använder en dedikerad ”red grupp” programvara säkerhetsexperter som simulerar attacker, testa möjligheten Azure att identifiera, skydda mot nya hot och återställa från överträdelser. Vår [globala incidenter](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) team fungerar dygnet runt för att minimera effekterna av attacker och skadlig aktivitet. Teamet följer etablerade procedurer för hantering av incidenter, kommunikation och återställning och använder identifierbart och förutsägbara gränssnitt med interna och externa partners.

Våra system ger kontinuerlig intrångsidentifiering och förebygga, service skydd mot attacker, reguljära intrång testning och kriminalteknisk verktyg för att identifiera och åtgärda hot. [Multifaktorautentisering](../multi-factor-authentication/multi-factor-authentication.md) ger ett extra lager av säkerhet för slutanvändare få åtkomst till nätverket. Och för programmet och värd-providern, vi erbjuder åtkomstkontroll, övervakning, skadlig kod, säkerhetsproblem genomsökning, korrigeringsfiler och konfigurationshantering.

Microsoft Azure IoT Suite drar nytta av säkerhet och sekretess som är inbyggda i Azure-plattformen tillsammans med vår SDL och OSA processer för säker utveckling och drift av alla Microsoft-programvara. De här procedurerna tillhandahålla infrastrukturen skydd, nätverksskydd och identitets- och funktioner som är grundläggande för att säkerheten för en lösning. 

Den [Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md) inom den [IoT Suite](iot-suite-what-is-azure-iot.md) ger en fullständigt hanterad tjänst som gör tillförlitlig och säker dubbelriktad kommunikation mellan IoT-enheter och Azure-tjänster som [ Azure Machine Learning](../machine-learning/studio/what-is-machine-learning.md) och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) genom att använda säkerhetsreferenser per enhet och åtkomstkontroll.

Om du vill kommunicera bäst säkerhet och sekretess som finns inbyggda i Azure IoT Suite, har vi uppdelade suite till tre primära säkerhetsområden. 

![Azure IoT Suite](media/securing-iot-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Säker enhetsetableringen och autentisering
Azure IoT Suite skyddar enheter när de ut i fältet genom att tillhandahålla en unik identitetsnyckel för varje enhet som kan användas av IoT-infrastruktur för att kommunicera med enheten när den är i drift. Processen går snabbt och enkelt att ställa in. Genererad nyckel med en användarvalda enhets-ID utgör grunden för en token som används i all kommunikation mellan enheten och Azure IoT Hub.

Enhets-ID kan vara kopplad till en enhet under tillverkning (d.v.s. blinkade i en modul för maskinvara förtroende) eller använda en befintlig fast identitet som en proxy (till exempel serienummer CPU). Eftersom den här identifieringsinformation i enheten inte är enkelt, är det viktigt att införa logiska enhets-ID om de underliggande enhet maskinvaruändringarna men den logiska enheten är densamma. I vissa fall kan det inträffa associationen mellan en enhetsidentitet vid tidpunkten för distribution (d.v.s. en autentiserad fältet tekniker fysiskt konfigurerar en ny enhet under kommunikationen med serverdelen lösning). Den [Azure IoT Hub identitetsregistret](../iot-hub/iot-hub-devguide.md) tillhandahåller säker lagring av enheten identiteter och säkerhetsnycklar för en lösning. En eller flera enheter identiteter kan läggas till en lista över tillåtna eller blockerade webbplatser, aktivera fullständig kontroll över enheten.

Azure IoT-hubb kontrollen åtkomstprinciper i molnet aktivera aktivering och inaktivering av alla enhetsidentitet, vilket ger ett sätt att koppla en enhet från en IoT-distribution vid behov. Den här kopplingen och disassociation enheter baseras på varje enhetsidentitet.

Säkerhetsfunktioner för ytterligare enheter inkluderar följande:

* Enheter som accepterar inte oönskade nätverksanslutningar. De upprättar alla anslutningar och vägar på ett sätt som endast utgående. För en enhet tar emot ett kommando från serverdelen måste enheten upprättar en anslutning till att söka efter alla väntande kommandon för att bearbeta. När du är på ett säkert sätt att upprätta en anslutning mellan enheten och IoT-hubb, kan meddelanden från moln till enhet och enheten till molnet skickas transparent.  
* Enheter endast ansluta till eller upprätta vägar till välkända tjänster som de är peerkopplat, till exempel en Azure IoT-hubb.
* Systemnivå autentiseringen och auktoriseringen av använda per enhet identiteter, autentiseringsuppgifter och behörigheter nära-omedelbart återkallningsbar.

### <a name="secure-connectivity"></a>Säker anslutning
Hållbarhet av meddelanden är en viktig funktion i alla IoT-lösningar. Behovet av att varaktigt leverera kommandon och/eller ta emot data från enheter som är understruket av det faktum att IoT-enheter är anslutna via Internet eller andra liknande nätverk som kan vara instabilt. Azure IoT-hubb erbjuder hållbarhet meddelandehantering mellan moln och enheter via ett system för bekräftelser som svar på meddelanden. Ytterligare hållbarhet för meddelanden uppnås genom cachelagring meddelanden i IoT-hubb för upp till sju dagar för telemetri och två dagar för kommandon.

Effektivitet är viktigt att skydda resurser och åtgärden i en miljö med begränsad resurs. HTTPS (http-säkerhet), branschstandard säker version av populära http-protokollet stöds av Azure IoT-hubb som möjliggör effektiv kommunikation. Avancerade Message Queuing-protokollet (AMQP) och Message Queuing telemetri Transport (MQTT), stöds av Azure IoT Hub är utformade för effektivitet vad gäller Resursanvändning men tillförlitliga meddelandeleverans. 

Skalbarhet kräver möjlighet att på ett säkert sätt samverka med en mängd olika enheter. Azure IoT-hubb kan säker anslutning till både IP-aktiverade och icke-IP-aktiverade enheter. IP-aktiverade enheter kan ansluta direkt och kommunicerar med IoT-hubben över en säker anslutning. Icke-IP-aktiverade enheter är begränsad av resursen och Anslut endast över korta avstånd kommunikationsprotokoll, till exempel Zwave, ZigBee och Bluetooth. Fältet gateway används för att sammanställa dessa enheter och utför översättning av protokollet för att aktivera säker dubbelriktad kommunikation med molnet.

Följande: ytterligare säkerhetsfunktioner

* Kommunikationssökvägen mellan enheter och Azure IoT-hubb eller mellan gatewayer och Azure IoT Hub skyddas med Azure IoT Hub autentiseras med X.509-protokollet branschstandard säkerhet TLS (Transport Layer).
* För att skydda enheter mot oönskade inkommande anslutningar, kan Azure IoT-hubb inte öppna en anslutning till enheten. Enheten initierar alla anslutningar. 
* Azure IoT-hubb varaktigt lagrar meddelanden för enheter och väntar på att enheten ansluter. Dessa kommandon lagras i två dagar att aktivera enheter som ansluter bara ibland, på grund av strömavbrott eller anslutningen frågor för att ta emot dessa kommandon. Azure IoT-hubb upprätthåller en per enhet kö för varje enhet.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Säker bearbetning och lagring i molnet
Azure IoT Suite hjälper att säkra data för från krypterad kommunikation till databearbetning i molnet. Det ger flexibilitet för att implementera ytterligare kryptering och hantering av säkerhetsnycklar. Azure Active Directory (AAD) ger för användarautentisering och auktorisering, Azure IoT Suite en principbaserad auktoriseringsmodellen för data i molnet, möjliggör enkel åtkomsthantering som kan granskas och granskas. Den här modellen kan också nästan omedelbar återkallade åtkomst till data i molnet och enheter som är anslutna till Azure IoT Suite.

När data finns i molnet, kan de bearbetas och lagras i alla användardefinierade arbetsflöden. Åtkomst till varje del av informationen som kontrolleras med Azure Active Directory, beroende på lagringstjänsten som används.

Alla nycklar som används av IoT-infrastruktur lagras i molnet i säker lagring med möjlighet att rulla över om nycklar måste vara etablerade igen. Data kan lagras i [Azure Cosmos DB](../cosmos-db/introduction.md) eller i [SQL-databaser](../sql-database/sql-database-faq.md), aktivera definitionen av säkerhetsnivån som önskas. Azure tillhandahåller också ett sätt att övervaka och granska all åtkomst till dina data att varna dig om eventuella intrång eller obehörig åtkomst.

## <a name="conclusion"></a>Slutsats
Sakernas Internet börjar med dina saker – de saker som betyder mest för företag. IoT kan leverera häpnadsväckande värdet till ett företag genom att minska kostnaderna, öka intäkter och omvandla företag. Lyckats transformationen beror till stor del på att välja rätt IoT programvara och service provider. Det innebär att hitta en provider som inte bara catalyzes transformationen genom att förstå behov och krav, men ger också tjänster och program som skapats med säkerhet, sekretess, genomskinlighet och kompatibilitet som större designöverväganden. Microsoft har omfattande erfarenhet av att utveckla och distribuera säkra program och tjänster och fortsätter att vara ledande i den här nya ålder Sakernas Internet. 

Microsoft Azure IoT Suite bygger i säkerhetsåtgärder genom design, aktivera säker övervakning av tillgångar att förbättra effektiviteten operativa Enhetsprestanda att aktivera innovation och använda avancerade analyser för att omvandla företag. Med dess överlappande tillvägagångssättet för säkerhet, flera säkerhetsfunktioner och designmönster kan Azure IoT Suite distribuera en infrastruktur som kan användas för att omvandla ett företag. 

## <a name="additional-information"></a>Ytterligare information
Varje Azure IoT Suite förkonfigurerade lösning skapar instanser av Azure-tjänster, till exempel följande:

* [**Azure IoT-hubb**](https://azure.microsoft.com/services/iot-hub/): din gateway som ansluter molnet till ”saker”. Du kan skala till miljontals anslutningar per hubb och bearbeta massiva mängder data med per enhet autentiseringsstöd som hjälper dig att skydda din lösning.
* [**Azure Cosmos-DB**](https://azure.microsoft.com/services/documentdb/): en skalbar, -indexerat databastjänst för halvstrukturerade data som hanterar metadata för enheterna som du etablerar, till exempel attribut, konfiguration och egenskaper för säkerhetsbehörighet. Cosmos DB ger hög prestanda och hög genomströmning bearbetning, schema-oberoende indexering av data och en omfattande SQL-gränssnitt.
* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): realtid dataströmmen som bearbetas i molnet som gör att du kan snabbt utveckla och distribuera en billig analytics lösning för att upptäcka realtidsinsikter från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till någon volym och ändå upprätthålla högt genomflöde, låg latens och återhämtning.
* [**Azure Apptjänst**](https://azure.microsoft.com/services/app-service/): en molnplattform för att skapa kraftfulla webb- och mobilappar som ansluter till data var som helst; i molnet eller lokalt. Skapa spännande mobilappar för iOS, Android och Windows. Integrera med programvara som en tjänst (SaaS) och företagsprogram med out box anslutningen till massor av molnbaserade tjänster och företagsprogram. Koden i dina Favoriter språk och IDE – .NET, Node.js, PHP, Python eller Java – skapa webbappar och API: er snabbare än någonsin.
* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funktion i Logic Apps i Azure App Service hjälper till att integrera IoT-lösningen till din befintliga line-of-business-system och automatisera arbetsflödesprocesser. Logic Apps kan utvecklare utforma arbetsflöden som startar från en utlösare och sedan exekverar en följd av steg, regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps erbjuder out box-anslutning till ett brett spektrum av SaaS, molnbaserade, och lokala program.
* [**Azure-blobblagring**](https://azure.microsoft.com/services/storage/): tillförlitliga ekonomiska molnlagring för de data som dina enheter skicka till molnet.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar IoT-lösningen finns:

* [IoT-säkerhetsmetoder][lnk-security-best-practices]
* [IoT-säkerhetsarkitekturen][lnk-security-architecture]
* [Säkra din IoT-distribution][lnk-security-deployment]

[lnk-security-best-practices]: iot-security-best-practices.md
[lnk-security-architecture]: iot-security-architecture.md
[lnk-security-deployment]: iot-suite-security-deployment.md

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Förutsägande Underhåll förkonfigurerade lösning: översikt][lnk-predictive-overview]
* [Vanliga frågor och svar om IoT Suite][lnk-faq]

Du kan läsa om IoT-hubb säkerhet i [styra åtkomsten till IoT-hubb] [ lnk-devguide-security] i utvecklarhandboken för IoT-hubb.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
