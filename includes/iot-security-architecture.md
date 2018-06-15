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
ms.openlocfilehash: 62856d4743d853d5685503b5c21faedc46575e55
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814806"
---
# <a name="internet-of-things-security-architecture"></a>Sakernas Internet-säkerhetsarkitekturen

När ett system utformas, är det viktigt att förstå potentiella hot på systemet och lägga till lämpliga försvar därför eftersom systemet är utformad och konstruerad. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom förstå hur en angripare kan vara att en dator gör att lämpliga åtgärder finns på plats från början.

## <a name="security-starts-with-a-threat-model"></a>Säkerhet som börjar med en hotmodell

Microsoft har länge använt hot modeller för sina produkter och gjort företagets hot modellera processen offentligt tillgängliga. Företagets erfarenhet visar att modellering har oväntat fördelar utöver den omedelbara förståelsen för vilka hot är den mest om. Till exempel skapar det också en väg för en öppen diskussion med andra utanför Utvecklingsteamet, vilket kan leda till nya idéer och förbättringar i produkten.

Syftet med hotmodellering är att förstå hur en angripare kan vara att en dator och kontrollera att lämpliga åtgärder finns på plats. Hot modellering framtvingar designgruppen för att överväga åtgärder som att systemet i stället för efter en distribueras. Detta är ytterst viktigt eftersom det är omöjligt att onlineåterställningspunkter säkerhetsskyddet på en mängd enheter på fältet, tillförlitligt och lämnar kvar kunder på risk.

Många utvecklingsgrupper göra en utmärkt jobb som avbildar funktionella krav för system som har nytta av kunder. Identifiera icke-uppenbara sätt att någon kan missbrukas systemet är dock mer utmanande. Hotmodellering hjälper dig att förstå vad en angripare kan göra utvecklingsgrupper och varför. Hotmodellering är en strukturerad process som skapar en diskussion om säkerheten designbeslut i systemet, samt ändringar av design som görs på vägen att påverkan säkerhet. En hotmodell är bara ett dokument, representerar den här dokumentationen också ett enkelt sätt att kontrollera kontinuitet i knowledge kvarhållning av erfarenheter lärt dig och hjälp nytt team komma igång snabbt. Slutligen är ett resultat av hotmodellering så att du kan överväga andra aspekter av säkerhet, till exempel vilka säkerhetsåtaganden som du vill ge dina kunder. Dessa åtaganden tillsammans med hotmodellering informera och enheten testning av din lösning för Sakernas Internet (IoT).

### <a name="when-to-threat-model"></a>När du ska hot modellen

[Hotmodellering](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) ger det största värdet när du inkludera den i designfasen. När du designar har störst flexibilitet att göra ändringar eliminera hot. Eliminera hot avsiktligt är önskat utfall. Det är mycket enklare än att lägga till åtgärder, testa dem och att de fortfarande är aktuella och dessutom sådan eliminering är inte alltid möjligt. Det blir svårare att eliminera hot som en produkt blir mer mogen och slutligen i sin tur kräver mer arbete och mycket svårare kompromisser än hot modeling tidigt i utvecklingen.

### <a name="what-to-threat-model"></a>Vad du hotmodell

Du bör hot modellen hela lösningen och även fokusera på följande områden:

* Funktioner för säkerhet och sekretess
* De funktioner som fel är relevanta säkerhet
* De funktioner som rör en förtroendegräns

### <a name="who-threat-models"></a>Som hot modeller

Hotmodellering är en process som någon annan. Det är en bra idé att behandla modelldokument hot som alla andra komponenter i lösningen och verifiera den. Många utvecklingsgrupper göra en utmärkt jobb som avbildar funktionella krav för system som har nytta av kunder. Identifiera icke-uppenbara sätt att någon kan missbrukas systemet är dock mer utmanande. Hotmodellering hjälper dig att förstå vad en angripare kan göra utvecklingsgrupper och varför.

### <a name="how-to-threat-model"></a>Så här hotmodell

Hotet modellera processen består av fyra steg; stegen är:

* Modellen programmet
* Räkna upp hot
* Risken för hot
* Validera åtgärder

#### <a name="the-process-steps"></a>I steg

Tre tumregel att tänka på när du skapar en hotmodell:

1. Skapa ett diagram utanför Referensarkitektur.
1. Starta första bredd. Få en översikt och förstå systemet som helhet innan du dyker djup. Den här metoden kan du kontrollera att du tittar på rätt plats.
1. Enheten processen, låt inte processen enhet du. Om du hittar ett problem i fasen modellering och vill utforska den går för den! Tycker att du inte behöver utföra dessa steg slavishly.

#### <a name="threats"></a>Hot

De fyra Kärnelementen i en hotmodell är:

* Processer som webbtjänster, Win32-tjänster och * nix daemon. Vissa avancerade entiteter (till exempel fältet gateways och sensorer) kan representeras som en processen när tekniska gå nedåt i dessa områden inte är möjlig.
* Datalager (var som helst data lagras, till exempel en konfigurationsfil eller databas)
* Dataflöde (där data flyttas mellan olika element i programmet)
* Externa enheter (något som interagerar med systemet, men som inte kontrolleras av programmet, exempel innehåller användare och satellit feeds)

Alla element i Arkitekturdiagram regleras hot; den här artikeln STRIDE mnemonik. Läs [hot Modeling igen, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) vill veta mer om STRIDE-element.

Olika element i diagrammet programmet regleras vissa STRIDE hot:

* Processer som omfattas av STRIDE
* Dataflöden regleras TID
* Datalager regleras TID, och ibland R, när datalager loggfiler.
* Externa enheter regleras SRD

## <a name="security-in-iot"></a>Säkerhet i IoT

Den anslutna särskilda enheter har ett stort antal potentiella interaktion ytan områden och interaktion mönster, som måste beaktas för att tillhandahålla ett ramverk för att skydda digital åtkomst till dessa enheter. Termen ”digitala åtkomst” används här att skilja från alla åtgärder som utförs via direkt enhetsinteraktion där åtkomstsäkerhet tillhandahålls via fysiska åtkomstkontroll. Till exempel placera enheten i en plats med ett lås på dörren. Även om fysisk åtkomst kan nekas med hjälp av programvara och maskinvara, vidtas åtgärder för att förhindra fysisk åtkomst leder till system störningar.

Att utforska interaktion mönstren kan du titta på ”enhetskontroll” och ”enhetsdata” med samma nivå av uppmärksamhet. ”Enhetskontroll” kan klassificeras som all information som har angetts för en enhet av part med målet att ändra eller påverka sitt beteende mot dess tillstånd eller tillståndet för sin miljö. ”Enhetsdata” kan klassificeras som all information som en enhet genererar en annan part om dess tillstånd och observerade tillståndet för sin miljö.

För att optimera säkerhetsmetoder, rekommenderas det att en typisk IoT-arkitekturen är uppdelad i flera komponent-zoner som en del av hotet modeling övningen. Dessa zoner beskrivs fullständigt i det här avsnittet och inkluderar:

* Enheten
* Fältet Gateway
* Molnet gateways, och
* Tjänster.

Zoner är breda sätt att segmentera en lösning. varje zon har ofta krav data och autentisering och auktorisering. Zoner kan också användas för att skada isolering och begränsa effekten av låg förtroende zoner på högre förtroende zoner.

Varje zon skiljs åt av en förtroende gräns som anges som prickad röd linje i följande diagram. Representerar en övergång av data/information från en källa till en annan. Under den här övergången kan informationen vara föremål Spoofing, Tampering, Repudiation, avslöjande av Information, Denial of Service och höjning av privilegier (STRIDE).

![IoT-säkerhetszoner](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De komponenter som beskrivs i varje gräns genomgår också STRIDE som aktiverar en fullständig 360 hot modeling vy av lösningen. I följande avsnitt utveckla var och en av komponenterna och specifika säkerhetsfrågor och lösningar som ska införas.

I följande avsnitt beskrivs standard komponenter som vanligtvis finns i dessa zoner.

### <a name="the-device-zone"></a>Zonen enhet

Enheten miljön är omedelbar fysiskt utrymme runt enheten där fysisk åtkomst och/eller ”lokala nätverk” peer-to-peer digitala åtkomst till enheten är möjligt. ”Lokala nätverk” antas vara ett nätverk som är separat och isolerade från – men potentiellt bryggade till – det offentliga Internet och innehåller alla kort Håll trådlösa alternativknapp-teknik som möjliggör peer-to-peer-kommunikation av enheter. Det gör *inte* omfattar alla nätverk virtualiseringsteknik skapar illusionen av ett lokalt nätverk och det också omfattar inte offentlig operatör nätverk som kräver att alla enheter som har två kan kommunicera över offentligt nätverk utrymme om de anger en relation för peer-to-peer-kommunikation.

### <a name="the-field-gateway-zone"></a>Zonen fältet Gateway

Fältet gateway är en enhet/installation eller vissa generell server-programvara som fungerar som kommunikation aktiveraren och eventuella som ett system för enheten och enheten databearbetning hubb. Zonen fältet gateway innehåller fältet gatewayen och alla enheter som är kopplade till den. Som namnet antyder fältet gateways fungerar utanför dedikerade databearbetning verksamhet, är vanligtvis plats bunden, regleras potentiellt fysiska intrång och har begränsad operativa redundans. Alla när du säger att en gateway för fältet är ofta en sak en touch och sabotera samtidigt som du vet vad dess funktion är.

En gateway för fältet skiljer sig från en router för enbart trafik i att den har en aktiv roll vid hantering av åtkomst som informationsflödet, vilket innebär att det är ett program åtgärdas entiteten och nätverksanslutning eller session terminal. En NAT-enhet eller brandvägg däremot inte uppfyller kraven som fältet gateways eftersom de inte är explicit anslutning eller session terminaler, men i stället en väg (eller block) anslutningar eller sessioner görs via dem. Fältet gateway har två olika områden i ytan. En står de enheter som är kopplade till den och representerar för zonen och den andra står alla externa parter och är i utkanten av zonen.

### <a name="the-cloud-gateway-zone"></a>Zonen molnet gateway

Molngatewayen är ett system som möjliggör kommunikation från och till enheter eller gateways för fältet från flera olika platser över offentligt nätverk utrymme, vanligtvis mot en molnbaserad kontroll- och system för analys, ett federationsförtroende på sådana system. I vissa fall kan underlätta en molngateway omedelbart åtkomst till särskilda enheter från terminaler, till exempel surfplattor eller telefoner. ”Moln” är avsedd att referera till en dedikerad databearbetning system som inte har bundits till samma plats som anslutna enheter eller gateways för fältet i kontexten som beskrivs här. Också förhindra riktade fysisk åtkomst operativa åtgärder i en zon för molnet och exponeras inte nödvändigtvis att en ”offentliga” molninfrastruktur.  

En molngateway kan potentiellt mappas till ett nätverksvirtualisering överlägg till certifikatutfärdarhierarki gateway för moln och alla dess anslutna enheter eller gateways för fältet från annan nätverkstrafik. Molngatewayen är inte ett system för enheten eller en bearbetning eller lagringsutrymmet för enhetsdata; Dessa anläggningar gränssnittet med molngateway. Zonen molnet gateway innehåller själva molngatewayen tillsammans med alla fält gatewayer och enheter som är direkt eller indirekt kopplade till den. Kanten på zonen är en distinkta ytan där alla externa parter kommunicerar via.

### <a name="the-services-zone"></a>Zonen tjänster

”Tjänst” har definierats för den här kontexten som en programvarukomponent eller modul som samverkar med enheter via en gateway fältet eller molnet för insamling och analys, samt för kommandot och kontroll. Tjänster är medlare. De fungerar under sin identitet mot gateways och andra undersystem, lagra och analysera data, utfärda självständigt kommandon till enheter baserat på datainsikter eller scheman och avslöja information och styra funktioner till behöriga användare.

### <a name="information-devices-versus-special-purpose-devices"></a>Information-enheter kontra särskilda enheter

Datorer, telefoner och surfplattor är främst interaktiv information enheter. Telefoner och surfplattor är uttryckligen optimerade runt maximera batteri livslängd. De helst inaktivera delvis när inte omedelbart interagerar med en person eller när de inte tillhandahåller tjänster som spela musik eller styra sin ägare till en viss plats. Ur system fungerar enheterna information technology huvudsakligen som proxy för personer. De är ”personer motstånd” föreslå ”personer sensorer” samla in indata och åtgärder.

Särskilda enheter, är från enkla temperatursensorer till komplexa factory produktion rader med tusentals komponenter i dem, olika. Dessa enheter är mycket mer begränsade i syfte och även om de tillhandahåller vissa användargränssnittet i hög grad är begränsade till fingrarna med eller integreras i tillgångar i den fysiska världen. De mått och rapportera miljömässiga omständigheter, aktivera ventiler, styra servos, ljud larm, Växla ljus och utföra andra uppgifter. De bidrar till att den fungerar som en information-enhet är antingen för Allmänt för dyr, för stort eller för sprödbrott. Konkret syftet avgör omedelbart sina tekniska designen som också tillgängliga monetära budgeten för produktions- och schemalagda livstid igen. Kombinationen av dessa två viktiga faktorer begränsar tillgängliga operativa energi budget fysiska storleken och därmed tillgängligt lagringsutrymme beräknings- och säkerhetsfunktioner.

Om något ”går fel” automatiserad eller fjärråtkomst kan kontrolleras enheter, till exempel fel fysiska fel eller logik willful obehöriga intrång och manipulation. Produktion många kan förstöras, byggnader kan looted eller bränt och personer kan vara skadade eller även die. Det här är en helt annan klass skadan än någon ökar ett stulna kreditkort gränsen. Säkerhetsfältet för enheter som sätt att flytta och för sensordata som slutligen leder kommandon som orsakar saker att flytta, måste vara högre än i e-handel eller banktjänstscenario.

### <a name="device-control-and-device-data-interactions"></a>Kontroll av enhet och enheten data interaktioner

Den anslutna särskilda enheter har ett stort antal potentiella interaktion ytan områden och interaktion mönster, som måste beaktas för att tillhandahålla ett ramverk för att skydda digital åtkomst till dessa enheter. Termen ”digitala åtkomst” används här att skilja från alla åtgärder som utförs via direkt enhetsinteraktion där åtkomstsäkerhet tillhandahålls via fysiska åtkomstkontroll. Till exempel placera enheten i en plats med ett lås på dörren. Även om fysisk åtkomst kan nekas med hjälp av programvara och maskinvara, vidtas åtgärder för att förhindra fysisk åtkomst leder till system störningar.

Att utforska interaktion mönster titta på ”enhetskontroll” och ”enhetsdata” med samma nivå av kontrolleras när hotmodellering. ”Enhetskontroll” kan klassificeras som all information som har angetts för en enhet av part med målet att ändra eller påverka sitt beteende mot dess tillstånd eller tillståndet för sin miljö. ”Enhetsdata” kan klassificeras som all information som en enhet genererar en annan part om dess tillstånd och observerade tillståndet för sin miljö.

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>Hot modeling Azure IoT-Referensarkitektur

Microsoft använder framework beskrivs tidigare gör hot modellering för Azure IoT. I följande avsnitt används konkreta exempel på Azure IoT-Referensarkitektur för att demonstrera hur du tycker om hot modellering för IoT och åtgärda hot som identifieras. Det här exemplet identifierar fyra huvudsakliga områden i fokus:

* Enheter och datakällor
* Datatransport
* Enheten och händelsebearbetning, och
* Presentation

![Hot modellering för Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Följande diagram ger en förenklad vy av Microsofts IoT-arkitekturen med hjälp av en modell för Flödesdiagram för Data som används av hot Modeling-verktyget:

![Hot modellering för Azure IoT verktyget MS hot modellering](media/iot-security-architecture/iot-security-architecture-fig3.png)

Det är viktigt att notera att arkitekturen avgränsar enheter och gateway-funktioner. Den här metoden gör att användaren kan utnyttja gatewayenheter som är säkrare: de är kan kommunicera med molngatewayen med säkra protokoll, som kräver normalt större bearbetning omkostnader som en ursprunglig enhet – till exempel en termostat - kunde Ange egen. Anta att Molngatewayen representeras av tjänsten Azure IoT-hubb i zonen Azure-tjänster.

### <a name="device-and-data-sourcesdata-transport"></a>Transport-enheten och data källor/data

Det här avsnittet behandlar arkitekturen beskrivs tidigare via för hotmodellering och ger en översikt över hur du kan lösa vissa problem som inbyggd. Det här exemplet fokuserar på grundelementen i en hotmodell:

* Processer (både under din kontroll och externa objekt)
* Kommunikation (kallas även dataflöden)
* Lagring (kallas även för datalager)

#### <a name="processes"></a>Processer

Var och en av de kategorier som beskrivs i Azure IoT-arkitekturen i det här exemplet försöker åtgärda ett antal olika hot mellan de olika stegen/informationen finns i: processen, kommunikation och lagring. Följande är en översikt över vanligaste för kategorin ”process” följt av en översikt över hur dessa hot kan undvikas bäst:

**(S)-förfalskning**: en angripare kan extrahera kryptografiska nyckelmaterial från en enhet, antingen på nivån programvara eller maskinvara och sedan komma åt systemet med en annan fysisk eller virtuell enhet under identitet på enheten som nyckelmaterialet har tagits från. En bra bild är fjärransluten kontroller som kan aktivera valfri TV och som är populära prankster verktyg.

**Denial för tjänsten (D)**: en enhet kan återges inte klarar fungerar eller kommunikation med stör alternativknapp frekvenser eller skärande kablar. Övervakning kamera som hade ström eller nätverket anslutningen avsiktligt blockerade rapportera inte exempelvis data, alls.

**Manipulering (T)**: en angripare kan helt eller delvis ersätta program som körs på enheten kan vara så att ersatta programmet utnyttja äkta identiteten för enheten om nyckelmaterialet eller kryptografiska verksamhet hålla nyckeln Det fanns material olaglig programmet. Till exempel att en angripare kan utnyttja extraherade nyckelmaterial för att fånga och utelämna data från enheten på kommunikationssökvägen och Ersätt den med falska data som har verifierats med stulna nyckelmaterial.

**Avslöjande av information (I)**: om enheten kör behandlas för programvara, behandlas programvaran potentiellt kan läcka ut data till obehöriga personer. Till exempel att en angripare kan utnyttja extraherade nyckelmaterial för att mata in sig i kommunikationssökvägen mellan enheten och en domänkontrollant eller fältet gateway eller molngateway till siphon av information.

**Höjning av privilegier (E)**: en enhet som har en specifik funktion kan tvingas att göra något annat. En ventilen är programmerad att öppna halvvägs kan till exempel att öppna ända.

| **Komponent** | **Hot** | **Lösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhet |S |Tilldela identitet till enheten och autentisering av enheten |Ersätt enhet eller en del av enheten med en annan enhet. Hur vet du handlar på rätt enhet? |Autentisering av den enhet som använder Transport Layer Security (TLS) eller IPSec. Infrastrukturen ska ha stöd för med i förväg delad nyckel (PSK) på de enheter som inte kan hantera fullständig asymmetrisk kryptering. Använda Azure AD [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt) |
|| TRID |Tillämpa tamperproof mekanismer till enheten, till exempel genom att göra det svårt att det går inte att extrahera nycklar och andra kryptografiska material från enheten. |Risken är om någon är manipulation enheten (fysiska störningar). Hur vill du, enheten har inte ändrats. |Den mest effektiva begränsande faktorn är en betrodd platform module (TPM)-funktion som gör att lagra nycklar på särskilda-chip kretsar som nycklarna går inte att läsa, men kan endast användas för kryptografiska åtgärder som använder nyckeln men lämna aldrig ut nyckeln. Minne kryptering av enheten. Nyckelhantering för enheten. Koden för signering. | |
|| E |Med åtkomstkontroll på enheten. Auktoriseringsschema. |Om enheten kan för enskilda åtgärder som ska utföras baserat på kommandon från en extern källa eller även avslöjade sensorer kan angrepp att utföra åtgärder på ett annat tillgänglig. |Med auktoriseringsschema för enheten | |
| Fältet Gateway |S |Autentisera fältet gateway till Molngateway (t ex cert, PSK, eller anspråk baserade.) |Om någon kan imitera fältet Gateway att den sig själv som en enhet. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Samma nyckel lagring och attestering gäller för enheter i allmänt – bästa möjliga är använda TPM. 6LowPAN tillägget för IPSec att stödja trådlösa Sensor nätverk (WSN). |
|| TRID |Skydda fältet Gateway mot manipulation (TPM)? |Bedrägeri som lura molnet gateway-tänka pratar till fältet gateway kan leda till avslöjande av information och data manipulation |Minne-kryptering, TPM'S, autentisering. | |
|| E |Åtkomstkontroll för fältet Gateway | | | |

Här följer några exempel på hot i den här kategorin:

Förfalskning: Kan en angripare extrahera kryptografiska nyckelmaterial från en enhet, antingen på programvara eller maskinvara och därefter åtkomst system med en annan fysisk eller virtuell enhet under identitet på enheten som nyckelmaterialet har hämtats från.

**DOS-attack**: en enhet kan återges inte klarar fungerar eller kommunikation med stör alternativknapp frekvenser eller skärande kablar. Övervakning kamera som hade ström eller nätverket anslutningen avsiktligt blockerade rapportera inte exempelvis data, alls.

**Manipulering**: en angripare kan helt eller delvis ersätta program som körs på enheten kan vara så att ersatta programmet utnyttja äkta identiteten för enheten om nyckelmaterialet eller kryptografiska verksamhet hålla nyckeln Det fanns material olaglig programmet.

**Manipulering**: kamera övervakning som visas en synliga spektrumet bild av ett tomt Hall kunde syftar på ett foto av sådana Hall. En rök eller brand sensor kunde reporting någon hålla en ljusare under den. I båda fallen enheten kan vara tekniskt fullständigt betrodd mot systemet, men den rapporterar behandlas för information.

**Manipulering**: en angripare kan utnyttja extraherade nyckelmaterial för att fånga och utelämna data från enheten på kommunikationssökvägen och Ersätt den med falska data som har verifierats med stulna nyckelmaterial.

**Manipulering**: en angripare kan helt eller delvis ersätta program som körs på enheten kan vara så att programmet ersatta utnyttja äkta identiteten för enheten om nyckelmaterialet eller kryptografiska verksamhet hålla Det fanns viktiga material olaglig programmet.

**Avslöjande av information**: om enheten kör behandlas för programvara, behandlas programvaran potentiellt kan läcka ut data till obehöriga personer.

**Avslöjande av information**: en angripare kan utnyttja extraherade nyckelmaterial för att mata in sig i kommunikationssökvägen mellan gateway-enhet och en domänkontrollant eller ett fält eller molngateway till siphon av information.

**DOS-attack**: enheten kan stängas av eller aktiverat i ett läge där kommunikation är möjlig (vilket är avsiktlig i många industriella datorer).

**Manipulering**: enheten kan konfigureras för att fungera i ett tillstånd som är okända för kontrollsystem (utanför kända kalibreringsparametrar) och därför ger data som kan tolkas

**Rättighetsökning**: en enhet som har en specifik funktion kan tvingas att göra något annat. En ventilen är programmerad att öppna halvvägs kan till exempel att öppna ända.

**DOS-attack**: enheten kan omvandlas till ett tillstånd där kommunikation är möjlig.

**Manipulering**: enheten kan konfigureras för att fungera i ett tillstånd som är okända för kontrollsystem (utanför kända kalibreringsparametrar) och därför ger data som kan tolkas.

**Förfalskning/Tampering/Repudiation**: om inte skyddade (vilket är sällan fallet med consumer fjärr-kontroller), en angripare kan ändra tillståndet för en enhet anonymt. En bra bild är fjärransluten kontroller som kan aktivera valfri TV och som är populära prankster verktyg.

#### <a name="communication"></a>Kommunikation

Hot runt kommunikationssökvägen mellan enheter, enheter och fältet gateways och gateway-enhet och molnet. I följande tabell innehåller vägledning runt öppna sockets på enheten/VPN:

| **Komponent** | **Hot** | **Lösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enheten IoT-hubb |TID |(D) TLS (PSK/RSA) att kryptera trafiken |Avlyssning eller stör kommunikationen mellan enheten och gateway |Säkerhet på protokollnivå. Du måste ta reda på hur du skyddar dem med anpassade protokoll. I de flesta fall sker kommunikationen från enheten till IoT-hubben (enheten inleder anslutningen). |
| Enheten enhet |TID |(D) TLS (PSK/RSA) att kryptera trafiken. |Läsningen av data som överförs mellan enheter. Manipulation av data. Överbelastning enheten med nya anslutningar |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP. Du måste ta reda på hur du skyddar dem med anpassade protokoll. Lösning för DoS-hot är att peer-enheter via en moln- eller gateway och har endast act som klienter mot nätverket. Peering kan resultera i en direkt anslutning mellan peer-datorer efter att ha varit asynkrona av gateway |
| Extern enhet enhet |TID |Stark länkning av den externa enheten till enheten |Avlyssning anslutningen till enheten. Stör enheten kommunikationen |Länkning av den externa enheten till enheten NFC/Bluetooth LE på ett säkert sätt. Kontrollera den operativa panelen på enheten (fysiska) |
| Fältet Gateway Cloud Gateway |TID |TLS (PSK/RSA) att kryptera trafiken. |Avlyssning eller stör kommunikationen mellan enheten och gateway |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP). Du måste ta reda på hur du skyddar dem med anpassade protokoll. |
| Gateway för enhet moln |TID |TLS (PSK/RSA) att kryptera trafiken. |Avlyssning eller stör kommunikationen mellan enheten och gateway |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP). Du måste ta reda på hur du skyddar dem med anpassade protokoll. |

Här följer några exempel på hot i den här kategorin:

**DOS-attack**: begränsad enheter är vanligtvis under DoS hot om de aktivt lyssna efter inkommande anslutningar eller oönskad datagram i ett nätverk, eftersom en angripare inte kan öppna många anslutningar parallellt och tjänsten dem eller tjänst dem långsamt, eller enheten kan vara översvämmat med oönskad trafik. I båda fallen måste återges enheten effektivt inte fungerar alls i nätverket.

**Förfalskning, avslöjande av Information**: begränsad enheter och särskilda enheter har ofta en för alla säkerhet verksamhet som lösenord eller PIN-skydd eller de helt förlitar sig på betrodda nätverk, vilket innebär att de beviljar åtkomst till information om en enhet i samma nätverk och nätverket är ofta endast skyddas av en delad nyckel. Det innebär att när den delade hemligheten enhet eller nätverket lämnas, är det möjligt att styra enheten eller se data som sänds från enheten.  

**Förfalskning**: en angripare kan fånga upp eller delvis åsidosätta sändningen och förfalska avsändaren (man i mitten)

**Manipulering**: en angripare kan fånga upp eller delvis åsidosätta sändningen och skicka falsk information 

**Avslöjande av information:** en angripare kan eavesdrop på en sändning och få information utan tillstånd **Denial of Service:** en angripare kan sylt broadcast signalen och neka information distribution

#### <a name="storage"></a>Storage

Varje enhet och fältet gateway har någon form av lagring (tillfälliga för queuing data, operativsystem (OS) bildlagring).

| **Komponent** | **Hot** | **Lösning** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhetslagring |TRID |Lagringskryptering signering loggarna |Läsning av data från lagring (personligt identifierbar information data), manipulera telemetridata. Manipulera i kö eller cachelagrad kommandot kontrolldata. Manipulera konfiguration eller inbyggd programvara uppdateringspaket kan medan cachelagras eller köade lokalt leda till OS-och/eller system komponenter komprometteras |Kryptering, message authentication code (MAC) eller digital signatur. Möjligt, starkt åtkomstkontroll via resursåtkomst styra där listor (ACL) eller behörigheter. |
| Enhetens OS-bild |TRID | |Manipulera OS / ersätta OS-komponenter |OS-partitionen är skrivskyddad, signerade OS-avbildning, kryptering |
| Fältet Gateway-lagring (queuing data) |TRID |Lagringskryptering signering loggarna |Läsning av data från lagring (personligt identifierbar information data), manipulera telemetridata, manipulera i kö eller cachelagrad kommandot kontrolldata. Manipulera konfiguration eller inbyggd programvara uppdateringspaket (avsedda för enheter eller fältet gateway) kan medan cachelagras eller köade lokalt leda till OS-och/eller system komponenter komprometteras |BitLocker |
| Fältet Gateway OS-avbildningen |TRID | |Manipulera OS / ersätta OS-komponenter |OS-partitionen är skrivskyddad, signerade OS-avbildning, kryptering |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Enheten och händelsen bearbetning eller ett moln gateway zon

En molngateway är system som aktiverar fjärrkommunikation från och till enheter eller gateways för fältet från flera olika platser över offentligt nätverk utrymme, vanligtvis mot en molnbaserad kontroll- och system för analys, ett federationsförtroende på sådana system. I vissa fall kan underlätta en molngateway omedelbart åtkomst till särskilda enheter från terminaler, till exempel surfplattor eller telefoner. Kontexten som beskrivs här ”moln” är avsedd att referera till en dedikerad databearbetning system som inte har bundits till samma plats som den anslutna enheter eller fältet gateways och där operativa åtgärder för att förhindra fysisk åtkomst som mål men inte nödvändigtvis att en ”offentliga” molninfrastruktur. En molngateway kan potentiellt mappas till ett nätverksvirtualisering överlägg till certifikatutfärdarhierarki gateway för moln och alla dess anslutna enheter eller gateways för fältet från annan nätverkstrafik. Molngatewayen är inte ett system för enheten eller en bearbetning eller lagringsutrymmet för enhetsdata; Dessa anläggningar gränssnittet med molngateway. Zonen molnet gateway innehåller själva molngatewayen tillsammans med alla fält gatewayer och enheter som är direkt eller indirekt kopplade till den.

Molngatewayen är främst anpassade inbyggda program som körs som en tjänst med exponerade slutpunkter som fältet gateway och enheter att ansluta. Det måste därför utformas med säkerhet i åtanke. Följ [SDL](http://www.microsoft.com/sdl) processer för att designa och skapa den här tjänsten.

#### <a name="services-zone"></a>Zonen för tjänster

Kontrollsystem (eller domänkontrollant) är en programvarulösning som har kontakt med en enhet eller en fältet gateway eller molngateway för att styra en eller flera enheter och/eller för att samla in och/eller lagra och analysera data på enheten för presentation eller efterföljande kontroll syften. System är endast enheter i omfånget för den här diskussionen som kan underlätta interaktion med personer omedelbart. Undantagen är mellanliggande fysisk kontroll på portalen innehåller på enheter som en växel som gör att en person för att stänga av enheten eller ändra andra egenskaper och där det inte finns ingen funktionell ekvivalent som kan nås digitalt.

Mellanliggande fysisk kontroll på portalen innehåller är sådana där styr logik avgränsar funktionen av fysiska yta så att en motsvarande funktion kan initieras via fjärranslutning eller inkommande står i konflikt med fjärråtkomst indata kan undvikas – exempel intermediated kontrollen hämtar bifogas begreppsmässigt till ett lokalt system som använder samma underliggande funktioner som alla andra fjärrstyrning system som enheten kan kopplas till parallellt. Övre hot mot cloud computing-lösningar kan läsas på [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/) sidan.

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

* [SDL hot modellering verktyget](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT Referensarkitektur](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
