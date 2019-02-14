---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 61fb8380bcad7a30d822ab610f52e8515477d683
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56247169"
---
# <a name="internet-of-things-iot-security-architecture"></a>Säkerhetsarkitektur för Internet of Things (IoT)

När du designar ett system, är det viktigt att förstå de potentiella hot på systemet och lägga till lämpliga försvar, eftersom systemet är utformad och byggts. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom förstå hur en angripare kan eventuellt att angripa en dator gör att lämpliga åtgärder finns på plats från början.

## <a name="security-starts-with-a-threat-model"></a>Säkerhet som börjar med en hotmodell

Microsoft har länge använt hotmodeller för sina produkter och gjort företagets threat modeling process som är allmänt tillgängliga. Företagets upplevelsen visar att modellering har oväntat fördelar utöver den omedelbara förståelsen för vad hoten som är mest om. Till exempel skapar det också en väg för en öppen diskussion med andra utanför Utvecklingsteamet, vilket kan leda till nya idéer och förbättringar i produkten.

Målet med hotmodellering är att förstå hur en angripare kan eventuellt att angripa en dator och sedan kontrollera att lämpliga åtgärder är på plats. Threat modellering framtvingar den grupp att tänka på åtgärder som systemet är utformat för i stället för när systemet har distribuerats. Detta är ytterst viktigt, eftersom det är omöjligt att onlineåterställningspunkter security försvar för en mängd olika enheter i fältet, felbenägna och lämnar kvar kunder på riskerar.

Många utvecklingsteam göra ett tydligt sätt samla in funktionskrav för systemet som har nytta av kunder. Identifiera icke-uppenbart sätt att någon kan missbrukas systemet är dock mer utmanande. Hotmodellering kan hjälpa att utvecklingsteam förstå vad en angripare kan göra och varför. Hotmodellering är en strukturerad process som skapar en diskussion om säkerheten designbeslut i systemet, samt ändringar i designen som görs på vägen inverkan säkerheten. En hotmodell är bara ett dokument, representerar den här dokumentationen också ett perfekt sätt att säkerställa kontinuitet kunskap, kvarhållning av lektioner lärt dig och hjälp med att nya team snabbt komma igång. Slutligen är ett resultat av hotmodellering så att du kan överväga andra aspekter av säkerhet, till exempel vilka säkerhetsåtaganden som du vill ge dina kunder. Dessa åtaganden tillsammans med hotmodellering informera och genomför testning av din lösning för Internet of Things (IoT).

### <a name="when-to-do-threat-modeling"></a>När du ska hot modellering

[Hotmodellering](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) innehåller det största värdet när du sedan inkludera den i designfasen. När du designar, har den största flexibiliteten att göra ändringar för att eliminera hot. Eliminera hot avsiktligt är önskat utfall. Det är mycket enklare än att lägga till åtgärder, testa dem och att se till att de fortfarande är aktuella och dessutom sådan eliminering är inte alltid möjligt. Det blir svårare att eliminera hot som en produkt blir mognare och slutligen i sin tur kräver mer arbete och mycket svårare kompromisser än threat modeling tidigt i utvecklingen.

### <a name="what-to-consider-for-threat-modeling"></a>Tänk på följande för hotmodellering

Du bör se ut på lösningen som en helhet och också fokusera på följande områden:

* Funktioner för säkerhet och sekretess
* De funktioner vars fel är relevanta säkerhet
* De funktioner som touch en förtroendegräns

### <a name="who-performs-threat-modeling"></a>Som utför hotmodellering

Hotmodellering är en process som med andra. Det är en bra idé att behandla threat modellen dokumentet som alla andra komponenter i lösningen och verifiera den. Många utvecklingsteam göra ett tydligt sätt samla in funktionskrav för systemet som har nytta av kunder. Identifiera icke-uppenbart sätt att någon kan missbrukas systemet är dock mer utmanande. Hotmodellering kan hjälpa att utvecklingsteam förstå vad en angripare kan göra och varför.

### <a name="how-to-perform-threat-modeling"></a>Så här utför du hotmodellering

Hotet modellera processen består av fyra steg; stegen är:

* Modellera tillämpningen
* Räkna upp hot
* Identifiera hot
* Verifiera åtgärder

#### <a name="the-process-steps"></a>Processtegen

Tre tumregel att tänka på när du skapar en hotmodell:

1. Skapa ett diagram av Referensarkitektur.

2. Starta bredden först. Få en översikt och förstå systemet som helhet, innan du börjar med djup. Den här metoden hjälper till att säkerställa att du tittar på rätt plats.

3. Driv processen, låt inte processen driva din. Om du hittar ett problem i fasen modellering och vill utforska det, kör! Känner du måste följa de här stegen slavishly.

#### <a name="threats"></a>Hot

De fyra Kärnelementen i en hotmodell är:

* Processer som webbtjänster och Win32-tjänster och * nix daemon. Vissa komplexa entiteter (till exempel fält-gateways och sensorer) kan representeras som en process när en teknisk nedåt i dessa områden inte är möjligt.

* Datalagren (var som helst data lagras, till exempel en konfigurationsfil eller databas)

* Dataflöde (där data flyttas mellan andra element i programmet)

* Externa enheter (något som interagerar med systemet, men är inte kontrolleras av programmet, exempel inkludera användare och satellit feeds)

Alla element i Arkitekturdiagram omfattas av olika hot den här artikeln STRIDE mnemonik. Läs [Threat Modeling igen, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) vill veta mer om STRIDE-element.

Olika elementen i diagram program omfattas vissa STRIDE-hot:

* Processer som omfattas av STRIDE
* Dataflöden omfattas TID
* Datalager omfattas TID, och ibland R, när datalager som är loggfiler.
* Externa enheter omfattas SRD

## <a name="security-in-iot"></a>Säkerhet i IoT

Anslutna enheter för särskilda ändamål har ett stort antal möjliga interaktion ytor och interaktion mönster, som måste beaktas för att ge ett ramverk för att skydda digital åtkomst till dessa enheter. Termen ”digital access” används här att skilja från alla åtgärder som utförs via direct enhetsinteraktion där åtkomstsäkerhet är tillgängligt via fysisk åtkomstkontroll. Till exempel placera enheten i ett rum med ett lås på dörren. Även om det går inte att nekas fysisk åtkomst med hjälp av programvara och maskinvara, vidtas åtgärder för att förhindra att fysisk åtkomst till systemet störningar ledande.

När du utforskar interaktion mönster, kan du titta på ”enhet” och ”enhet” med samma uppmärksamhet. ”Enhetskontroll” kan klassificeras som all information som har angetts för en enhet av part med målet att ändra eller påverka sitt beteende för dess tillstånd eller tillståndet för sin miljö. ”Enhetsdata” kan klassificeras som all information som en enhet genererar andra parten om dess tillstånd och den observerade statusen för sin miljö.

För att optimera säkerhetsmetoder, rekommenderar vi att en typisk IoT-arkitekturen är uppdelad i flera komponent-zoner som en del av hotet modellering övningen. Zonerna är fullständigt som beskrivs i det här avsnittet, inklusive följande:

* -Enhet
* Fält-Gateway
* Gateways, i molnet och
* Tjänster.

Zoner är bred sätt att segmentera en lösning. varje zon har ofta sina egna data och autentisering och auktorisering krav. Zoner kan också användas för att skada för isolering och begränsa effekten av låg förtroende zoner på högre förtroende zoner.

Varje zon avgränsas med ett förtroende gräns, vilket anges som den prickad röda linjen i följande diagram. Representerar en övergång av data/information från en källa till en annan. Under den här ändringen kommer kan data/information vara föremål för Spoofing, Tampering, Repudiation, avslöjande av Information, Denial of Service och höjning av privilegier (STRIDE).

![Zoner för IoT-säkerhet](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De komponenter som visas i varje gräns genomgår också STRIDE, att aktivera en fullständig 360 threat modeling vy av lösningen. I följande avsnitt beskriver var och en av de komponenter och specifika säkerhetsfrågor och lösningar som ska införas.

I följande avsnitt beskrivs standard komponenter som vanligtvis finns i dessa zoner.

### <a name="the-device-zone"></a>Zonen enhet

Enhetsmiljö är omedelbar fysiskt utrymme runt enheten där fysisk åtkomst och/eller ”lokala nätverk” peer-to-peer digitala åtkomst till enheten som är möjligt. Ett ”lokalt nätverk” antas vara ett nätverk som är unik och isolerade från – men potentiellt bryggor till – det offentliga Internet och omfattar alla kort Håll trådlösa radio-teknik som möjliggör peer-to-peer-kommunikation av enheter. Den gör *inte* omfattar alla nätverk virtualiseringsteknik som skapar illusionen av ett lokalt nätverk och även omfattar inte publik operator-nätverk som kräver några två enheter kan kommunicera över offentligt nätverksutrymme om de har att ange en relation för peer-to-peer-kommunikation.

### <a name="the-field-gateway-zone"></a>Zonen för fält-gateway

Fält-gateway är en enhet/installation eller vissa allmänna datorn serverprogram som fungerar som verktyg för kommunikation och potentiellt, som ett kontrollsystem för enheten och enheten databearbetning hub. Zonen för fält-gateway innehåller den fält-gatewayen och alla enheter som är kopplade till den. Som namnet antyder, fält-gateways fungerar utanför dedikerade databearbetning verksamhet, är vanligtvis plats bunden, omfattas potentiellt fysiskt intrång och har begränsad operativa redundans. Alla om du vill att en fält-gateway är ofta en sak en touch och sabotera samtidigt som du vet vad dess funktion är.

En fältgateway skiljer sig från en router för enbart trafik eftersom den har en aktiv roll i hanteringen av åtkomst som informationsflödet, vilket innebär att det är ett program åtgärdas entitet och nätverksanslutning eller terminal-session. En NAT-enhet eller brandvägg, däremot inte uppfyller kraven som fält-gateways eftersom de inte är explicit anslutning eller session terminaler, men i stället en väg (eller block) anslutningar eller sessioner som görs via dem. Fält-gateway har två olika ytor. En står de enheter som är kopplade till den och representerar inuti zonen och den andra ansikten alla externa parter och är i utkanten av zonen.

### <a name="the-cloud-gateway-zone"></a>Zonen molnet gateway

Molngatewayen är ett system som möjliggör kommunikation från och till enheter eller fält-gateways från flera olika platser mellan offentligt nätverksutrymme, vanligtvis mot en molnbaserad kontroll och data analysis-system, en federation på sådana system. I vissa fall kan kan en molngateway omedelbart underlättar åtkomst till enheter för särskilda ändamål från terminaler, till exempel surfplattor eller telefoner. I den kontext som beskrivs här är ”moln” avsedd att referera till en dedikerad databearbetning-system som inte har bundits till samma plats som de anslutna enheter eller fält-gateways. Även förhindra riktade fysisk åtkomst operativa åtgärder i en moln-zon och exponeras inte nödvändigtvis till en ”offentliga” molninfrastrukturer.  

En molngateway kan potentiellt mappas till ett nätverksvirtualisering överlägg till isolerar molngatewayen och alla dess anslutna enheter eller fält-gateways från annan nätverkstrafik. Molngatewayen själva är inte ett system för enheten eller en bearbetning eller lagringsutrymmet för enhetsdata; Dessa anläggningar gränssnitt med molngatewayen. Molnet gateway zonen innehåller själva molngatewayen tillsammans med alla fält-gateways och enheter som är direkt eller indirekt anslutna till den. Edge i zonen är ett distinkt utsatt område där alla externa parter kommunicera via.

### <a name="the-services-zone"></a>Zonen tjänster

”Tjänst” har definierats för den här kontexten som alla programvarukomponent eller modulen som samverkar med enheter via en gateway för fältet eller molnet för datainsamling och analys, samt för kommando och kontroll. Tjänsterna är medlare. De fungerar under sin identitet för gatewayer och andra undersystem, lagra och analysera data, utfärdar självständigt kommandon till enheter utifrån datainsikter eller scheman och exponera information och styra funktioner för behöriga användare.

### <a name="information-devices-versus-special-purpose-devices"></a>Information-enheter och enheter för särskilda ändamål

Datorer, telefoner och surfplattor är främst interaktiva information enheter. Telefoner och surfplattor optimeras uttryckligen runt maximera batteri livslängd. De helst inaktivera delvis när du inte omedelbart interagerar med en person eller när de inte tillhandahåller tjänster som spela musik eller guidar filens ägare till en viss plats. Från ett system perspektiv agerar enheterna information technology främst som proxyservrar för människor. De är ”personer aktuatorer” föreslå åtgärder och ”personer sensorer” samla in indata.

Enheter för särskilda ändamål, är från enkla temperatursensorer till komplexa fabriksproduktionsrader med tusentals olika komponenter i dem, olika. Dessa enheter är mycket mer begränsade i syfte och även om vissa användargränssnittet ger, de huvudsakligen är begränsade till gränssnittet med eller integreras i tillgångar i den fysiska världen. De mäta och rapportera miljömässiga omständigheter, aktivera ventiler, styra servos, ljud larm, byta lamporna och utföra många andra uppgifter. De bidrar till att den fungerar som en information-enhet är antingen för Allmänt för dyrt, för stort eller för känsligt. Konkreta syftet anger omedelbart får sin tekniska designen som även den tillgängliga monetära budgeten för produktion och schemalagda livslängd åtgärden. Kombinationen av dessa två viktiga faktorer begränsar tillgängliga operativa energi budget, fysiska fotavtryck och därmed tillgängligt lagringsutrymme beräknings- och säkerhetsfunktioner.

Om något ”går fel” med automatiserade eller fjärranslutna kontrolleras enheter, till exempel defekter fysiska fel eller logik willful obehöriga intrång och manipulering av. Produktion många kan förstöras, byggnader kan vara looted eller bränt och personer kan vara skadade eller även chips. Det här är en helt annan klass informerats än någon ökar ett stulna kreditkort gränsen. Säkerhetsribban för enheter som gör flytta, samt för sensordata som så småningom resulterar i kommandon som orsakar saker att flytta, måste vara högre än i alla e-handel eller banktjänstscenario.

### <a name="device-control-and-device-data-interactions"></a>Kontroll av enhet och enheten data interaktioner

Anslutna enheter för särskilda ändamål har ett stort antal möjliga interaktion ytor och interaktion mönster, som måste beaktas för att ge ett ramverk för att skydda digital åtkomst till dessa enheter. Termen ”digital access” används här att skilja från alla åtgärder som utförs via direct enhetsinteraktion där åtkomstsäkerhet är tillgängligt via fysisk åtkomstkontroll. Till exempel placera enheten i ett rum med ett lås på dörren. Även om det går inte att nekas fysisk åtkomst med hjälp av programvara och maskinvara, vidtas åtgärder för att förhindra att fysisk åtkomst till systemet störningar ledande.

När du utforskar interaktion mönster, titta på ”enhet” och ”enhet” med samma uppmärksamhet vid hotmodellering. ”Enhetskontroll” kan klassificeras som all information som har angetts för en enhet av part med målet att ändra eller påverka sitt beteende för dess tillstånd eller tillståndet för sin miljö. ”Enhetsdata” kan klassificeras som all information som en enhet genererar andra parten om dess tillstånd och den observerade statusen för sin miljö.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Utför threat modeling för Azure IoT-Referensarkitektur

Microsoft använder ramverket som beskrivs tidigare för att göra threat modeling för Azure IoT. Följande avsnitt använder konkreta exempel på Azure IoT-Referensarkitektur för att demonstrera hur du förhåller dig threat modeling för IoT samt hur du löser hot som har identifierats. Det här exemplet identifierar fyra huvudområden för fokus:

* Enheter och datakällor
* Dataöverföring,
* Enhets- och bearbetning av händelser och
* Presentation

![Threat Modeling Azure IOT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Följande diagram ger en förenklad vy av Microsofts arkitektur för IoT med hjälp av en dataflödesdiagram-modellen som används av Microsoft Threat Modeling Tool:

![Threat Modeling för Azure IoT med MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Det är viktigt att notera att arkitekturen separerar funktioner för enheter och gateway. Den här metoden gör att användaren kan använda sig av gatewayenheter som är säkrare: de är kan kommunicera med molngatewayen med säker protokoll, som vanligtvis kräver större omkostnader bearbetning som en ursprunglig enhet – till exempel en termostat - kunde Ange på egen hand. Anta att Molngatewayen representeras av tjänsten Azure IoT Hub i zonen Azure-tjänster.

### <a name="device-and-data-sourcesdata-transport"></a>Enhets- och data källor/dataöverföring

Det här avsnittet utforskar arkitekturen som beskrivs tidigare instrumentpanel för hotmodellering och ger en översikt över hur att lösa några av de inbyggda problem. Det här exemplet fokuserar på core elementen i en hotmodell:

* Processer (både under din kontroll och externa objekt)
* Kommunikation (kallas även dataflöden)
* Lagring (kallas även för datalager)

#### <a name="processes"></a>Processer

Det här exemplet försöker åtgärda ett antal olika hot i olika faser data/information finns i i var och en av de kategorier som beskrivs i Azure IoT-arkitektur: processen, kommunikation och lagring. Nedan följer en översikt över vanligaste för kategorin ”process” följt av en översikt över hur dessa hot kan undvikas bäst:

**(S)-förfalskning**: En angripare kan extrahera kryptografiska nyckelmaterial från en enhet, antingen på programvara eller maskinvara och därefter åtkomst till systemet med en annan fysisk eller virtuell enhet under identiteten för enheten nyckelmaterial har tagits från. En bra bild är fjärrkontroller som kan använda valfri TV samt som är populära prankster verktyg.

**Denial of Service (D)**: En enhet kan återges inte klarar fungerar eller kommunicera genom att störa radio frekvenser eller övergripande kablar. En kamera för övervakning som hade dess power eller nätverksanslutningen avsiktligt blockerade rapportera inte till exempel data, alls.

**Manipulering (T)**: En angripare kan helt eller delvis ersätta den programvara som körs på enheten, potentiellt så att ersatta programvaran du använder äkta identiteten för enheten om nyckelmaterial eller kryptografiska funktioner innehåller viktiga material var tillgängliga för den olaglig program. En angripare kan till exempel använda extraherade nyckelmaterial för att fånga upp och ignorera data från enheten på kommunikationsvägen och Ersätt den med falska data som autentiseras med det stulna nyckelmaterialet.

**Avslöjande av information (I)**: Om enheten kör behandlas för programvara, kan programvaran behandlas potentiellt läcka data till obehöriga personer. En angripare kan till exempel använda extraherade nyckelmaterial för att mata in själva i kommunikationsvägen mellan enheten och en domänkontrollant eller fält-gateway eller molngatewayen till siphon av information.

**Rättighetsökning (E)**: En enhet som har en specifik funktion kan tvingas att göra något annat. En kran är programmerad att öppna halvvägs kan till exempel att öppna hela vägen.

| **Komponent** | **Hot** | **Problemlösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhet |S |Tilldela identitet till enheten och autentisering av enheten |Ersätt enhet eller en del av enheten med en annan enhet. Hur vet du att du talar på rätt enhet? |Autentisering av enheten, använda Transport Layer Security (TLS) eller IPSec. Infrastruktur bör ha i förväg delad nyckel (PSK) på de enheter som inte kan hantera fullständiga asymmetrisk kryptering. Använda Azure AD [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Tillämpa tamperproof mekanismer för att enheten, till exempel genom att göra det svårt att omöjligt att extrahera nycklar och andra kryptografiskt material från enheten. |Risken är om någon är manipulation enheten (fysiska störningar). Hur är du säker på att, enheten har inte ändrats. |Den mest effektiva lösning är en betrodd platform module (TPM)-funktion som gör att lagra nycklar i särskilda-chip kretsar där nycklarna som går inte att läsa, men kan bara användas för kryptografiska åtgärder som använder nyckeln men lämnar aldrig ut nyckeln. Minne kryptering av enheten. Nyckelhantering för enheten. Inloggningskod. | |
|| E |Med åtkomstkontroll över enheten. Auktoriseringsschema. |Om enheten tillåter enskilda åtgärder som ska utföras baserat på kommandon från en extern källa eller även komprometterade sensorer, kan angreppet att utföra åtgärder som annars inte tillgänglig. |Med auktoriseringsschema för enheten | |
| Field Gateway |S |Autentisera fält-gateway till Molngateway (t ex certifikat, PSK, eller anspråk baserade.) |Om någon kan imitera fält-Gateway, kan det ge själva som vilken enhet som helst. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Samma viktiga problem om lagring och attestering av enheter i allmänhet – bästa fall är Använd TPM. 6LowPAN tillägg för IPSec för trådlösa Sensor nätverk (WSN). |
|| TRID |Skydda fält-Gateway mot manipulation (TPM)? |Bedrägeri som lura molnet gateway-tänka det pratar med fält-gateway kan resultera i avslöjande av information och data manipulation |Minne-kryptering, TPM'S, autentisering. | |
|| E |Åtkomstkontroll för fält-Gateway | | | |

Här följer några exempel på hot i den här kategorin:

**Förfalskning**: En angripare kan extrahera kryptografiska nyckelmaterial från en enhet, antingen på programvara eller maskinvara och därefter åtkomst till systemet med en annan fysisk eller virtuell enhet under identiteten för enheten nyckelmaterial har tagits från.

**Denial of Service**: En enhet kan återges inte klarar fungerar eller kommunicera genom att störa radio frekvenser eller övergripande kablar. En kamera för övervakning som hade dess power eller nätverksanslutningen avsiktligt blockerade rapportera inte till exempel data, alls.

**Manipulering**: En angripare kan helt eller delvis ersätta den programvara som körs på enheten, potentiellt så att ersatta programvaran du använder äkta identiteten för enheten om nyckelmaterial eller kryptografiska funktioner innehåller viktiga material var tillgängliga för den olaglig program.

**Manipulering**: En kamera för övervakning som visar en synliga spektrumet bild av en tom Hall kan syfta ett foto av sådana Hall. En rök eller fire-sensor kunde rapporterar någon håller en ljusare under den. I båda fallen enheten kanske tekniskt fullständigt betrodd för systemet, men den rapporterar behandlas för information.

**Manipulering**: En angripare kan utnyttja extraherade nyckelmaterial för att fånga upp och ignorera data från enheten på kommunikationsvägen och Ersätt den med falska data som autentiseras med det stulna nyckelmaterialet.

**Manipulering**: En angripare kan helt eller delvis ersätta den programvara som körs på enheten, potentiellt så att ersatta programvaran du använder äkta identiteten för enheten om nyckelmaterial eller kryptografiska funktioner innehåller viktiga material var tillgängliga olaglig programmet.

**Avslöjande av information**: Om enheten kör behandlas för programvara, kan programvaran behandlas potentiellt läcka data till obehöriga personer.

**Avslöjande av information**: En angripare kan utnyttja extraherade nyckelmaterial för att mata in själva i kommunikationsvägen mellan enheten och en domänkontrollant eller fält-gateway eller molngatewayen till siphon av information.

**Denial of Service**: Enheten kan vara avstängd eller aktiverat i ett läge där kommunikation är inte möjlig (vilket är avsiktlig i många industriella datorer).

**Manipulering**: Enheten kan konfigureras för att fungera i ett tillstånd som är okända för kontrollsystem (utanför kända kalibreringsparametrar) och därmed tillhandahåller data som kan tolkas

**Rättighetsökning**: En enhet som har en specifik funktion kan tvingas att göra något annat. En kran är programmerad att öppna halvvägs kan till exempel att öppna hela vägen.

**Denial of Service**: Enheten kan omvandlas till ett tillstånd där kommunikation inte är möjlig.

**Manipulering**: Enheten kan konfigureras för att fungera i ett tillstånd som är okända för kontrollsystem (utanför kända kalibreringsparametrar) och därmed tillhandahåller data som kan tolkas.

**Förfalskning/manipulering/Repudiation**: Om inte skyddad (vilket är sällan vad gäller konsument fjärrkontroller), en angripare kan manipulera tillståndet för en enhet anonymt. En bra bild är fjärrkontroller som kan använda valfri TV samt som är populära prankster verktyg.

#### <a name="communication"></a>Kommunikation

Hot runt kommunikationsvägen mellan enheter, enheter och fält-gateways och gateway-enhet och molnet. I följande tabell har lite vägledning kring öppna sockets enhet/VPN-anslutning:

| **Komponent** | **Hot** | **Problemlösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enheten IoT-hubb |TID |(D) TLS (PSK/RSA) att kryptera trafiken |Avlyssning eller störningar kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivån. Du måste ta reda på hur du skyddar dem med anpassade protokoll. I de flesta fall sker kommunikationen från enheten till IoT Hub (enheten initierar anslutningen). |
| Enheten till enhet |TID |(D) TLS (PSK/RSA) att kryptera trafiken. |Läsning av data i rörelse mellan enheter. Manipulera data. Överbelastning enheten med nya anslutningar |Säkerhet på protokollnivån (MQTT/AMQP/HTTP/CoAP. Du måste ta reda på hur du skyddar dem med anpassade protokoll. Lösning för DoS-hot är att peer-enheter via molnet eller fält-gateway och ha dem endast act som klienter mot nätverket. Peer-kopplingen kan resultera i en direkt anslutning mellan peer-datorer efter att ha varit asynkrona av gateway |
| Extern enhet enhet |TID |Stark parkoppling av externa entiteten till enheten |Avlyssning anslutningen till enheten. Stör enheten kommunikationen |På ett säkert sätt länka till externa enheten NFC/Bluetooth LE att entiteten. Kontrollera den operativa panelen på enheten (fysiska) |
| Field Gateway Cloud Gateway |TID |TLS (PSK/RSA) att kryptera trafiken. |Avlyssning eller störningar kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivån (MQTT/AMQP/HTTP/CoAP). Du måste ta reda på hur du skyddar dem med anpassade protokoll. |
| Device Cloud Gateway |TID |TLS (PSK/RSA) att kryptera trafiken. |Avlyssning eller störningar kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivån (MQTT/AMQP/HTTP/CoAP). Du måste ta reda på hur du skyddar dem med anpassade protokoll. |

Här följer några exempel på hot i den här kategorin:

**Denial of Service**: Begränsad enheter är Allmänt under DoS-hot när de aktivt lyssna efter inkommande anslutningar eller oombedda datagram i ett nätverk, eftersom en angripare inte kan öppna många anslutningar parallellt och tjänsten dem eller tjänsten dem långsamt eller enheten kan vara översvämmas med oönskad trafik. I båda fallen återges enheten effektivt inte fungerar alls i nätverket.

**Förfalskning, avslöjande av Information**: Begränsad enheter och enheter för särskilda ändamål har ofta en för alla säkerhet anläggningar som lösenord eller PIN-kod protection eller att de helt beroende av betrodda nätverk, vilket innebär att de beviljar åtkomst till information när en enhet är på samma nätverk och nätverket skyddas ofta bara av en delad nyckel. Det innebär att när den delade hemligheten till enheten eller nätverk lämnas, det är möjligt att styra enheten och Observera data som sänts ut från enheten.  

**Förfalskning**: en angripare kan komma åt eller delvis åsidosätta sändningen och förfalska avsändaren (mannen i mitten)

**Manipulering**: en angripare kan komma åt eller delvis åsidosätta sändningen och skicka falsk information 

**Avslöjande av information:** en angripare kan eavesdrop på en sändning och få information utan behörighet **Denial of Service:** en angripare kan sylt broadcast signalen och neka information distribution

#### <a name="storage"></a>Storage

Varje enhet och fält-gateway har någon form av lagring (tillfälligt för queuing data, operativsystem (OS) bildlagring).

| **Komponent** | **Hot** | **Problemlösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhetslagring |TRID |Lagringskryptering, signering loggarna |Läsning av data från storage (PII-data), manipulera telemetridata. Manipulera i kö eller cachelagrade data för kontroll av kommandot. Manipulera konfiguration eller inbyggd programvara uppdateringspaket kan medan cachelagras eller i kö lokalt leda till OS och/eller system komponenter äventyras |Kryptering, message authentication code (MAC) eller digital signatur. Där styr möjligt, stark åtkomstkontroll via resursåtkomst listor (ACL) eller behörigheter. |
| Enhetens OS-avbildning |TRID | |Manipulera OS / ersätta OS-komponenter |OS-partition är skrivskyddad, loggat OS-avbildning, kryptering |
| Fält-Gateway-lagring (queuing data) |TRID |Lagringskryptering, signering loggarna |Läsning av data från storage (PII-data), manipulera telemetridata, manipulera i kö eller cachelagrade data för kontroll av kommandot. Manipulera konfiguration eller inbyggd programvara uppdateringspaket (är avsedd för enheter eller fält-gateway) kan medan cachelagras eller i kö lokalt leda till OS och/eller system komponenter äventyras |BitLocker |
| Fältet Gateway OS-avbildning |TRID | |Manipulera OS / ersätta OS-komponenter |OS-partition är skrivskyddad, loggat OS-avbildning, kryptering |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Enhets- och event bearbetning/i molnet gateway zon

En molngateway är system som möjliggör kommunikation från och till enheter eller fält-gateways från flera olika platser i offentligt nätverksutrymme, vanligtvis mot en molnbaserad kontroll och data analysis-system, en federation på sådana system. I vissa fall kan kan en molngateway omedelbart underlättar åtkomst till enheter för särskilda ändamål från terminaler, till exempel surfplattor eller telefoner. I den kontext som beskrivs här, ”cloud” är avsedd att referera till en dedikerad databearbetning system som inte är bunden till på samma plats som de anslutna enheter eller fält-gateways och där operativa åtgärder förhindra riktade fysisk åtkomst men är inte nödvändigtvis till en ” infrastruktur för offentliga moln ”. En molngateway kan potentiellt mappas till ett nätverksvirtualisering överlägg till isolerar molngatewayen och alla dess anslutna enheter eller fält-gateways från annan nätverkstrafik. Molngatewayen själva är inte ett system för enheten eller en bearbetning eller lagringsutrymmet för enhetsdata; Dessa anläggningar gränssnitt med molngatewayen. Molnet gateway zonen innehåller själva molngatewayen tillsammans med alla fält-gateways och enheter som är direkt eller indirekt anslutna till den.

Molngatewayen är främst anpassade inbyggda del av programvaran som körs som en tjänst med exponerade slutpunkter som fält-gateway och enheter ansluta till. Det måste därför utformas med säkerhet i åtanke. Följ [SDL](https://www.microsoft.com/sdl) processer för att utforma och skapa den här tjänsten.

#### <a name="services-zone"></a>Tjänster-zon

Ett kontrollsystem (eller domänkontrollant) är en programvarulösning som har kontakt med en enhet eller en fält-gateway eller molngatewayen i syfte att kontrollera en eller flera enheter och/eller för att samla in och/eller lagra och/eller analysera enhetsdata för presentation eller efterföljande kontroll syften. Kontrollsystem finns endast enheter i omfånget för den här diskussionen som kan underlätta interaktion med personer direkt. Undantagen är mellanliggande fysisk kontroll ytor på enheter som en växel som gör att en person att Stäng av enheten eller ändra andra egenskaper och för vilket det finns ingen funktionell motsvarande som kan nås digitalt.

Mellanliggande fysisk kontroll ytor är sådana där reglerar logic avgränsar funktionen av fysiska yta så att en motsvarande funktion kan initieras via fjärranslutning eller indata är i konflikt med remote indata kan undvikas – till exempel intermediated kontrollen ytor är begreppsmässigt kopplade till ett lokalt system som använder samma underliggande funktioner som alla andra fjärrstyrning system som enheten kan kopplas till parallellt. Största hot till molnbaserad databehandling kan vara läsa vid från [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) sidan.

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

* [SDL Hotmodelleringsverktyg](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT-Referensarkitektur](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
