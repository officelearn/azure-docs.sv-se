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
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034554"
---
När man utformar ett system är det viktigt att förstå de potentiella hoten mot det systemet, och lägga till lämpliga försvar därefter, eftersom systemet är utformat och utformat. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom förstå hur en angripare kan äventyra ett system hjälper till att se till att lämpliga mildrande åtgärder är på plats från början.

## <a name="security-starts-with-a-threat-model"></a>Säkerhet börjar med en hotmodell

Microsoft har länge använt hotmodeller för sina produkter och har gjort företagets hotmodelleringsprocess allmänt tillgänglig. Företagets erfarenhet visar att modelleringen har oväntade fördelar utöver den omedelbara förståelsen för vilka hot som är mest oroande. Till exempel skapar det också en väg för en öppen diskussion med andra utanför utvecklingsteamet, vilket kan leda till nya idéer och förbättringar i produkten.

Syftet med hotmodellering är att förstå hur en angripare kan kompromettera ett system och sedan se till att lämpliga åtgärder finns på plats. Hotmodellering tvingar designteamet att överväga mildrande åtgärder eftersom systemet är utformat i stället för när ett system har distribuerats. Detta faktum är oerhört viktigt, eftersom eftermontering säkerhet försvar till en myriad av enheter i området är omöjligt, felbenägna och lämnar kunder i riskzonen.

Många utvecklingsteam gör ett utmärkt jobb med att fånga upp de funktionella kraven för det system som gynnar kunderna. Men att identifiera icke-uppenbara sätt att någon kan missbruka systemet är mer utmanande. Hotmodellering kan hjälpa utvecklingsteam att förstå vad en angripare kan göra och varför. Hotmodellering är en strukturerad process som skapar en diskussion om säkerhetsdesignbeslut i systemet, samt ändringar i designen som görs längs vägen som påverkar säkerheten. Medan en hotmodell helt enkelt är ett dokument, utgör den här dokumentationen också ett idealiskt sätt att säkerställa kontinuitet i kunskap, behålla lärdomar och hjälpa nya team ombord snabbt. Slutligen är ett resultat av hotmodellering som gör att du kan överväga andra aspekter av säkerhet, till exempel vilka säkerhetsåtaganden du vill ge dina kunder. Dessa åtaganden i samband med hot modellering informera och driva testning av din Sakernas Internet (IoT) lösning.

### <a name="when-to-do-threat-modeling"></a>När ska hotmodellering

[Hot modellering](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) erbjuder det största värdet när du införlivar den i designfasen. När du utformar har du störst flexibilitet att göra ändringar för att eliminera hot. Eliminera hot genom design är det önskade resultatet. Det är mycket lättare än att lägga till mildrande åtgärder, testa dem och se till att de förblir aktuella och dessutom är en sådan eliminering inte alltid möjligt. Det blir svårare att eliminera hot som en produkt blir mer mogen, och i sin tur i slutändan kräver mer arbete och mycket svårare kompromisser än hot modellering tidigt i utvecklingen.

### <a name="what-to-consider-for-threat-modeling"></a>Att tänka på för hot modellering

Du bör titta på lösningen som helhet och även fokusera på följande områden:

* Säkerhets- och sekretessfunktionerna
* De funktioner vars fel är säkerhetsrelevanta
* De funktioner som berör en förtroendegräns

### <a name="who-performs-threat-modeling"></a>Vem utför hotmodellering

Hot modellering är en process som alla andra. Det är en bra idé att behandla hotmodelldokumentet som alla andra komponenter i lösningen och validera det. Många utvecklingsteam gör ett utmärkt jobb med att fånga upp de funktionella kraven för det system som gynnar kunderna. Men att identifiera icke-uppenbara sätt att någon kan missbruka systemet är mer utmanande. Hotmodellering kan hjälpa utvecklingsteam att förstå vad en angripare kan göra och varför.

### <a name="how-to-perform-threat-modeling"></a>Så här utför du hotmodellering

Hotmodelleringsprocessen består av fyra steg. stegen är:

* Modellera programmet
* Räkna upp hot
* Minimera hot
* Validera mildrande åtgärder

#### <a name="the-process-steps"></a>Processstegen

Tre tumregler att tänka på när man bygger en hotmodell:

1. Skapa ett diagram av referensarkitektur.

2. Börja vidden först. Få en överblick och förstå systemet som helhet innan djupdykning. Detta tillvägagångssätt hjälper till att säkerställa att du djupdykning på rätt ställen.

3. Kör processen, låt inte processen köra dig. Om du hittar ett problem i modelleringsfasen och vill utforska det, gå för det! Känn inte att du behöver följa dessa steg slaviskt.

#### <a name="threats"></a>Hot

De fyra centrala delarna av en hotmodell är:

* Processer som webbtjänster, Win32-tjänster och *nix-demoner. Vissa komplexa entiteter (till exempel fältgateways och sensorer) kan abstraheras som en process när en teknisk detaljgranskning i dessa områden inte är möjlig.

* Datalager (var som helst lagras data, till exempel en konfigurationsfil eller databas)

* Dataflöde (där data flyttas mellan andra element i programmet)

* Externa entiteter (allt som interagerar med systemet, men som inte kontrolleras av programmet, exempel inkluderar användare och satellitflöden)

Alla element i det arkitektoniska diagrammet är föremål för olika hot; denna artikel STRIDE mnemonic. Läs [Hot Modellering igen, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) att veta mer om STRIDE element.

Olika delar av programdiagrammet är föremål för vissa STRIDE-hot:

* Processerna är föremål för STRIDE
* Dataflöden är föremål för TID
* Datalager är föremål för TID, och ibland R, när datalager är loggfiler.
* Externa enheter omfattas av SRD

## <a name="security-in-iot"></a>Säkerhet i IoT

Anslutna specialenheter har ett betydande antal potentiella interaktionsytor och interaktionsmönster, som alla måste anses utgöra ett ramverk för att säkerställa digital åtkomst till dessa enheter. Termen "digital åtkomst" används här för att skilja från alla åtgärder som utförs genom direkt enhetsinteraktion där åtkomstsäkerhet tillhandahålls genom fysisk åtkomstkontroll. Till exempel att sätta enheten i ett rum med ett lås på dörren. Fysisk åtkomst kan inte nekas med hjälp av programvara och maskinvara, men åtgärder kan vidtas för att förhindra fysisk åtkomst från att leda till systemstörningar.

När du utforskar interaktionsmönstren kan du titta på "enhetskontroll" och "enhetsdata" med samma uppmärksamhetsnivå. "Enhetskontroll" kan klassificeras som all information som tillhandahålls en enhet av någon part med målet att ändra eller påverka dess beteende mot dess tillstånd eller tillståndet i dess miljö. "Enhetsdata" kan klassificeras som all information som en enhet avger till någon annan part om dess tillstånd och det observerade tillståndet i dess miljö.

För att optimera bästa säkerhetspraxis rekommenderar vi att en typisk IoT-arkitektur är uppdelad i flera komponenter/zoner som en del av hotmodelleringsövningen. Dessa zoner beskrivs helt i hela detta avsnitt och inkluderar:

* Enhet
* Fältgateway,
* Molngateways och
* Tjänster.

Zoner är ett brett sätt att segmentera en lösning. varje zon har ofta sina egna data- och autentiserings- och auktoriseringskrav. Zoner kan också användas för att isolera skador och begränsa effekterna av lågförtroendezoner på högre förtroendezoner.

Varje zon avgränsas med en förtroendegräns, som noteras som den prickade röda linjen i följande diagram. Det representerar en övergång av data/information från en källa till en annan. Under den här övergången kan data/information bli föremål för Spoofing, Manipulering, avståndstagande, informationsavslöjande, denial of service och elevation of privilege (STRIDE).

![IoT-säkerhetszoner](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De komponenter som avbildas inom varje gräns utsätts också för STRIDE, vilket möjliggör en fullständig 360 hotmodelleringsvy av lösningen. I följande avsnitt beskrivs var och en av komponenterna och särskilda säkerhetsproblem och lösningar som bör införas.

I följande avsnitt beskrivs standardkomponenter som vanligtvis finns i dessa zoner.

### <a name="the-device-zone"></a>Enhetszonen

Enhetsmiljön är det omedelbara fysiska utrymmet runt enheten där fysisk åtkomst och/eller "lokalt nätverk" peer-to-peer digital åtkomst till enheten är möjlig. Ett "lokalt nätverk" antas vara ett nätverk som är distinkt och isolerat från - men potentiellt överbryggas till - det offentliga Internet, och innehåller alla kortdistans trådlös radioteknik som tillåter peer-to-peer-kommunikation av enheter. Det omfattar *inte* någon nätverksvirtualiseringsteknik som skapar en illusion av ett sådant lokalt nätverk och det omfattar inte heller offentliga operatörsnätverk som kräver två enheter för att kommunicera över offentliga nätverk om de skulle gå in i en peer-to-peer-kommunikationsrelation.

### <a name="the-field-gateway-zone"></a>Fältgatewayzonen

Fältgateway är en enhet/apparat eller något allmänt serverdatorprogram som fungerar som kommunikationsaktiverare och, eventuellt, som ett enhetskontrollsystem och enhetsdatabehandlingsnav. Fältgatewayzonen innehåller själva fältgatewayen och alla enheter som är kopplade till den. Som namnet antyder fungerar fältgateways utanför dedikerade databehandlingsanläggningar, är vanligtvis platsbundna, kan vara föremål för fysiskt intrång och har begränsad operativ redundans. Allt för att säga att ett fält gateway är ofta en sak man kan röra och sabotera samtidigt veta vad dess funktion är.

En fältgateway skiljer sig från en vanlig trafikrouter genom att den har haft en aktiv roll i hanteringen av åtkomst- och informationsflöde, vilket innebär att det är en programadresserad entitet och nätverksanslutning eller sessionsterminal. En NAT-enhet eller -brandvägg betraktas däremot inte som fältgateways eftersom de inte är explicita anslutnings- eller sessionsterminaler, utan snarare en väganslutningar (eller blockera) anslutningar eller sessioner som görs via dem. Fältporten har två distinkta ytor. Den ena är vänd mot de enheter som är anslutna till den och representerar zonens insida, och den andra är vänd mot alla externa parter och är zonens kant.

### <a name="the-cloud-gateway-zone"></a>Zonen molngateway

En molngateway är ett system som möjliggör fjärrkommunikation från och till enheter eller fältgateways från flera olika platser i det offentliga nätverksutrymmet, vanligtvis mot ett molnbaserat kontroll- och dataanalyssystem, en federation av sådana system. I vissa fall kan en molngateway omedelbart underlätta åtkomsten till specialenheter från terminaler som surfplattor eller telefoner. I det sammanhang som diskuteras här är "moln" tänkt att referera till ett dedikerat databehandlingssystem som inte är bundet till samma plats som de anslutna enheterna eller fältgatewayerna. Även i en molnzon förhindrar operativa åtgärder riktad fysisk åtkomst och är inte nödvändigtvis exponerade för en "offentlig molninfrastruktur".  

En molngateway kan eventuellt mappas till ett nätverksvirtualiseringsöverlägg för att isolera molngatewayen och alla dess anslutna enheter eller fältgateways från annan nätverkstrafik. Molngatewayen i sig är inte ett enhetskontrollsystem eller en bearbetnings- eller lagringsanläggning för enhetsdata. dessa anläggningar gränssnitt med molnet gateway. Molngatewayzonen innehåller molngatewayen själv tillsammans med alla fältgateways och enheter som är direkt eller indirekt kopplade till den. Zonens kant är en avgränsad yta där alla externa parter kommunicerar igenom.

### <a name="the-services-zone"></a>Tjänstezonen

En "tjänst" definieras för den här kontexten som alla programvarukomponenter eller moduler som interagerar med enheter via en fält- eller molngateway för datainsamling och dataanalys, samt för kommando och kontroll. Tjänster är medlare. De agerar under sin identitet mot gateways och andra delsystem, lagrar och analyserar data, utfärdar autonomt kommandon till enheter baserat på datainsikter eller scheman och exponerar informations- och kontrollfunktioner för behöriga slutanvändare.

### <a name="information-devices-versus-special-purpose-devices"></a>Informationsenheter jämfört med specialenheter

Datorer, telefoner och surfplattor är i första hand interaktiva informationsenheter. Telefoner och surfplattor är uttryckligen optimerade för att maximera batteriets livslängd. De stänger helst av delvis när de inte omedelbart interagerar med en person, eller när de inte tillhandahåller tjänster som att spela musik eller vägleda sin ägare till en viss plats. Ur ett systemperspektiv fungerar dessa it-enheter huvudsakligen som proxyservrar gentemot människor. De är "människor ställdon" tyder på åtgärder och "människor sensorer" samla in input.

Specialenheter, från enkla temperatursensorer till komplexa fabriksproduktionslinjer med tusentals komponenter inuti dem, är olika. Dessa enheter är mycket mer begränsade i syfte och även om de ger vissa användargränssnitt, de är till stor del begränsade till gränssnitt med eller integreras i tillgångar i den fysiska världen. De mäter och rapporterar miljöförhållanden, vridventiler, styrservar, ljudlarm, brytare och utför många andra uppgifter. De hjälper till att göra arbete som en informationsenhet är antingen för generisk, för dyr, för stor eller för spröd. Det konkreta syftet dikterar omedelbart deras tekniska utformning samt den tillgängliga monetära budgeten för deras produktion och schemalagda livslängd drift. Kombinationen av dessa två nyckelfaktorer begränsar den tillgängliga driftenergibudgeten, det fysiska fotavtrycket och därmed tillgängliga lagrings-, beräknings- och säkerhetsfunktionerna.

Om något "går fel" med automatiserade eller fjärrstyrda enheter, till exempel, fysiska defekter eller kontroll logik defekter till uppsåtlig obehörig intrång och manipulation. Produktionen partier kan förstöras, byggnader kan plundras eller brännas ner, och människor kan skadas eller till och med dö. Detta är en helt annan klass av skador än någon maxing ut ett stulet kreditkort gräns. Säkerhetsfältet för enheter som får saker att röra sig, och även för sensordata som så småningom resulterar i kommandon som gör att saker och ting kan röra sig, måste vara högre än i någon e-handel eller bankscenario.

### <a name="device-control-and-device-data-interactions"></a>Enhetskontroll och enhetsdatainteraktioner

Anslutna specialenheter har ett betydande antal potentiella interaktionsytor och interaktionsmönster, som alla måste anses utgöra ett ramverk för att säkerställa digital åtkomst till dessa enheter. Termen "digital åtkomst" används här för att skilja från alla åtgärder som utförs genom direkt enhetsinteraktion där åtkomstsäkerhet tillhandahålls genom fysisk åtkomstkontroll. Till exempel att sätta enheten i ett rum med ett lås på dörren. Fysisk åtkomst kan inte nekas med hjälp av programvara och maskinvara, men åtgärder kan vidtas för att förhindra fysisk åtkomst från att leda till systemstörningar.

När du utforskar interaktionsmönstren kan du titta på "enhetskontroll" och "enhetsdata" med samma grad av uppmärksamhet när du utformar hot. "Enhetskontroll" kan klassificeras som all information som tillhandahålls en enhet av någon part med målet att ändra eller påverka dess beteende mot dess tillstånd eller tillståndet i dess miljö. "Enhetsdata" kan klassificeras som all information som en enhet avger till någon annan part om dess tillstånd och det observerade tillståndet i dess miljö.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Utföra hotmodellering för Azure IoT-referensarkitekturen

Microsoft använder det ramverk som beskrivits tidigare för att göra hotmodellering för Azure IoT. I följande avsnitt används det konkreta exemplet på Azure IoT Reference Architecture för att visa hur du tänker på hotmodellering för IoT och hur du åtgärdar de identifierade hoten. I det här exemplet identifieras fyra huvudområden:

* Enheter och datakällor.
* Datatransport,
* Enhets- och händelsebearbetning, och
* Presentation

![Hotmodellering för Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Följande diagram innehåller en förenklad vy av Microsofts IoT-arkitektur med hjälp av en dataflödesdiagrammodell som används av Microsoft Threat Modeling Tool:

![Hotmodellering för Azure IoT med ms-verktyg för hotmodellering](media/iot-security-architecture/iot-security-architecture-fig3.png)

Det är viktigt att notera att arkitekturen separerar enheten och gateway-funktionerna. Den här metoden gör det möjligt för användaren att utnyttja gateway-enheter som är säkrare: de kan kommunicera med molngatewayen med hjälp av säkra protokoll, vilket vanligtvis kräver större bearbetningskostnader som en inbyggd enhet - till exempel en termostat - kan tillhandahålla på egen hand. I zonen Azure-tjänster antar du att Cloud Gateway representeras av Azure IoT Hub-tjänsten.

### <a name="device-and-data-sourcesdata-transport"></a>Enhets- och datakällor/datatransport

I det här avsnittet undersöks arkitekturen som beskrivits tidigare genom den av riskmodellering och en översikt över hur du ska hantera några av de inneboende problemen. Det här exemplet fokuserar på de centrala delarna i en hotmodell:

* Processer (både under din kontroll och externa objekt)
* Kommunikation (kallas även dataflöden)
* Lagring (kallas även datalager)

#### <a name="processes"></a>Processer

I var och en av de kategorier som beskrivs i Azure IoT-arkitekturen försöker det här exemplet minska ett antal olika hot i de olika stadierna data/information finns i: process, kommunikation och lagring. Nedan följer en översikt över de vanligaste för kategorin "process", följt av en översikt över hur dessa hot bäst kan mildras:

**Spoofing (S)**: En angripare kan extrahera kryptografiskt nyckelmaterial från en enhet, antingen på programvaru- eller maskinvarunivå, och därefter komma åt systemet med en annan fysisk eller virtuell enhet under enhetens identitet som nyckelmaterialet har hämtats från. En bra illustration är fjärrkontroller som kan vända någon TV och som är populära upptågsmakare verktyg.

**Denial of Service (D)**: En enhet kan göras oförmögen att fungera eller kommunicera genom att störa radiofrekvenser eller skärtrådar. Till exempel kan en övervakningskamera som hade sin ström- eller nätverksanslutning avsiktligt utslagen inte rapportera data alls.

**Manipulering (T)**: En angripare kan helt eller delvis ersätta programvaran som körs på enheten, vilket kan göra det möjligt för den ersatta programvaran att utnyttja enhetens äkta identitet om nyckelmaterialet eller de kryptografiska anläggningar som innehåller nyckelmaterial var tillgängliga för det olagliga programmet. En angripare kan till exempel utnyttja extraherat nyckelmaterial för att fånga upp och undertrycka data från enheten på kommunikationssökvägen och ersätta det med falska data som autentiseras med det stulna nyckelmaterialet.

**Information Disclosure (I)**: Om enheten kör manipulerad programvara, kan sådan manipulerad programvara potentiellt läcka data till obehöriga parter. En angripare kan till exempel utnyttja extraherat nyckelmaterial för att injicera sig själv i kommunikationssökvägen mellan enheten och en styrenhet eller fältgateway eller molngateway för att suga bort information.

**Behörighetshöjd (E)**: En enhet som utför specifik funktion kan tvingas göra något annat. Till exempel kan en ventil som är programmerad att öppna halvvägs luras att öppna hela vägen.

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhet |S |Tilldela identitet till enheten och autentisera enheten |Byta enhet eller en del av enheten mot någon annan enhet. Hur vet du att du pratar med rätt enhet? |Autentisera enheten med hjälp av Transport Layer Security (TLS) eller IPSec. Infrastruktur bör stödja användning av fördelad nyckel (PSK) på de enheter som inte kan hantera fullständig asymmetrisk kryptografi. Utnyttja Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID (AV TRID) |Applicera manipuleringssäkra mekanismer på enheten, till exempel genom att göra det svårt att omöjligt att extrahera nycklar och annat kryptografiskt material från enheten. |Risken är om någon manipulerar enheten (fysisk störning). Hur är du säker på att enheten inte har manipulerats. |Den mest effektiva begränsningen är en betrodd plattformsmodul (TPM) kapacitet som gör det möjligt att lagra nycklar i speciella on-chip kretsar från vilka nycklarna inte kan läsas, men kan endast användas för kryptografiska operationer som använder nyckeln men aldrig avslöja nyckeln. Minneskryptering av enheten. Nyckelhantering för enheten. Signerar koden. |
|| E |Har åtkomstkontroll över enheten. Tillståndssystem. |Om enheten tillåter att enskilda åtgärder utförs baserat på kommandon från en extern källa, eller till och med komprometterade sensorer, gör det möjligt för attacken att utföra åtgärder som inte är på annat sätt tillgängliga. |Har tillståndsschema för enheten |
| Fältgateway |S |Autentisera fältgatewayen till Molngateway (t.ex. certbaserad, PSK eller Anspråksbaserad.) |Om någon kan förfalska Fältgateway kan den presentera sig som vilken enhet som helst. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Alla samma viktiga lagring och intyg oro för enheter i allmänhet - bästa fall är att använda TPM. 6LowPAN-tillägg för IPSec för att stödja trådlösa sensornätverk (WSN). |
|| TRID (AV TRID) |Skydda fältgatewayen mot manipulering (TPM?) |Spoofing attacker som lurar molnet gateway tro att det talar med fältet gateway kan resultera i utlämnande av information och data manipulering |Minneskryptering, TPM, autentisering. |
|| E |Åtkomstkontrollmekanism för Fältgateway | | |

Här är några exempel på hot i den här kategorin:

**Förfalskning:** En angripare kan extrahera kryptografiskt nyckelmaterial från en enhet, antingen på programvaru- eller maskinvarunivå, och därefter komma åt systemet med en annan fysisk eller virtuell enhet under enhetens identitet som nyckelmaterialet har hämtats från.

**Denial of Service**: En enhet kan göras oförmögen att fungera eller kommunicera genom att störa radiofrekvenser eller skärtrådar. Till exempel kan en övervakningskamera som hade sin ström- eller nätverksanslutning avsiktligt utslagen inte rapportera data alls.

**Manipulering**: En angripare kan helt eller delvis ersätta programvaran som körs på enheten, vilket kan göra det möjligt för den utbytta programvaran att utnyttja enhetens äkta identitet om nyckelmaterialet eller de kryptografiska anläggningar som innehåller nyckelmaterial var tillgängliga för det olagliga programmet.

**Manipulering:** En övervakningskamera som visar en synlig spektrumbild av en tom hall kan riktas mot ett fotografi av en sådan hall. En rök- eller brandsensor kan rapportera någon som håller en tändare under den. I båda fallen kan enheten vara tekniskt fullt tillförlitlig mot systemet, men den rapporterar manipulerad information.

**Manipulering:** En angripare kan utnyttja extraherat nyckelmaterial för att fånga upp och undertrycka data från enheten på kommunikationssökvägen och ersätta det med falska data som autentiseras med det stulna nyckelmaterialet.

**Manipulering**: En angripare kan helt eller delvis ersätta programvaran som körs på enheten, vilket kan göra det möjligt för den utbytta programvaran att utnyttja enhetens äkta identitet om nyckelmaterialet eller de kryptografiska anläggningar som innehåller nyckelmaterial var tillgängliga för det olagliga programmet.

**Information Disclosure**: Om enheten kör manipulerad programvara, sådan manipulerad programvara kan potentiellt läcka data till obehöriga parter.

**Information Disclosure**: En angripare kan utnyttja extraherat nyckelmaterial för att injicera sig själv i kommunikationsvägen mellan enheten och en styrenhet eller fältgateway eller molngateway för att suga bort information.

**Denial of Service**: Enheten kan stängas av eller förvandlas till ett läge där kommunikation inte är möjlig (vilket är avsiktligt i många industriella maskiner).

**Manipulering**: Enheten kan konfigureras om för att fungera i ett tillstånd som är okänt för styrsystemet (utanför kända kalibreringsparametrar) och därmed tillhandahålla data som kan misstolkas

**Behörighetshöjd**: En enhet som gör specifik funktion kan tvingas göra något annat. Till exempel kan en ventil som är programmerad att öppna halvvägs luras att öppna hela vägen.

**Denial of Service**: Enheten kan omvandlas till ett tillstånd där kommunikation inte är möjlig.

**Manipulering**: Enheten kan konfigureras om för att fungera i ett tillstånd som är okänt för styrsystemet (utanför kända kalibreringsparametrar) och därmed tillhandahålla data som kan misstolkas.

**Spoofing/Tampering/Repudiation**: Om den inte är säkrad (vilket sällan är fallet med konsumentfjärrkontroller) kan en angripare manipulera tillståndet för en enhet anonymt. En bra illustration är fjärrkontroller som kan vända någon TV och som är populära upptågsmakare verktyg.

#### <a name="communication"></a>Kommunikation

Hot kring kommunikationssökvägen mellan enheter, enheter och fältgatewayer samt enhets- och molngateway. Följande tabell har viss vägledning kring öppna uttag på enheten/VPN:

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Enhet IoT-hubb |TID ( TID ) |(D) TLS (PSK/RSA) för att kryptera trafiken |Tjuvlyssna eller störa kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivå. Med anpassade protokoll måste du ta reda på hur du skyddar dem. I de flesta fall sker kommunikationen från enheten till IoT Hub (enheten initierar anslutningen). |
| Enhet till enhet |TID ( TID ) |(D) TLS (PSK/RSA) för att kryptera trafiken. |Läsa data under överföring mellan enheter. Manipulering av data. Överbelasta enheten med nya anslutningar |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP. Med anpassade protokoll måste du ta reda på hur du skyddar dem. Begränsningen för DoS-hotet är att peer-enheter via ett moln eller en fältgateway och låta dem bara fungera som klienter mot nätverket. Peering kan resultera i en direkt koppling mellan peer-datorerna efter att ha förmedlats av gatewayen |
| Extern entitetsenhet |TID ( TID ) |Stark parkoppling av den externa enheten till enheten |Tjuvlyssnar anslutningen till enheten. Störa kommunikationen med enheten |Koppla den externa enheten till enheten NFC/Bluetooth LE på ett säkert sätt. Styra enhetens driftpanel (Fysisk) |
| Molngateway för fältgateway |TID ( TID ) |TLS (PSK/RSA) för att kryptera trafiken. |Tjuvlyssna eller störa kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP). Med anpassade protokoll måste du ta reda på hur du skyddar dem. |
| Molngateway för enhet |TID ( TID ) |TLS (PSK/RSA) för att kryptera trafiken. |Tjuvlyssna eller störa kommunikationen mellan enheten och gatewayen |Säkerhet på protokollnivå (MQTT/AMQP/HTTP/CoAP). Med anpassade protokoll måste du ta reda på hur du skyddar dem. |

Här är några exempel på hot i den här kategorin:

**Denial of Service**: Begränsade enheter är i allmänhet under DoS-hot när de aktivt lyssnar efter inkommande anslutningar eller oönskade datagram i ett nätverk, eftersom en angripare kan öppna många anslutningar parallellt och inte serva dem eller serva dem långsamt, eller enheten kan översvämmas med oönskad trafik. I båda fallen kan enheten effektivt göras obrukbar i nätverket.

**Förfalskning, informationsavslöjande:** Begränsade enheter och specialenheter har ofta en för alla säkerhetsanordningar som lösenord eller PIN-skydd, eller de är helt beroende av att lita på nätverket, vilket innebär att de ger tillgång till information när en enhet finns i samma nätverk, och det nätverket skyddas ofta endast av en delad nyckel. Det innebär att när den delade hemligheten till enheten eller nätverket avslöjas, är det möjligt att styra enheten eller observera data som släpps ut från enheten.  

**Spoofing**: en angripare kan avlyssna eller delvis åsidosätta sändningen och förfalska upphovsmannen (mannen i mitten)

**Manipulering:** en angripare kan avlyssna eller delvis åsidosätta sändningen och skicka falsk information 

**Information Disclosure:** en angripare kan tjuvlyssna på en sändning och få information utan tillstånd **Denial of Service:** en angripare kan störa sändningssignalen och neka informationsdistribution

#### <a name="storage"></a>Lagring

Varje enhet och fältgateway har någon form av lagring (tillfälligt för att köa data, operativsystem (OS) bildlagring).

| **Komponent** | **Hot** | **Åtgärd** | **Risk** | **Implementering** |
| --- | --- | --- | --- | --- |
| Lagring av enheter |TRID (AV TRID) |Lagringskryptering, signera loggarna |Läsa data från lagring (PII-data), manipulering av telemetridata. Manipulering av köade eller cachelagrade kommandokontrolldata. Manipulering av konfigurations- eller firmwareuppdateringspaket medan de cachelagras eller köas lokalt kan leda till att operativsystem och/eller systemkomponenter äventyras |Kryptering, meddelandeautentiseringskod (MAC) eller digital signatur. Om möjligt kan stark åtkomstkontroll genom åtkomstkontrollistor (ACL) eller behörigheter. |
| Bild av enhetsoperator |TRID (AV TRID) | |Manipulering av OS /ersätter OS-komponenterna |Skrivskyddad OS-partition, signerad OS-avbildning, Kryptering |
| Field Gateway-lagring (köa data) |TRID (AV TRID) |Lagringskryptering, signera loggarna |Läsa data från lagring (PII-data), manipulering av telemetridata, manipulering av köade eller cachelagrade kommandokontrolldata. Manipulering av konfigurations- eller firmwareuppdateringspaket (avsedda för enheter eller fältgateway) medan cachelagrade eller köade lokalt kan leda till att operativsystem och/eller systemkomponenter äventyras |BitLocker |
| Bild av Field Gateway OS |TRID (AV TRID) | |Manipulering av OS /ersätter OS-komponenterna |Skrivskyddad OS-partition, signerad OS-avbildning, Kryptering |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zon för enhets- och händelsebearbetning/molngateway

En molngateway är ett system som möjliggör fjärrkommunikation från och till enheter eller fältgateways från flera olika platser i det offentliga nätverksutrymmet, vanligtvis mot ett molnbaserat kontroll- och dataanalyssystem, en federation av sådana system. I vissa fall kan en molngateway omedelbart underlätta åtkomsten till specialenheter från terminaler som surfplattor eller telefoner. I det sammanhang som diskuteras här är "moln" tänkt att hänvisa till ett dedikerat databehandlingssystem som inte är bundet till samma plats som de anslutna enheterna eller fältgatewayerna, och där operativa åtgärder förhindrar riktad fysisk åtkomst men inte nödvändigtvis är till en " infrastruktur för offentliga moln. En molngateway kan eventuellt mappas till ett nätverksvirtualiseringsöverlägg för att isolera molngatewayen och alla dess anslutna enheter eller fältgateways från annan nätverkstrafik. Molngatewayen i sig är inte ett enhetskontrollsystem eller en bearbetnings- eller lagringsanläggning för enhetsdata. dessa anläggningar gränssnitt med molnet gateway. Molngatewayzonen innehåller molngatewayen själv tillsammans med alla fältgateways och enheter som är direkt eller indirekt kopplade till den.

Cloud gateway är mestadels specialbyggd programvara som körs som en tjänst med exponerade slutpunkter som fältgateway och enheter ansluter till. Som sådan måste utformas med säkerhet i åtanke. Följ [SDL-processen](https://www.microsoft.com/sdl) för att utforma och bygga denna tjänst.

#### <a name="services-zone"></a>Zonen Tjänster

Ett styrsystem (eller styrenhet) är en programvarulösning som samverkar med en enhet, en fältgateway eller molngateway i syfte att styra en eller flera enheter och/eller för att samla in och/eller lagra och/eller analysera enhetsdata för presentation eller efterföljande kontrolländamål. Kontrollsystem är de enda entiteterna i den här diskussionen som omedelbart kan underlätta interaktionen med människor. Undantagen är mellanliggande fysiska kontrollytor på enheter, som en strömbrytare som gör att en person kan stänga av enheten eller ändra andra egenskaper, och för vilka det inte finns någon funktionell motsvarighet som kan nås digitalt.

Mellanliggande fysiska kontrollytor är de där styrande logik begränsar den fysiska kontrollytans funktion så att en likvärdig funktion kan initieras på distans eller ingångskonflikter med fjärrinmatning kan undvikas – sådan mellanliggande kontrollytor är konceptuellt anslutna till ett lokalt styrsystem som utnyttjar samma underliggande funktionalitet som alla andra fjärrstyrningssystem som enheten kan anslutas till parallellt. De vanligaste hoten mot molnbaserad databehandling kan läsas på [CSA-sidan (Cloud Security Alliance).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

* [SDL hot modellering verktyg](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT-referensarkitektur](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
