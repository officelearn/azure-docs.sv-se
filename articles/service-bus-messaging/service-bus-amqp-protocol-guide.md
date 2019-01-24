---
title: AMQP 1.0 i Azure Service Bus och Event Hubs-protokollguide | Microsoft Docs
description: Uttryck och beskrivning av AMQP 1.0 i Azure Service Bus och Event Hubs-protokollguide
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 88f586fac4392e880efc3ef611a7c03177582bff
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856714"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 i Azure Service Bus och Event Hubs-protokollguide

Advanced Message Queueing Protocol 1.0 är ett standardiserat synkroniseringstecken och överför protokoll för att asynkront, säkert och tillförlitligt överföra meddelanden mellan två parter. Det är det primära protokollet av Azure Service Bus-meddelanden och Azure Event Hubs. Båda tjänsterna har också stöd för HTTPS. Den SBMP protokoll som stöds också är fasas ut och ersatts med AMQP.

AMQP 1.0 är resultatet av bred bransch samarbete som förenat mellanprogram leverantörer, till exempel Microsoft och Red Hat, med många meddelanden mellanprogram användare, till exempel JP Morgan Chase som representerar branschen för finansiella tjänster. Det har uppnått formella godkännande som en internationell standard som ISO/IEC 19494 teknisk standardisering-forum för AMQP-protokollet och tillägget specifikationer är OASIS.

## <a name="goals"></a>Mål

Den här artikeln sammanfattar grundkoncepten i av AMQP 1.0 messaging specifikationen tillsammans med en liten uppsättning draft tillägget specifikationer som för närvarande som slutförs i den tekniska kommittén OASIS AMQP kort och som förklarar hur Azure Service Bus implementerar och bygger på dessa specifikationer.

Målet är för utvecklare med hjälp av valfri stack för befintliga AMQP 1.0-klienten på alla plattformar för att kunna interagera med Azure Service Bus via AMQP 1.0.

Vanliga allmänna AMQP 1.0-stackar, till exempel Apache Proton eller Lite AMQP.NET implementera redan alla AMQP 1.0-kärnprotokoll. Dessa grundläggande gester omsluts ibland med en högre nivå API: et Apache Proton erbjuder även två, API: et för tvingande Messenger och reaktiv reaktor API.

I följande diskussionen förutsätter vi att hanteringen av AMQP-anslutningar, sessioner, och länkar och hantering av ramens överföringar och flödeskontroll hanteras av respektive stack (till exempel Apache Proton-C) och inte kräver mycket eventuella specifika uppmärksamhet från programutvecklare. Vi antar elementattributet förekomsten av en liten API-primitiver som möjligheten att ansluta och för att skapa någon form av *avsändaren* och *mottagare* abstraktion objekt, som sedan har vissa formen på `send()` och `receive()` åtgärder, respektive.

När diskutera avancerade funktioner i Azure Service Bus, till exempel bläddring bland meddelanden eller hantering av sessioner, beskrivs dessa funktioner i AMQP villkoren, men även som en överlappande pseudo implementering ovanpå abstraktionen antagen API.

## <a name="what-is-amqp"></a>Vad är AMQP?

AMQP är ett synkroniseringstecken och överför protokoll. Synkroniseringstecken innebär att det ger struktur för binära dataströmmar som flödar i båda riktningarna för en nätverksanslutning. Strukturen innehåller en avbildning för olika datablock kallas *bildrutor*, att utbytas mellan de anslutna parterna. Överföring funktioner Se till att båda kommunicerande parterna kan upprätta en delad förståelse om när bildrutor föras över och när överföringar anses slutförd.

Till skillnad från tidigare har upphört att gälla utkastversioner produceras av arbetsgruppen AMQP som fortfarande används av några koordinatorerna, fastställa arbetsgruppens slutgiltiga och standardiserad AMQP 1.0-protokollet inte förekomsten av en asynkron meddelandekö eller någon viss topologi för entiteter i en asynkron meddelandekö.

Protokollet som kan användas för symmetrisk peer-to-peer-kommunikation för interaktion med koordinatorerna som stöder köer och publicera/prenumerera på entiteter, som Azure Service Bus. Det kan också användas för interaktion med infrastruktur för meddelanden där interaktion mönster skiljer sig från vanlig köer, så är fallet med Azure Event Hubs. En Event Hub fungerar som en kö när händelser skickas till den, men fungerar mer som en seriell lagringstjänst när händelser läses av detta. Det liknar något en bandenhet. Klienten hämtar en förskjutning i tillgängliga data i dataströmmen och visas sedan alla händelser från förskjutningen till den senaste tillgängliga.

AMQP 1.0-protokollet är utformad för att kunna förlängas att aktivera ytterligare specifikationer att förbättra dess funktioner. De tre tillägg specifikationer som beskrivs i det här dokumentet visar detta. För kommunikation över den befintliga HTTPS/WebSockets infrastruktur, kan det vara svårt att konfigurera de interna AMQP TCP-portarna. En bindning specifikation definierar hur du även lägga till AMQP via WebSockets. För att interagera med infrastruktur för meddelanden på ett sätt för begäran/svar, av hanteringsskäl eller att tillhandahålla avancerade funktioner, definierar AMQP management specifikation obligatoriska grundläggande interaction primitiver. För sammansluten auktorisering model-integration definierar specifikationen AMQP anspråk baserade security associera och förnya auktoriseringstoken som är associerade med länkar.

## <a name="basic-amqp-scenarios"></a>Grundläggande AMQP-scenarier

Det här avsnittet beskrivs den grundläggande användningen av AMQP 1.0 med Azure Service Bus, vilket inkluderar skapa anslutningar, sessioner och länkar och överföring av meddelanden till och från Service Bus-entiteter som köer, ämnen och prenumerationer.

Den mest auktoritativa källan och lär dig hur AMQP fungerar är AMQP 1.0-specifikation, men specifikationen har skrivits för exakt implementering och inte för att lära protokollet. Det här avsnittet fokuserar på introduktion till så mycket terminologi efter behov för att beskriva hur Service Bus använder AMQP 1.0. En mer omfattande introduktion till AMQP, samt en bredare beskrivning av AMQP 1.0, kan du läsa [kursen video][this video course].

### <a name="connections-and-sessions"></a>Anslutningar och sessioner

AMQP anropar program som kommunicerar *behållare*; dessa innehåller *noder*, som är de kommunicerar enheterna i dessa behållare. En kö kan vara felaktiga noden. AMQP tillåter multiplexering, så att en enda anslutning kan användas för många kommunikationsvägar mellan noder. en program-klient kan till exempel samtidigt tar emot från en kö och skickar till en annan kö via samma nätverksanslutning.

![][1]

Nätverksanslutningen är därför fäst på behållaren. Den har initierats av behållare i klientrollen att göra en utgående TCP-socketanslutning till en behållare i rollen mottagare som lyssnar efter och tar emot inkommande TCP-anslutningar. Handskakningen anslutningen ingår även förhandling protokollversion, deklarerar eller förhandlade användningen av Transport Level Security (TLS/SSL) och en autentisering/auktorisering handskakning definitionsområdet anslutning som baseras på SASL.

Azure Service Bus måste du använda TLS vid alla tidpunkter. Den stöder anslutningar via TCP-port 5671, där TCP-anslutningen läggs först med TLS innan AMQP-protokollet handshake och stöder anslutningar via TCP-port 5672 där servern omedelbart erbjuder en obligatorisk uppgradering av anslutning med hjälp av AMQP-föreskrivs modellen i TLS. AMQP WebSockets bindningen skapar en tunnel via TCP-port 443 som motsvarar sedan 5671 AMQP-anslutningar.

När du skapat anslutningen och TLS kan erbjuder Service Bus två alternativ för SASL-mekanism:

* SASL OFORMATERAD används ofta för att skicka autentiseringsuppgifter för användarnamn och lösenord till en server. Service Bus har inte konton, men namngivna [delad åtkomst säkerhetsregler](service-bus-sas.md), vilket ger rättigheter och associeras med en nyckel. Namnet på en regel används som användarnamnet och nyckeln (som base64-kodade text) används som lösenord. De rättigheter som är associerade med den valda regeln styr vilka åtgärder som tillåts på anslutningen.
* SASL anonym används för att kringgå SASL auktorisering när klienten vill använda anspråk baserade säkerhet (CBS) modellen som beskrivs senare. Med det här alternativet upprättas en klientanslutning anonymt under en kort period då klienten endast kan interagera med CBS-slutpunkten och göra CBS-handskakningen.

När Transportanslutningen har upprättats kan behållarna deklarera maximalt ramstorleken som de är villig att hantera och efter en timeout för inaktivitet kommer de ensidigt frånkoppla om det finns ingen aktivitet på anslutningen.

De deklarera också hur många samtidiga kanaler stöds. En kanal är en enkelriktad, utgående virtuella överföring sökväg på anslutningen. En session tar en kanal från var och en av de sammankopplade behållarna för att bilda en dubbelriktad kommunikation sökväg.

Sessioner har en Windows-baserad flow-modellen för åtkomstkontroll; När en session skapas varje part deklarerar hur många ramar som det är beredd att acceptera till dess mottagna fönster. Som parter exchange ramar, överförda ramar fyllning som fönster och överföringar stoppa när fönstret är full och tills fönstret blir återställning eller expanderade med hjälp av den *flow performative* (*performative* är AMQP giltighetstiden för på protokollnivå gester som utbyts mellan de två parterna).

Det här fönstret-baserade modellen är ungefär detsamma som TCP-konceptet av Windows-baserad flödesreglering, men på sessionsnivå inuti socket. Det protokoll begreppet att tillåta att för flera samtidiga sessioner finns så att trafik för hög prioritet kan vara rusade tidigare begränsade normal trafik, som på en uttrycklig lane Målwebbplatsens.

Azure Service Bus använder för närvarande exakt en session för varje anslutning. Service Bus maximalt ram-storleken är 262 144 byte (256 kB) för Service Bus Standard och Event Hubs. Det är 1,048,576 (1 MB) för Service Bus Premium. Service Bus medför inte några viss strypning sessionsnivå windows, men återställer fönstret regelbundet som en del av länken på servernivå flödeskontroll (se [nästa avsnitt](#links)).

Anslutningar, kanaler och sessioner är tillfälliga. Om den underliggande anslutningen döljer, anslutningar, måste TLS-tunnel, SASL autentiseringskontext och sessioner återställas.

### <a name="links"></a>Länkar

AMQP överför meddelanden över länkar. En länk är en sökväg för kommunikation som skapades under en session som möjliggör överföring av meddelanden i en riktning; status-förhandling överföring är över länken och dubbelriktad kommunikation mellan de anslutna parterna.

![][2]

Länkar kan skapas med antingen behållaren när som helst och över en befintlig session, vilket gör AMQP skiljer sig från många andra protokoll, inklusive HTTP och MQTT, där överföringar och överföringen är en exklusiv behörighet för parten som skapar den socketanslutning.

Länken initieras behållaren ställer motsatt behållaren att godkänna en länk och den väljer en roll för avsändare eller mottagare. Därför antingen behållare kan initiera skapar enkelriktade eller dubbelriktade kommunikationsvägar med denna modelleras som par av länkar.

Länkar med namnet och som är associerade med noder. Enligt informationen i början är noder kommunicerar entiteter i en behållare.

I Service Bus är en nod direkt motsvarar en kö, ett ämne, en prenumeration eller en underkön för obeställbara meddelanden i en kö eller prenumeration. Namnet på noden som används i AMQP är därför det relativa namnet på entiteten i Service Bus-namnområdet. Om en kö med namnet `myqueue`, som också är dess nodnamnet för AMQP. En ämnesprenumeration följer reglerna från HTTP-API genom att sorteras i en ”prenumerationer” resurssamling och därför en prenumeration **sub** på ett ämne **mytopic** har nodnamnet AMQP  **mytopic/prenumerationer/sub**.

Den anslutande klienten krävs också att använda en lokal nodnamnet för att skapa länkar. Service Bus är inte förebyggande om dessa nodnamn och tolkar inte dem. AMQP 1.0-klientstackar använder vanligtvis ett schema för att garantera som dessa tillfälliga nodnamn som är unika inom omfånget för klienten.

### <a name="transfers"></a>Överföringar

När en länk har upprättats, kan meddelanden överföras via länken. AMQP, körs en överföring med en explicit protocol-gest (den *överföring* performative) som flyttas ett meddelande från sändare till mottagare via en länk. En överföring har slutförts när den är ””, vilket innebär att båda parter har upprättat en delad förståelse om resultatet av överföringen.

![][3]

I det enklaste fallet välja avsändaren att skicka meddelanden ”före reglerats”, vilket innebär att klienten inte är intresserad av resultatet och mottagaren ger inte någon feedback om resultatet av åtgärden. Det här läget stöds av Service Bus på nivån AMQP-protokollet, men inte visas i någon av klientens API: er.

Vanliga fallet är att meddelanden skickas oreglerade och mottagaren anger sedan godkännande eller avvisande med hjälp av den *disposition* performative. Avvisande uppstår när mottagaren kan inte acceptera meddelandet av någon anledning och avvisande meddelandet innehåller information om orsaken, vilket är ett fel struktur som definieras av AMQP. Om meddelanden avvisas på grund av interna fel i Service Bus, returnerar tjänsten extra information i denna struktur som kan användas för att tillhandahålla diagnostik tips till supportpersonalen om du arkiverar supportärenden. Du lär dig mer information om felen finns senare.

En särskild form av avvisande är den *är* tillstånd, vilket betyder att mottagaren har några tekniska invändningar mot överföringen, men också något intresse reglera överföringen. Ärendet finns, till exempel när ett meddelande har skickats till en Service Bus-klient, och klienten väljer att ”Avbryt” meddelandet eftersom det går inte att utföra det arbete som resulterar från att bearbeta meddelandet. meddelandeleverans själva är inte vid fel. En variant av det aktuella tillståndet är den *ändras* tillstånd, vilket gör att ändringar i meddelandet eftersom det har släppts. Det aktuella tillståndet används inte av Service Bus för närvarande.

AMQP 1.0-specifikation definierar en ytterligare disposition tillstånd som kallas *emot*, som är hjälper dig att hantera länken återställning. Länk-återställning kan Rekonstruerar tillståndet för en länk och eventuella väntande leveranser ovanpå en ny anslutning och -sessionen när den tidigare anslutning och sessionen har gått förlorade.

Service Bus stöder inte länken återställning; Om klienten förlorar anslutningen till Service Bus med meddelandet oreglerade överföring väntar meddelandeöverföringen går förlorad och klienten måste återansluta, återupprätta länken och försök överföringen.

Därmed kan Service Bus och Event Hubs överlåta ”minst en gång” där avsändaren kan vara säker på för meddelandet att ha lagras och har accepterat, men har inte stöd för ”exakt en gång” överföringar på AMQP-nivå, där systemet försöker återställa länken och Fortsätt att förhandla om delivery-tillstånd för att undvika duplicering av meddelandeöverföringen.

För att kompensera för möjligt dubblett skickar, Service Bus stöder dubblettidentifiering som en valfri funktion i köer och ämnen. Identifiering av dubbletter registrerar meddelande-ID: N för alla inkommande meddelanden under en användardefinierad tidsperiod och släpper tyst alla meddelanden som skickas med samma meddelande-ID under det fönstret.

### <a name="flow-control"></a>Flödeskontroll

Förutom den sessionsnivå flow-modellen för åtkomstkontroll som tidigare diskuteras, har varje länk sin egen flow-modellen för åtkomstkontroll. Sessionsnivå flödeskontroll skyddar behållaren från att hantera många bildrutor i när länken på servernivå flödeskontroll placerar programmet ansvarar för hur många meddelanden som ska hantera från en länk och när du behöver.

![][4]

På en länk, överföringar kan bara hända när avsändaren har tillräckligt med *länka kredit*. Länken kredit är en räknare som angetts av mottagaren använder den *flow* performative, som är begränsad till en länk. När avsändaren har tilldelats länk kredit, försöker den använda upp den krediten genom att skicka meddelanden. Varje meddelande leverans minskar länken återstående kredit med 1. När länken kredit är slut stoppar leveranser.

När Service Bus är i rollen mottagare kan innehåller det omedelbart avsändaren med gott om länken kredit, så att meddelanden kan skickas omedelbart. När länken kredit används Service Bus skickar en *flow* performative till avsändaren att uppdatera kreditsaldo länk.

I rollen avsändare skickar meddelanden använda upp eventuella utestående länk-krediter i Service Bus.

Ett anrop för ”ta emot” på API-nivå som översätter till en *flow* performative som skickas till Service Bus av klienten och Service Bus använder den krediten genom att ta det första tillgängliga, olåsta meddelandet från kön, låser den, och Överför den. Om det finns inget meddelande som är tillgängliga för leverans, upprätta eventuella utestående kredit med en länk till att viss enheten förblir registrerade efter ankomst och meddelanden är låst och överförs när de blir tillgängliga för alla utestående kreditkontroll.

Lås för ett meddelande som släpps när överföringen är i ett av de terminal *accepterat*, *avvisade*, eller *är*. Meddelandet tas bort från Service Bus när avslutat tillstånd är *accepterat*. Den finns kvar i Service Bus och levereras till nästa mottagaren när överföringen når något av andra tillstånd. Service Bus flyttas automatiskt meddelandet till entitetens obeställbara meddelanden. kön när den når den maximalt antal leveranser tillåts för entiteten på grund av upprepade avvisningar eller versioner.

Även om Service Bus-API: er inte visar dessa alternativ idag, använda en lägre nivå AMQP-protokollet klient länken kredit modellen för att aktivera ”pull-style” interaktionen för att utfärda en enhet av kredit för varje receive-begäran till en ”push-style”-modell av utfärda ett stort antal länka krediter och ta emot meddelanden när de blir tillgängliga utan några ytterligare åtgärder. Push stöds via den [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) eller [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) inställningar för enhetsegenskaper. När de är noll, använder AMQP-klienten den som länk-kredit.

I det här sammanhanget är det viktigt att förstå att klockan för förfallodatum för låset på meddelandet i entiteten startar när meddelandet tas från entiteten, inte när meddelandet placeras på kabeln. När klienten visar kan ta emot meddelanden genom att utfärda länk kredit, förväntas det därför aktivt hämta meddelanden över nätverket och vara redo att hantera dem. Annars upphört meddelandelåset innan meddelandet levereras även. Användning av länk-kredit flödeskontroll återspegla direkt omedelbar beredskap utan tillgängliga meddelanden som skickas till mottagaren.

Följande avsnitt innehåller en schema över performative flödet Sammanfattningsvis, under olika API interaktioner. Varje avsnitt beskrivs en annan logisk åtgärd. Vissa av dessa interaktioner vara ”lazy”, vilket innebär att de kan endast utföras när det behövs. Om du skapar en avsändaren kanske inte pekfunktioner nätverk tills det första meddelandet som skickas eller begärt.

Pilarna i följande tabell visar performative flödesriktning.

#### <a name="create-message-receiver"></a>Skapa meddelande mottagare

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>role=**receiver**,<br/>källa = {entitetsnamn}<br/>Target = {klient länk-ID}<br/>) |Klienten ansluter till entitet som mottagare |
| Service Bus-svar som kopplar dess slutet av länken |<-- attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>rollen =**avsändaren**,<br/>källa = {entitetsnamn}<br/>Target = {klient länk-ID}<br/>) |

#### <a name="create-message-sender"></a>Skapa meddelandets avsändare

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>rollen =**avsändaren**,<br/>källa = {klient länk-ID}<br/>Target = {entitetsnamn}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<-- attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>role=**receiver**,<br/>källa = {klient länk-ID}<br/>Target = {entitetsnamn}<br/>) |

#### <a name="create-message-sender-error"></a>Skapa meddelandets avsändare (fel)

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>rollen =**avsändaren**,<br/>källa = {klient länk-ID}<br/>Target = {entitetsnamn}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<-- attach(<br/>namn = {länk-name},<br/>Hantera = {numeriska referensen}<br/>role=**receiver**,<br/>källa = null,<br/>Target = null<br/>)<br/><br/><-- detach(<br/>Hantera = {numeriska referensen}<br/>stängd =**SANT**,<br/>fel = {felinformation}<br/>) |

#### <a name="close-message-receiversender"></a>Stäng mottagare/avsändaren

| Client | Service Bus |
| --- | --- |
| --> detach(<br/>Hantera = {numeriska referensen}<br/>stängd =**SANT**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<-- detach(<br/>Hantera = {numeriska referensen}<br/>stängd =**SANT**<br/>) |

#### <a name="send-success"></a>Skicka (lyckades)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>leverans-id = {numeriska referensen}<br/>leverans-taggen = {binära referensen}<br/>reglerats =**FALSKT**, mer =**FALSKT**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>role=receiver,<br/>först = {leverans ID}<br/>senast = {leverans ID}<br/>reglerats =**SANT**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Skicka (fel)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>leverans-id = {numeriska referensen}<br/>leverans-taggen = {binära referensen}<br/>reglerats =**FALSKT**, mer =**FALSKT**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>role=receiver,<br/>först = {leverans ID}<br/>senast = {leverans ID}<br/>reglerats =**SANT**,<br/>state=**rejected**(<br/>fel = {felinformation}<br/>)<br/>) |

#### <a name="receive"></a>Ta emot

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföra ()<br/>leverans-id = {numeriska referensen}<br/>leverans-taggen = {binära referensen}<br/>settled=**false**,<br/>more=**false**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |
| --> () disposition<br/>role=**receiver**,<br/>först = {leverans ID}<br/>senast = {leverans ID}<br/>reglerats =**SANT**,<br/>state=**accepted**<br/>) |Ingen åtgärd |

#### <a name="multi-message-receive"></a>Får flera meddelande

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföra ()<br/>leverans-id = {numeriska referensen}<br/>leverans-taggen = {binära referensen}<br/>settled=**false**,<br/>more=**false**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |
| Ingen åtgärd |< överföra ()<br/>leverans-id = {numeriska referensen + 1},<br/>leverans-taggen = {binära referensen}<br/>settled=**false**,<br/>more=**false**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |
| Ingen åtgärd |< överföra ()<br/>leverans-id = {numeriska referensen + 2},<br/>leverans-taggen = {binära referensen}<br/>settled=**false**,<br/>more=**false**,<br/>tillstånd =**null**,<br/>resume=**false**<br/>) |
| --> () disposition<br/>role=receiver,<br/>först = {leverans ID}<br/>senast = {leverans ID + 2}<br/>reglerats =**SANT**,<br/>state=**accepted**<br/>) |Ingen åtgärd |

### <a name="messages"></a>Meddelanden

I följande avsnitt beskrivs vilka egenskaper från standard AMQP meddelande avsnitten som används av Service Bus och hur de mappas till uppsättningen med Service Bus-API.

Egenskaper som programmet behöver definierar bör mappas till AMQP'S `application-properties` kartan.

#### <a name="header"></a>sidhuvud

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| hållbar |- |- |
| prioritet |- |- |
| ttl |TTL-värde för det här meddelandet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| first-acquirer |- |- |
| Antal leveranser |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| message-id |Programdefinierade, fri form identifierare för det här meddelandet. Används för identifiering av dubbletter. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| användar-id |Programdefinierade användar-ID, tolkas inte av Service Bus. |Är inte tillgängliga via Service Bus-API. |
| till |Programdefinierade mål-ID, tolkas inte av Service Bus. |[Till](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| ämne |Programdefinierade syfte meddelandeidentifieraren, tolkas inte av Service Bus. |[Etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| Svara till |Programdefinierade svars-path indikator, tolkas inte av Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| Korrelations-id |Programdefinierade Korrelations-ID, tolkas inte av Service Bus. |[Korrelations-ID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| innehållstyp |Programdefinierade innehållstyp indikator för brödtexten, tolkas inte av Service Bus. |[contentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Programdefinierade Innehållskodning indikator för brödtexten, tolkas inte av Service Bus. |Är inte tillgängliga via Service Bus-API. |
| absolute-expiry-time |Anger på vilka absoluta snabb meddelandet upphör att gälla. Ignoreras på indata (sidhuvud TTL observeras), auktoritativa på utdata. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| Skapandetid |Deklarerar då meddelandet skapades. Inte används av Service Bus |Är inte tillgängliga via Service Bus-API. |
| grupp-id |Programdefinierade identifierare för en uppsättning meddelanden. Används för Service Bus-sessioner. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| group-sequence |Räknare som identifierar den relativa sekvensnumret för meddelandet i en session. Ignoreras av Service Bus. |Är inte tillgängliga via Service Bus-API. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Meddelande-anteckningar

Det finns några andra service bus meddelandeegenskaper, som inte är en del av egenskaper för AMQP och skickas vidare som `MessageAnnotations` för meddelandet.

| Anteckningens kartan nyckel | Användning | API-namn |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklarerar då meddelandet ska visas på enheten |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Programdefinierade nyckel som bestämmer vilken partition som meddelandet ska hamna i. | [partitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Programdefinierad partitionsnyckel värde när en transaktion som ska användas för att skicka meddelanden via en överföring kön. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Definierade UTC-tid som representerar den faktiska tiden av enqueuing meddelandet. Ignoreras på indata. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt--sekvensnummer | Definierade unikt nummer som tilldelas ett meddelande. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Definierade i kö sekvensnumret för meddelandet. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-until | Definierade. Datum och tidpunkt som meddelandet kommer att låsas i kö eller prenumeration. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Definierade. Om meddelandet tas emot från kö för obeställbara meddelanden, källan till det ursprungliga meddelandet. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Kapaciteten för transaktion

En transaktion grupperar två eller flera åtgärder tillsammans i en körning omfattning. Enligt natur har sådana en transaktion måste se till att alla åtgärder som hör till en viss grupp av åtgärder lyckas eller misslyckas gemensamt.
Åtgärderna är grupperade med en identifierare `txn-id`.

För transaktionell interaktion klienten fungerar som en `transaction controller` , som styr vilka åtgärder som ska grupperas tillsammans. Service Bus-tjänsten fungerar som en `transactional resource` och utför arbete enligt en förfrågan från den `transaction controller`.

Klienten och tjänsten kommunicerar via en `control link` , som har upprättats genom klienten. Den `declare` och `discharge` meddelanden skickas av controller med länken kontroll att allokera och genomföra transaktioner respektive (de utgör inte avgränsning av transaktionella arbete). De faktiska skicka och ta emot utförs inte på den här länken. Varje transaktionella den begärda åtgärden är uttryckligen anges med önskad `txn-id` och därför kan uppstå på en länk på anslutningen. Om länken kontroll stängs även om det finns inte ut transaktioner som den skapats sedan alla sådana transaktioner återställs omedelbart och försöker utföra ytterligare transaktionella arbete på dem. leder till misslyckad. Meddelanden i kontrollen länk får inte vara före reglerats.

Varje anslutning måste initiera en egen kontroll länk för att kunna börja och sluta transaktioner. Tjänsten definierar ett särskilt mål som fungerar som en `coordinator`. Klienten/kontrollanten upprättar en kontroll länk till det här målet. Kontroll av länk är utanför gränsen för en entitet, det vill säga samma kontroll länk kan användas för att initiera och utskrivning transaktioner för flera enheter.

#### <a name="starting-a-transaction"></a>Starta en transaktion

Att börja transaktionella arbeta. kontrollanten måste skaffa en `txn-id` från coordinator. Detta sker genom att skicka en `declare` typ meddelande. Om deklarationen lyckas koordinatorn svarar med en disposition-resultatet som medför det tilldelade `txn-id`.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| bifoga)<br/>namn = {länk-name},<br/>... ,<br/>rollen =**avsändaren**,<br/>Target =**Coordinator**<br/>) | ------> |  |
|  | <------ | bifoga)<br/>namn = {länk-name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>state=**Declared**(<br/>**txn-id**= {transaktions-ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Avslutning av en transaktion

Kontrollanten avslutar transaktionella arbetet genom att skicka en `discharge` meddelande Coordinator. Kontrollanten anger att den vill allokera eller återställa transaktionella arbetet genom att ange den `fail` flaggan utsläpp brödtext. Om koordinatorn inte att slutföra utsläpp kan avvisas meddelandet med det här resultatet i `transaction-error`.

> Obs: misslyckas = true avser återställningen av en transaktion och misslyckas = false refererar till Commit.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>Transaktionell arbete<br/>på andra länkar<br/> . . . |
| transfer(<br/>leverans-id = 57,...)<br/>{AmqpValue)<br/>**Utskrivning (txn-id = 0,<br/>misslyckas = false)**)}| ------> |  |
| | <------ | disposition ( <br/> först = 57, senast = 57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Skicka ett meddelande i en transaktion

Alla transaktionella arbetet utförs med transaktionella leverans tillstånd `transactional-state` som utför txn-id. När det gäller skickar meddelanden, utförs transaktionell-tillståndet av meddelandets överföring ram. 

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| transfer(<br/>handle=1,<br/>leverans-id = 1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{nyttolast}| ------> |  |
| | <------ | disposition ( <br/> först = 1, senast = 1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Tar bort ett meddelande i en transaktion

Meddelandet disposition innehåller åtgärder som `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Om du vill utföra dessa åtgärder i en transaktion, skickar den `transactional-state` med disposition.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |transfer(<br/>Hantera = 2,<br/>leverans-id = 11, <br/>tillstånd = null)<br/>{nyttolast}|  
| disposition ( <br/> först = 11, senast = 11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Avancerade funktioner för Service Bus

Det här avsnittet beskrivs avancerade funktioner i Azure Service Bus som baseras på utkast tillägg till AMQP, för närvarande håller på att utvecklas i den tekniska kommittén OASIS för AMQP. Service Bus implementerar de senaste versionerna av dessa utkast och antar ändringar som införs eftersom dessa utkast att nå standard status.

> [!NOTE]
> Service Bus-meddelanden avancerade åtgärder stöds via ett begäran/svar-mönster. Dessa åtgärder beskrivs i artikeln [AMQP 1.0 i Service Bus: begäran-svar-baserade åtgärder](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP-hantering

AMQP management specifikation är först av draft-tilläggen som beskrivs i den här artikeln. Den här specifikationen definierar en uppsättning protokoll lagrade ovanpå AMQP-protokollet som tillåter management samverkan med infrastruktur för meddelanden via AMQP. Specifikationen definierar allmänna åtgärder som *skapa*, *läsa*, *uppdatera*, och *ta bort* för att hantera enheter i en infrastruktur för meddelanden och en uppsättning frågeåtgärder.

Alla dessa gester kräver en begäran/svar-interaktion mellan klienten och infrastruktur för meddelanden och därför specifikationen definierar hur den interaktionsmönstret ovanpå AMQP: klienten ansluter till infrastruktur för meddelanden initierar en session och skapar sedan ett par med länkar. På en länk klienten fungerar som avsändare och på den andra fungerar den som mottagare, vilket skapar ett par med länkar som kan fungera som en dubbelriktad-kanal.

| Logisk åtgärd | Client | Service Bus |
| --- | --- | --- |
| Skapa sökvägen för begäran-svar |--> attach(<br/>namn = {*Länknamnet*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**avsändaren**,<br/>källa =**null**,<br/>target=”myentity/$management”<br/>) |Ingen åtgärd |
| Skapa sökvägen för begäran-svar |Ingen åtgärd |\<-- attach(<br/>namn = {*Länknamnet*},<br/>hantera = {*numeriska referensen*},<br/>role=**receiver**,<br/>källa = null,<br/>target=”myentity”<br/>) |
| Skapa sökvägen för begäran-svar |--> attach(<br/>namn = {*Länknamnet*},<br/>hantera = {*numeriska referensen*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>Target = ”myclient$ id”<br/>) | |
| Skapa sökvägen för begäran-svar |Ingen åtgärd |\<-- attach(<br/>namn = {*Länknamnet*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**avsändaren**,<br/>source=”myentity”,<br/>Target = ”myclient$ id”<br/>) |

Med den par länkar på plats, begäran/svar-implementeringen är enkel: en begäran är ett meddelande skickas till en entitet i meddelandeinfrastrukturen som förstår det här mönstret. I det begärandemeddelandet den *svarsadressen* i den *egenskaper* delen är inställd på den *target* identifierare för den länk som kan leverera svaret. Hantering av entiteten bearbetar begäran och sedan leverera svaret via länken vars *target* ID: t matchar det angivna *svarsadressen* identifierare.

Mönstret kräver naturligtvis att behållaren klienten och klientgenererade-ID för mål-svar är unikt över alla klienter och av säkerhetsskäl också svårt att förutse.

Meddelandeutbyten för management-protokollet och för alla andra protokoll som använder samma mönster som sker på programnivå; de definiera inte nya AMQP på protokollnivå gester. Det är avsiktlig, så att program kan vidta omedelbara nytta av dessa tillägg med kompatibla AMQP 1.0-stackar.

Service Bus implementerar inte de grundläggande funktionerna i management-specifikationen för närvarande, men begäran/svar-mönstret som definieras i management-specifikationen är grundläggande för funktionen anspråk baserade säkerhet och för nästan alla de avancerade funktioner som beskrivs i följande avsnitt:

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

AMQP anspråk baserade auktorisering (CBS) specifikationen draft bygger på begäran/svar-mönstret management specifikation och beskriver en generaliserad modell för hur du använder federerad säkerhetstoken med AMQP.

Standard-säkerhetsmodellen i AMQP som beskrevs i inledningen är baserad på SASL och integreras med AMQP-handskakningen. Använder SASL har fördelen att den innehåller en utökningsbar modell som en uppsättning metoder har definierats från alla protokoll som kallades tidigare leans på SASL kan dra. Bland dessa mekanismer är ”OFORMATERAD” för överföring av användarnamn och lösenord, ”externt” för att binda till TLS-på radnivå, ”anonym” för att uttrycka avsaknad av explicit autentisering/auktorisering och ett stort antal ytterligare mekanismer som gör att skicka autentisering och/eller auktorisering autentiseringsuppgifter eller token.

AMQP'S SASL integration har två nackdelar:

* Alla autentiseringsuppgifter och token är begränsade till anslutningen. En infrastruktur för meddelanden kanske vill ge differentierad åtkomstkontroll på basis av per entitet; till exempel tillåta att innehavaren av en token ska skickas till kön A men inte till kön B. Med den autentiseringskontext förankrad på anslutningen, går det inte att använda en enda anslutning och ännu använder olika åtkomsttoken för kön A och kön B.
* Åtkomsttoken är vanligtvis endast giltiga under en begränsad tid. Den här giltigheten kräver att användaren med jämna mellanrum hämta token som ger en möjlighet att tokenutfärdare att vägra att utfärda en ny token om användarens åtkomst behörigheten har ändrats. AMQP-anslutningar kan pågå under långa tidsperioder. SASL-modellen innehåller endast en chans att ställa in en token vid anslutningen, vilket innebär att den infrastruktur för meddelanden som antingen har du koppla från klienten när token upphör att gälla eller som behövs för att godta risken för fortsatt kommunikation med en klient som åtkomstbehörighet kan ha återkallats under tiden.

AMQP CBS-specifikationen som implementeras av Service Bus, kan en elegant lösning för båda dessa problem: Det gör att en klient att koppla åtkomsttoken till varje nod och för att uppdatera dessa token innan de går ut, utan att avbryta flödet meddelande.

CBS definierar en hantering av virtuella-nod med namnet *$cbs*, tillhandahållas av meddelandeinfrastrukturen. Hanteringsnoden accepterar token för alla andra noder i meddelandeinfrastrukturen.

Protokoll-gest är en begäran/svar-exchange som definieras i management-specifikationen. Att innebär att klienten upprättar ett par med länkar till den *$cbs* noden och sedan skickar en begäran på utgående länken och väntar sedan svaret på inkommande länken.

Meddelandet med begäran har följande egenskaper för program:

| Nyckel | Valfri | Värdetyp | Värdet innehållet |
| --- | --- | --- | --- |
| åtgärd |Nej |sträng |**PUT-token** |
| typ |Nej |sträng |Typ av token som är put. |
| namn |Nej |sträng |Den ”målgruppen” som gäller för token. |
| upphörande |Ja |tidsstämpel |Förfallotid för token. |

Den *namn* egenskapen identifierar den entitet som token vara associerad. Det är sökvägen till köer eller ämnen/prenumerationer i Service Bus. Den *typ* egensapen identifierar vydat typ:

| Tokentyp | Token beskrivning | Brödtexten | Anteckningar |
| --- | --- | --- | --- |
| amqp:jwt |JSON-Webbtoken (JWT) |AMQP-värde (sträng) |Ännu inte tillgänglig. |
| amqp:swt |Simple Webbtoken (SWT) |AMQP-värde (sträng) |Stöds endast för SWT-token som utfärdas av AAD/ACS |
| servicebus.windows.net:sastoken |Service Bus SAS-Token |AMQP-värde (sträng) |- |

Token ger rättigheter. Service Bus känner tre grundläggande rättigheter: ”Skicka” gör det möjligt att skicka ”Listen” aktiverar tar emot, och ”hantera” kan manipulera entiteter. SWT-token som utfärdas av AAD/ACS uttryckligen inkludera dessa rättigheter som anspråk. Service Bus SAS-token finns regler som har konfigurerats på namnområdet eller entitet och dessa regler har konfigurerats med behörighet. Signera token med nyckeln som är associerad med regeln därför att gör token snabb respektive rättigheter. Den token som är associerade med en entitet med hjälp av *put-token* låter anslutna klienten interagerar med entiteten per token rättigheter. En länk där klienten tar på den *avsändaren* roll kräver ”skicka” höger; tar på den *mottagare* roll kräver ”Listen” direkt.

Svarsmeddelande har följande *egenskaper för program* värden

| Nyckel | Valfri | Värdetyp | Värdet innehållet |
| --- | --- | --- | --- |
| statuskod |Nej |int |HTTP-svarskoden **[RFC2616]**. |
| status-description |Ja |sträng |Beskrivning av status. |

Klienten kan anropa *put-token* flera gånger och för entiteter i meddelandeinfrastrukturen. Token är begränsade till den befintliga klienten och Fäst mot den aktuella anslutningen, vilket innebär att servern avslutar de sparade token när anslutningen bryts.

Den aktuella implementationen av Service Bus tillåter endast CBS tillsammans med metoden SASL ”anonym”. En SSL/TLS-anslutning måste alltid finnas innan SASL-handskakningen.

Mekanismen för anonym måste därför stödjas av valda AMQP 1.0-klienten. Anonym åtkomst innebär att den inledande handskakningen, inklusive att skapa för den första sessionen sker utan Service Bus att veta vem som skapar anslutningen.

När anslutningen och sessionen har upprättats kan du koppla länkar till den *$cbs* nod och skicka den *put-token* begär är den enda tillåtna åtgärder. En giltig token måste anges med en *put-token* begäran för en eller flera noder för entiteten i 20 sekunder när anslutningen har upprättats, annars anslutningen ensidigt ignoreras av Service Bus.

Klienten är skyldig att betala för att hålla reda på token upphör att gälla. När en token upphör att gälla, utelämnar Service Bus omedelbart alla länkar på anslutningen till respektive entiteten. För att förhindra problem inträffar, kan klienten ersätta token för noden med ett nytt lösenord när som helst via den virtuella *$cbs* hanteringsnod med samma *put-token* rörelser och utan får i den sätt att nyttolasten trafik som flöden på olika länkar.

### <a name="send-via-functionality"></a>Skicka via funktioner

[Skicka via / överföra avsändaren](service-bus-transactions.md#transfers-and-send-via) är en funktion som gör att service bus framåt ett visst meddelande till en målentitet via en annan entitet. Den här funktionen används för att utföra åtgärder över entiteter i en enda transaktion.

På så sätt kan du skapa en avsändare och upprätta länken till den `via-entity`. Ytterligare information skickas vid upprättandet länken för att upprätta SANT målet meddelanden/överföringar på den här länken. När bifoga har lyckats, vidarebefordras automatiskt alla meddelanden som skickas på den här länken till den *målentitet* via *via entitet*. 

> Obs! Autentisering måste utföras för både *via entitet* och *målentitet* innan du etablerar den här länken.

| Client | | Service Bus |
| --- | --- | --- |
| bifoga)<br/>namn = {länk-name},<br/>role=sender,<br/>källa = {klient länk-ID}<br/>Target =**{via entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | bifoga)<br/>namn = {länk-name},<br/>role=receiver,<br/>källa = {klient länk-ID}<br/>Target = {via-entity},<br/>Egenskaper för = kartan [()<br/>com.microsoft:transfer-destination-address=<br/>{målentitet})] ) |

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [Stöd för AMQP 1.0 för Service Bus partitionerade köer och ämnen]
* [AMQP i Service Bus för Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[Stöd för AMQP 1.0 för Service Bus partitionerade köer och ämnen]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP i Service Bus för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
