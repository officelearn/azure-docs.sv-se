---
title: AMQP 1.0 i Azure Service Bus och Händelsehubbar protokollet guiden | Microsoft Docs
description: Protokollet guide till uttryck och en beskrivning av AMQP 1.0 i Azure Service Bus och Händelsehubbar
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: ''
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: clemensv;hillaryc;sethm
ms.openlocfilehash: 9af578cef9a89b4ae953b26f261f99593b79deb2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 i Azure Service Bus och Händelsehubbar protocol-guiden

Avancerade Message Queueing-protokollet 1.0 är en standardiserad synkroniseringstecken och överför protokoll för asynkront säkert och tillförlitligt meddelanden överförs mellan två parter. Det är primärt protokoll av Azure Service Bus-meddelanden och Händelsehubbar i Azure. Båda tjänsterna har också stöd för HTTPS. Den SBMP protokoll som stöds också kommer att överges för AMQP.

AMQP 1.0 är resultatet av bred branschen samarbete som mellanprogramvaruleverantörer, till exempel Microsoft och Red Hat med många meddelanden mellanprogram användare, till exempel JP Morgan Chase som representerar industrin för finansiella tjänster, samverkar. Det har uppnått formella godkännande som en internationell standard som ISO/IEC 19494 teknisk standardisering-forum för AMQP protokoll och tillägget specifikationer är OASIS.

## <a name="goals"></a>Mål

Den här artikeln sammanfattar grundläggande begrepp som den AMQP 1.0 messaging specifikation tillsammans med en liten uppsättning utkast tillägget specifikationer som för närvarande är att färdigställas i den tekniska kommittén OASIS AMQP kort och förklarar hur Azure Service Bus implementerar och bygger på dessa specifikationer.

Målet är att alla utvecklare som med alla befintliga AMQP 1.0-klienten stack på alla plattformar för att kunna interagera med Azure Service Bus via AMQP 1.0.

Vanliga allmänna AMQP 1.0-stackar, till exempel Apache Proton eller AMQP.NET Lite implementera redan alla kärnprotokoll AMQP 1.0. De grundläggande gester är ibland omslutna av en högre nivå API; Apache Proton erbjuder även två tvingande Messenger-API och reaktiv reaktor API.

I följande diskussionen förutsätter vi att hanteringen av AMQP anslutningar, sessioner, och länkar och hantering av ram överföringar och flödeskontroll hanteras av respektive stacken (till exempel Apache Proton-C) och inte kräver mycket eventuella specifika uppmärksamhet från programutvecklare. Vi förutsätter elementattributet finnas några API-primitiver som möjligheten att ansluta och för att skapa någon form av *avsändaren* och *mottagare* abstraction objekt, som sedan har vissa form av `send()` och `receive()` åtgärder, respektive.

När du ska diskutera avancerade funktioner för Azure Service Bus, till exempel meddelande Bläddra eller hantering av sessioner, beskrivs funktionerna i AMQP villkor, men även som en skiktad genererat implementering ovanpå antagen API abstraktionen.

## <a name="what-is-amqp"></a>Vad är AMQP?

AMQP är ett synkroniseringstecken och överför protokoll. Synkroniseringstecken innebär att det finns struktur för binära dataströmmar som flöda i båda riktningarna för en nätverksanslutning. Strukturen innehåller en avbildning för distinkta datablock, kallas *ramar*att utväxlas mellan de anslutna parterna. Överföring funktioner Se till att båda kommunicerande parterna kan upprätta en delad förståelse om när ramar överföras och när överföringar anses slutförd.

Till skillnad från tidigare har upphört att gälla utkastversioner av arbetsgruppen AMQP som fortfarande används av några meddelande mäklare, fastställa arbetsgruppens slutliga och standardiserad AMQP 1.0-protokollet inte förekomsten av en viss topologi för entiteter i en koordinator för meddelandet eller förhandlare meddelandet.

Protokollet kan användas för symmetrisk peer-to-peer-kommunikation för interaktion med meddelandet mäklare som stöder köer och publicera/prenumerera entiteter som Azure Service Bus. Det kan också användas för interaktion med meddelandeinfrastruktur där interaktion mönster skiljer sig från vanlig köer, vilket är fallet med Händelsehubbar i Azure. En Händelsehubb fungerar som en kö när händelser skickas till den, men fungerar mer som en seriell lagringstjänsten när händelser läses från den. en bandenhet eller något liknande. Klienten hämtar en förskjutning i tillgängliga dataströmmen och sedan behandlas alla händelser från förskjutningen till den senaste tillgängliga.

AMQP 1.0-protokollet är utformat för att kunna förlängas att aktivera ytterligare specifikationer att förbättra dess funktioner. De tre tillägg specifikationer som beskrivs i det här dokumentet illustrerar detta. En bindning-specifikation definierar så layer AMQP över WebSockets för kommunikation över den befintliga HTTPS/WebSockets infrastruktur där konfigurera inbyggda AMQP TCP-portarna kan vara svårt. För att interagera med meddelandeinfrastrukturen på ett sätt för frågor och svar för hanteringsändamål eller för att tillhandahålla avancerade funktioner, definierar AMQP management specifikation krävs grundläggande interaktion primitiver. För federerade auktorisering model-integration definierar specifikationen AMQP anspråk baserade säkerhet så att koppla och förnya auktorisering token som är associerade med länkar.

## <a name="basic-amqp-scenarios"></a>Grundläggande AMQP-scenarier

Det här avsnittet beskrivs de grundläggande användningen av AMQP 1.0 med Azure Service Bus, vilket innefattar att skapa anslutningar, sessioner och länkar och överföring av meddelanden till och från Service Bus-entiteter, till exempel köer, ämnen och prenumerationer.

Mest auktoritativ datakälla att lära dig hur AMQP fungerar är AMQP 1.0-specifikation, men specifikationen har skrivits till exakt hur implementeringen och inte för att utbilda protokollet. Det här avsnittet fokuserar på introduktion till så mycket terminologi som behövs för att beskriva hur Service Bus använder AMQP 1.0. För en mer omfattande introduktion till AMQP, samt en bredare beskrivning av AMQP 1.0, kan du granska [kursen video][this video course].

### <a name="connections-and-sessions"></a>Anslutningar och sessioner

AMQP anropar kommunicerande program *behållare*; dessa innehåller *noder*, vilka är de kommunicerande enheterna i dessa behållare. En kö kan vara felaktiga noden. AMQP tillåter för multiplexering, så att en anslutning kan användas för många Kommunikationssökvägar mellan noder. en program-klient kan till exempel samtidigt ta emot från en kö och skicka till en annan kö via samma nätverksanslutning.

![][1]

Nätverksanslutningen är därför fäst på behållaren. Den initieras av behållare i klientrollen att göra en utgående TCP-socketanslutning till en behållare i rollen mottagare som lyssnar efter och accepterar inkommande TCP-anslutningar. Handskakningen anslutningen innehåller förhandling protokollversion, deklarerar eller förhandlade användning av Transport Level Security (TLS/SSL) och en autentisering/auktorisering handskakning definitionsområdet anslutning som baseras på SASL.

Azure Service Bus kräver TLS vid alla tidpunkter. Den stöder anslutningar via TCP-port 5671, där TCP-anslutningen läggs först med TLS innan du anger AMQP protokollet handskakningen och stöder även anslutningar via TCP-port 5672 där servern omedelbart erbjuder en obligatorisk uppgradering av anslutningen i TLS med hjälp av AMQP föreskrivs-modellen. AMQP WebSockets bindningen skapar en tunnel via TCP-port 443 som motsvarar sedan AMQP 5671 anslutningar.

När du har installerat anslutningen och TLS, finns Service Bus två SASL mekanism alternativ:

* SASL OFORMATERAD används ofta för att skicka autentiseringsuppgifter för användarnamn och lösenord till en server. Service Bus saknar konton, men namngivna [delad åtkomst säkerhetsregler](service-bus-sas.md), vilket ger rättigheter och som är associerade med en nyckel. Namnet på en regel som används som användarnamnet och nyckel (som base64-kodad text) används som lösenord. De rättigheter som associeras med den valda regeln styr åtgärder som tillåts för anslutningen.
* SASL anonym används för att kringgå SASL tillstånd när du vill att klienten ska använda anspråk baserade säkerhet (CBS) modellen som beskrivs senare. Med det här alternativet går en klient att ansluta anonymt under en kort period under vilken klienten bara kan interagera med CBS slutpunkten och CBS handskakningen måste slutföra.

När transportanslutningen upprättas behållarna deklarera maximala ramstorleken som de är beredda att hantera och efter en timeout för inaktivitet ska ensidigt frånkoppling om det inte finns någon aktivitet på anslutningen.

De förklarar också hur många samtidiga kanaler stöds. En kanal är en enkelriktad, utgående virtuella överföringen ovanpå anslutningen. En session tar en kanal från var och en av de sammankopplade behållarna för att bilda en dubbelriktad kommunikation sökväg.

Sessioner har en Windows-baserad åtkomstkontroll Dataflödesmodell; När en session skapas varje part förklarar hur många ramar som den är beredd att acceptera till dess visas fönstret. Som parterna exchange ramar överförda ramar fill som fönster och överföringar stoppa när fönstret är full och tills fönstret blir Återställ eller expanderade med hjälp av den *flöde performative* (*performative* är AMQP term för protokollnivå gester som utbyts mellan de två parterna).

Den här Windows-baserad modellen är ungefär detsamma som TCP-konceptet av Windows-baserad flödesreglering, men på nivån session i socket. Det protokoll begreppet att tillåta för flera samtidiga sessioner finns så att trafik med hög prioritet kan vara rusade tidigare begränsad normal trafik som på en motorväg express lane.

Azure Service Bus använder för närvarande exakt en session för varje anslutning. Service Bus maximala ram-storlek är 262 144 byte (256 kB) för Service Bus Standard och Händelsehubbar. Det är 1,048,576 (1 MB) för Service Bus Premium. Service Bus medför inte några särskilda session-nivå bandbreddsbegränsning windows, men återställer fönstret regelbundet som en del av Koppla nivån flödeskontroll (se [i nästa avsnitt](#links)).

Anslutningar, kanaler och sessioner är tillfälliga. Om den underliggande anslutningen döljs, anslutningar, måste TLS-tunnelns, SASL autentiseringskontexten och sessionerna återställas.

### <a name="links"></a>Länkar

AMQP överför meddelanden över länkar. En länk är en sökväg för kommunikation som skapats under en session som gör att överföra meddelanden i en riktning; status-förhandlingen överföring är över länken och dubbelriktad kommunikation mellan de anslutna parterna.

![][2]

Länkar kan skapas genom att antingen behållare när som helst och över en befintlig session, vilket gör AMQP skiljer sig från många andra protokoll, inklusive HTTP och MQTT, där överföringar och överföringen är en exklusiv behörighet part skapar Socketanslutningen.

Behållaren för initiering av länk ställer motsatt behållaren kan inte godkänna en länk och den väljer en roll för avsändare eller mottagare. Därför behållare kan initiera skapar enkelriktad eller dubbelriktad Kommunikationssökvägar med denna modelleras som par av länkar.

Länkar med namnet och som är kopplade till noder. Enligt informationen i början är noder kommunicerar entiteter i en behållare.

I Service Bus är en nod direkt motsvarar en kö, ett ämne, en prenumeration eller en systemkön underkön för en kö eller prenumeration. Nodnamn som används i AMQP är därför det relativa namnet på entiteten i Service Bus-namnrymd. Om en kö heter **MinKö**, som också är dess AMQP nodnamn. En prenumeration på artikeln följer reglerna från http-API av sortering i en resurssamling ”prenumerationer” och därmed en prenumeration **sub** eller ett ämne **mytopic** har nodnamnet AMQP **sub-mytopic/prenumerationer**.

Den anslutande klienten även krävs för att använda en lokal nod för att skapa länkar. Service Bus tolka inte dem är inte normativ om dessa nodnamn. AMQP 1.0-klientstackar vanligtvis använder du ett schema för att garantera dessa tillfälliga noden namn är unikt i omfånget för klienten.

### <a name="transfers"></a>Överföringar

När en länk har upprättats kan meddelanden överföras via den länken. AMQP, en överföring körs med en explicit protokollet gest (den *överföring* performative) som flyttas ett meddelande från sändare till mottagare via en länk. En överföringen har slutförts när den är ””, vilket innebär att båda parterna har etablerat en delad förståelse om resultatet av överföringen.

![][3]

I det enklaste fallet kan avsändaren välja att skicka meddelanden ”före regleras”, vilket innebär att klienten inte är intresserad i resultatet och mottagaren inte ge feedback om resultatet av åtgärden. Det här läget stöds av Service Bus på protokollnivå AMQP, men inte visas i någon av klientens API: er.

I vanliga fall har att meddelanden skickas oreglerade och mottagaren anger sedan godkännande eller underkännande med hjälp av den *disposition* performative. Avvisande inträffar när mottagaren kan inte acceptera meddelandet av någon anledning och avvisning meddelandet innehåller information om orsaken till, vilket är ett fel struktur som definieras av AMQP. Om meddelanden som avvisas på grund av interna fel i Service Bus returnerar tjänsten extra information i denna struktur som kan användas för att tillhandahålla diagnostik tips till supportpersonalen om du arkiverar supportärenden. Du lär dig mer information om fel senare.

En speciell typ av avvisande är den *publicerat* tillstånd, vilket indikerar att mottagaren har några tekniska invändningar mot överföringen, men även inga intresse för att reglera överföringen. Det fallet finns, till exempel när ett meddelande har skickats till en Service Bus-klient, och klienten väljer att ”Avbryt” meddelandet eftersom det går inte att utföra det arbete som härrör från att bearbeta meddelandet. meddelandeleverans själva är inte vid fel. En variant av det aktuella tillståndet är den *ändrade* tillstånd, vilket gör ändringar i meddelandet som de släpps. Det aktuella tillståndet används inte av Service Bus för närvarande.

AMQP 1.0-specifikationen definierar en ytterligare disposition läge som kallas *emot*, som är hjälper dig att hantera länken återställning. Länken återställning kan Rekonstruerar tillståndet för en länk och eventuella väntande leveranser ovanpå en ny anslutning och -sessionen när den tidigare anslutning och session har gått förlorade.

Service Bus stöder inte länken återställning. Om klienten förlorar anslutningen till Service Bus med meddelandet oreglerade överföra väntande som meddelandeöverföringen går förlorad och klienten måste återansluta återupprätta länken och gör överföringen.

Därför Service Bus och Händelsehubbar stöder ”minst en gång” överföringar där avsändaren kan säkerställas för meddelandet med lagras och godkänts, men har inte stöd för ”exakt en gång” överföringar på nivån AMQP där systemet försöker återställa länken och fortsätta att förhandla leverans tillstånd för att undvika dubbletter av meddelandeöverföringen.

För att kompensera för möjlig dubblett skickar Service Bus stöder dubblettidentifiering som en valfri funktion i köer och ämnen. Identifiering av dubbletter registrerar meddelande-ID för inkommande meddelanden under en användardefinierad tidsperioden och sedan släpper tyst alla meddelanden som skickas med samma meddelande-ID: N under samma fönstret.

### <a name="flow-control"></a>Flödeskontroll

Förutom den session nivå flödet modellen för åtkomstkontroll som diskuterats tidigare, har varje länk sin egen Dataflödesmodell för kontrollen. Sessionen nivå flödeskontroll skyddar behållaren slipper hantera för många bildrutor i när Koppla nivån flödeskontroll placerar programmet ansvarig för hur många meddelanden som ska hanteras från en länk och när.

![][4]

På en länk överföringar kan bara inträffa när avsändaren har tillräckligt med *länka kredit*. Länken kredit är en uppsättning av mottagaren använder räknare i *flöde* performative, som är begränsad till en länk. När avsändaren har tilldelats länken kredit, försöker den använder upp den krediten genom att skicka meddelanden. Varje meddelande leverans minskar länken återstående kredit med 1. När länken kredit används stoppa leveranser.

När Service Bus är i rollen mottagaren kan ger den direkt avsändaren gott om länken kredit, så att meddelanden kan skickas omedelbart. Länken kredit används Service Bus skickar en *flöde* performative avsändaren att uppdatera Kreditsaldot för länken.

I rollen avsändaren skickar Service Bus-meddelanden använder upp krediter utestående länk.

Ett anrop för ”ta emot” på API-nivå översätter till en *flöde* performative som skickas till Service Bus av klienten och Service Bus använder den krediten genom att göra det första tillgängliga, olåsta meddelandet från kön, låsa, och Överför den. Om det finns inget meddelande är tillgängligt för leverans, upprätta krediter utestående med en länk till att viss enheten förblir registrerade i ordning för ankomst och meddelanden är låst och överförs när de blir tillgängliga för alla utestående kreditkontroll.

Låset för ett meddelande släpps när överföringen är i ett av de terminal *accepteras*, *avvisade*, eller *publicerat*. Meddelandet tas bort från Service Bus när terminal tillstånd är *accepteras*. Det finns kvar i Service Bus och levereras till nästa mottagaren när dataöverföringen når något av andra tillstånd. Service Bus placeras automatiskt meddelandet entitetens obeställbara meddelanden när den når maximal leverans antalet tillåtna för entiteten på grund av upprepade nekanden eller versioner.

Även om Service Bus-API: er inte direkt exponerar sådana ett alternativ idag, kan en lägre nivå AMQP protokollet klient använda länken kredit modellen aktivera interaktionen ”pull-style” för att utfärda en enhet av kredit för varje receive-begäran till en ”push-style” modell av utfärda ett stort antal länka krediter och ta emot meddelanden när de blir tillgängliga utan några ytterligare åtgärder. Push stöds via den [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) eller [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) egenskapsinställningar. När de är noll använder AMQP klienten den som kredit för länken.

Det är viktigt att förstå att klockan för förfallotiden för lås på meddelandet i entiteten startar när meddelandet hämtas från enheten inte när meddelandet placeras i uppkopplat läge i den här kontexten. När klienten visar kan ta emot meddelanden genom att utfärda länken kredit, förväntas den därför aktivt dra meddelanden i nätverket och är redo att hantera dem. Annars låset meddelandet kan ha gått ut innan meddelandet levereras även. Användning av länk kredit flödeskontroll återspegla direkt omedelbar är klar att hantera tillgängliga meddelanden som skickas till mottagaren.

Följande avsnitt innehåller en schema översikt över performative flödet under olika API interaktioner Sammanfattningsvis. I avsnitten beskrivs en annan logisk åtgärd. Vissa av dessa interaktioner kanske ”lazy”, vilket innebär att de kan endast utföras när det behövs. Skapar avsändarens kanske inte en nätverket interaktion tills det första meddelandet som skickas eller begärs.

Pilarna i följande tabell visar performative flödesriktning.

#### <a name="create-message-receiver"></a>Skapa meddelande mottagare

| Client | Service Bus |
| --- | --- |
| --> Bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**mottagare**,<br/>källa = {entitetsnamnet}<br/>Target = {klienten länken id}<br/>) |Klienten ansluter till enheten som mottagare |
| Service Bus-svar som kopplar slutet av länken |<--bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**avsändaren**,<br/>källa = {entitetsnamnet}<br/>Target = {klienten länken id}<br/>) |

#### <a name="create-message-sender"></a>Skapa avsändaren

| Client | Service Bus |
| --- | --- |
| --> Bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**avsändaren**,<br/>källa = {klienten länken id}<br/>Target = {entitetsnamnet}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**mottagare**,<br/>källa = {klienten länken id}<br/>Target = {entitetsnamnet}<br/>) |

#### <a name="create-message-sender-error"></a>Skapa avsändaren (fel)

| Client | Service Bus |
| --- | --- |
| --> Bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**avsändaren**,<br/>källa = {klienten länken id}<br/>Target = {entitetsnamnet}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--bifoga ()<br/>Name = {namn},<br/>Hantera = {numeriskt referensen}<br/>rollen =**mottagare**,<br/>källa = null<br/>Target = null<br/>)<br/><br/><--frånkoppling ()<br/>Hantera = {numeriskt referensen}<br/>stängd =**SANT**,<br/>fel = {felinformationen}<br/>) |

#### <a name="close-message-receiversender"></a>Stäng mottagare/avsändaren

| Client | Service Bus |
| --- | --- |
| --> frånkoppling ()<br/>Hantera = {numeriskt referensen}<br/>stängd =**SANT**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--frånkoppling ()<br/>Hantera = {numeriskt referensen}<br/>stängd =**SANT**<br/>) |

#### <a name="send-success"></a>Skicka (klart)

| Client | Service Bus |
| --- | --- |
| --> överföring (<br/>leverans-id = {numeriskt referensen}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**, mer =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>rollen = mottagaren.<br/>först = {leverans id}<br/>senaste = {leverans id}<br/>regleras =**SANT**,<br/>tillstånd =**accepteras**<br/>) |

#### <a name="send-error"></a>Skicka (fel)

| Client | Service Bus |
| --- | --- |
| --> överföring (<br/>leverans-id = {numeriskt referensen}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**, mer =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>rollen = mottagaren.<br/>först = {leverans id}<br/>senaste = {leverans id}<br/>regleras =**SANT**,<br/>tillstånd =**avvisade**()<br/>fel = {felinformationen}<br/>)<br/>) |

#### <a name="receive"></a>Ta emot

| Client | Service Bus |
| --- | --- |
| --> flöde (<br/>länken kredit = 1<br/>) |Ingen åtgärd |
| Ingen åtgärd |< transfer ()<br/>leverans-id = {numeriskt referensen}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**,<br/>fler =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |
| --> disposition (<br/>rollen =**mottagare**,<br/>först = {leverans id}<br/>senaste = {leverans id}<br/>regleras =**SANT**,<br/>tillstånd =**accepteras**<br/>) |Ingen åtgärd |

#### <a name="multi-message-receive"></a>Flera meddelandemottagning

| Client | Service Bus |
| --- | --- |
| --> flöde (<br/>länken kredit = 3<br/>) |Ingen åtgärd |
| Ingen åtgärd |< transfer ()<br/>leverans-id = {numeriskt referensen}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**,<br/>fler =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |
| Ingen åtgärd |< transfer ()<br/>leverans-id = {numeriskt referensen + 1}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**,<br/>fler =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |
| Ingen åtgärd |< transfer ()<br/>leverans-id = {numeriskt referensen + 2}<br/>leverans-tagg = {binära referensen}<br/>regleras =**FALSKT**,<br/>fler =**FALSKT**,<br/>tillstånd =**null**,<br/>återuppta =**FALSKT**<br/>) |
| --> disposition (<br/>rollen = mottagaren.<br/>först = {leverans id}<br/>senaste = {leverans id + 2}<br/>regleras =**SANT**,<br/>tillstånd =**accepteras**<br/>) |Ingen åtgärd |

### <a name="messages"></a>Meddelanden

I följande avsnitt beskrivs vilka egenskaper från standard AMQP meddelande avsnitt används av Service Bus och hur de mappas till Service Bus-API-uppsättningen.

Alla egenskaper som programmet behöver definierar ska mappas till AMQP'S `application-properties` kartan.

#### <a name="header"></a>sidhuvud

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| beständiga |- |- |
| prioritet |- |- |
| ttl |Livslängd för det här meddelandet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| första förvärvaren |- |- |
| Antal leverans |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| meddelande-id |Programdefinierade, fritt format identifierare för det här meddelandet. Används för identifiering av dubbletter. |[messageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| användar-id |Programdefinierade användaridentifierare inte tolkas av Service Bus. |Inte tillgängligt via Service Bus-API. |
| till |Programdefinierade Målidentifierare, inte tolkas av Service Bus. |[Till](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| Ämne |Tillämpningsdefinierade syfte identifierare inte tolkas av Service Bus. |[Etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| Svara till |Programdefinierade reply-path indikator, inte tolkas av Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| Korrelations-id |Programdefinierade korrelationsidentifierare, inte tolkas av Service Bus. |[correlationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| innehållstyp |Programdefinierade innehållstypen indikator för innehållet inte tolkas av Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| Innehållskodning |Programdefinierade Innehållskodning indikator för innehållet inte tolkas av Service Bus. |Inte tillgängligt via Service Bus-API. |
| absolut förfallotiden |Deklarerar på vilka absoluta snabbmeddelanden meddelandet upphör att gälla. Ignoreras på indata (sidhuvud TTL observeras), auktoritativ på utdata. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| Skapa en gång |Deklarerar då meddelandet skapades. Inte används av Service Bus |Inte tillgängligt via Service Bus-API. |
| grupp-id |Tillämpningsdefinierad identifierare för en uppsättning meddelanden. Används för Service Bus-sessioner. |[Sessions-ID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| grupp-sekvens |Räknaren identifierar relativa sekvensnumret för meddelandet i en session. Ignoreras av Service Bus. |Inte tillgängligt via Service Bus-API. |
| Svara till grupp-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

#### <a name="message-annotations"></a>Meddelandet anteckningar

Det finns några andra service bus meddelandeegenskaper som inte är en del av AMQP meddelandeegenskaper och skickas vidare som `MessageAnnotations` i meddelandet.

| Anteckningen kartan nyckel | Användning | API-namn |
| --- | --- | --- |
| x-opt-schemalagda-sätta-tid | Deklarerar då meddelandet ska visas på enheten |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt--partitionsnyckel | Programdefinierade nyckeln som bestämmer vilken partition meddelandet ska hamna i. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via--partitionsnyckel | Programdefinierade partitionsnyckel värde när en transaktion som ska användas för att skicka meddelanden via en kö för överföring. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-köas-tid | Tjänst-definierade UTC-tid som motsvarar den faktiska tiden för enqueuing meddelandet. Ignoreras på indata. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt--sekvensnummer | Tjänst-definierade unikt nummer som tilldelas ett meddelande. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x välja förskjutning | Tjänst-definierade köas sekvensnumret för meddelandet. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-låst-tills | Tjänst-definierade. Datum och tid då meddelandet kommer att låsas i kön eller prenumeration. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-systemkön-källa | Tjänst-definierade. Om meddelandet tas emot från en kö med olevererade brev, källan till det ursprungliga meddelandet. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Kapaciteten för transaktion

En transaktion grupperar två eller flera åtgärder tillsammans i en körning omfattning. Enligt natur har sådana en transaktion måste se till att alla åtgärder som tillhör en viss grupp av åtgärder lyckas eller misslyckas gemensamt.
Åtgärderna är grupperade efter en identifierare som `txn-id`.

För transaktionell interaktion klienten fungerar en `transaction controller` som styr åtgärder som ska grupperas. Service Bus-tjänsten fungerar som en `transactional resource` och utför arbete som begärs av den `transaction controller`.

Klienten och tjänsten kommunicerar via en `control link` som upprättas av klienten. Den `declare` och `discharge` meddelanden skickas av controller med länken kontrollen att allokera och slutföra transaktioner respektive (faktisk avgränsning av transaktionella arbete). Faktiska skicka och ta emot har inte utförts på den här länken. Varje transaktionella den begärda åtgärden är uttryckligen anges med den önskade `txn-id` och därför kan inträffa på en länk på anslutningen. Om kontrollen länken är stängd när det finns inte avslutat transaktioner som den har skapats sedan alla transaktioner återställs omedelbart och försöker utföra ytterligare transaktionella arbete på dem kommer att leda till fel. Meddelanden på kontrollen länk får inte vara före lösas.

Varje anslutning måste initiera en egen kontroll av länk för att kunna börja och sluta transaktioner. Tjänsten definierar ett särskilt mål som fungerar som en `coordinator`. Klienten/controller upprättar en kontroll länk till det här målet. Kontroll av länk är utanför gränsen för en entitet, det vill säga, samma kontroll länk kan användas för att initiera och fullgöra transaktioner för flera enheter.

#### <a name="starting-a-transaction"></a>Starta en transaktion

Du vill börja transaktionella arbete. styrenheten måste få en `txn-id` från koordinatorn. Detta åstadkoms genom att skicka en `declare` Skriv ett meddelande. Om deklarationen lyckas koordinatorn svarar med en disposition resultatet av `declared` som innehåller den tilldelade `txn-id`.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| Koppla)<br/>Name = {namn},<br/>... ,<br/>rollen =**avsändaren**,<br/>Target =**Coordinator**<br/>) | ------> |  |
|  | <------ | Koppla)<br/>Name = {namn},<br/>... ,<br/>Target=Coordinator()<br/>) |
| överföra)<br/>leverans-id = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>tillstånd =**Declared**()<br/>**txn-id**= {transaktions-id}<br/>))|

#### <a name="discharging-a-transaction"></a>Avsluta en transaktion

Domänkontrollanten kommer att sluta transaktionella arbete genom att skicka en `discharge` meddelande till koordinatorn. Domänkontrollanten som anger att den önskar bekräfta eller ångra transaktionella arbete genom att ange den `fail` flaggan på avslutning brödtext. Om koordinatorn kan inte slutföra utsläpp avvisades meddelandet med det här resultatet av `transaction-error`.

> Obs: misslyckas = true refererar till återställning av en transaktion och misslyckas = false refererar till Commit.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| överföra)<br/>leverans-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>tillstånd = deklarerade (<br/>txn-id = {transaktions-id}<br/>))|
| | . . . <br/>Transaktionell arbete<br/>på andra länkar<br/> . . . |
| överföra)<br/>leverans-id = 57,...)<br/>{AmqpValue)<br/>**Fullgöra (txn-id = 0,<br/>misslyckas = false)**)}| ------> |  |
| | <------ | disposition ( <br/> först = 57, senast = 57, <br/>tillstånd =**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Skicka ett meddelande i en transaktion

Alla transaktionella arbetet med tillståndet transaktionella leverans `transactional-state` som utför txn-id. För att skicka meddelanden utförs i transaktionstillstånd av meddelandets överföring ram. 

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| överföra)<br/>leverans-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>tillstånd = deklarerade (<br/>txn-id = {transaktions-id}<br/>))|
| överföra)<br/>Hantera = 1,<br/>leverans-id = 1, <br/>**tillstånd =<br/>TransactionalState (<br/>txn-id = 0)**)<br/>{nyttolast}| ------> |  |
| | <------ | disposition ( <br/> först = 1, senast = 1, <br/>tillstånd =**TransactionalState (<br/>txn-id = 0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Avyttra ett meddelande i en transaktion

Meddelandet disposition innehåller åtgärder som `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Om du vill utföra dessa åtgärder i en transaktion, skickar den `transactional-state` med dispositionen.

| Klienten (Controller) | | Service Bus (Coordinator) |
| --- | --- | --- |
| överföra)<br/>leverans-id = 0,...)<br/>{AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition ( <br/> först = 0, senast = 0, <br/>tillstånd = deklarerade (<br/>txn-id = {transaktions-id}<br/>))|
| | <------ |överföra)<br/>Hantera = 2,<br/>leverans-id = 11, <br/>tillstånd = null)<br/>{nyttolast}|  
| disposition ( <br/> först = 11, senast = 11, <br/>tillstånd =**TransactionalState (<br/>txn-id = 0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Avancerade funktioner för Service Bus

Det här avsnittet beskriver de avancerade funktionerna i Azure Service Bus som baseras på ett utkast till tillägg till AMQP, för närvarande under utveckling i den tekniska kommittén OASIS för AMQP. Service Bus implementerar de senaste versionerna av dessa utkast och tillämpar ändringar som dessa utkast nå standard status.

> [!NOTE]
> Service Bus-meddelanden avancerade åtgärder stöds via ett mönster i begäran och svar. Information om de här åtgärderna beskrivs i artikeln [AMQP 1.0 i Service Bus: begäran-svar-baserade operations](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP management

AMQP management specifikation är först av utkast-tillägg som beskrivs i den här artikeln. Den här specifikationen definierar en uppsättning lager ovanpå AMQP-protokollet som tillåter hanteringsinteraktioner med meddelandeinfrastrukturen via AMQP. Specifikationen definierar allmänna åtgärder som *skapa*, *läsa*, *uppdatera*, och *ta bort* för att hantera enheter i en infrastruktur för meddelanden och en uppsättning frågor.

Dessa gester kräver en begäran och svar interaktion mellan klienten och meddelandeinfrastrukturen och därför specifikationen definierar hur modellera interaktion mönstret ovanpå AMQP: klienten ansluter till meddelandeinfrastrukturen initierar en session och skapar sedan ett par med länkar. Klienten fungerar som avsändaren på en länk och det andra den fungerar som mottagare, vilket skapar ett par med länkar som kan fungera som en dubbelriktad kanal.

| Logisk åtgärd | Client | Service Bus |
| --- | --- | --- |
| Skapa begäran svar sökvägen |--> Bifoga ()<br/>name = {*länknamn*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**avsändaren**,<br/>källa =**null**,<br/>Target = ”myentity / $management”<br/>) |Ingen åtgärd |
| Skapa begäran svar sökvägen |Ingen åtgärd |\<--bifoga ()<br/>name = {*länknamn*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**mottagare**,<br/>källa = null<br/>Target = ”myentity”<br/>) |
| Skapa begäran svar sökvägen |--> Bifoga ()<br/>name = {*länknamn*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**mottagare**,<br/>källa = ”myentity / $management”<br/>Target = ”myclient$-ID.<br/>) | |
| Skapa begäran svar sökvägen |Ingen åtgärd |\<--bifoga ()<br/>name = {*länknamn*},<br/>hantera = {*numeriska referensen*},<br/>rollen =**avsändaren**,<br/>källa = ”myentity”<br/>Target = ”myclient$-ID.<br/>) |

Att ha paret med länkar, frågor och svar-implementeringen är enkla: en begäran är ett meddelande som skickas till en entitet i meddelandeinfrastrukturen som kan användas med det här mönstret. I det begärandemeddelandet den *svar till* i den *egenskaper* delen är inställd på den *mål* identifierare för den länk som kan leverera svaret. Hantering av entiteten behandlar begäran och sedan leverera svaret via länken vars *mål* identifierare som matchar de angivna *svar till* identifierare.

Mönstret naturligtvis kräver att klienten behållaren och klienten genererade identifieraren för mål-svar är unikt över alla klienter och av säkerhetsskäl bör också svårt att förutsäga.

Meddelandeutbyten som används för management-protokollet och för alla andra protokoll som använder samma mönster som sker på programnivå; de definierar inte nya AMQP protokollnivå gester. Det är avsiktlig, så att program kan dra omedelbar nytta av dessa tillägg med kompatibla AMQP 1.0-stackar.

Service Bus för närvarande implementerar inte någon av kärnfunktioner specifikationen management, men begäran och svar mönstret som definieras i management-specifikationen är grundläggande för funktionen anspråk baserade säkerhet och för nästan alla de avancerade funktioner som beskrivs i följande avsnitt.

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

AMQP anspråk baserad auktorisering (CBS) specifikation utkastet bygger på management specifikation begäran och svar mönstret och beskriver en generaliserad modell för hur du använder externa säkerhetstoken med AMQP.

Standard-säkerhetsmodellen i AMQP som beskrevs i inledningen baseras på SASL och integreras med AMQP anslutning handskakningen. SASL har fördelen att det tillhandahåller en utökningsbar modell som en uppsättning metoder har definierats från alla protokoll som formellt leans på SASL kan dra. Bland dessa mekanismer är ”OFORMATERAD” för överföring av användarnamn och lösenord som ”externa” att binda till TLS-level security, ”anonym” för att uttrycka avsaknaden av explicit autentisering/auktorisering och ett stort antal ytterligare mekanismer som tillåter autentisering och/eller autentiseringsuppgifter eller token.

AMQP'S SASL integrering har två nackdelar:

* Alla autentiseringsuppgifter och token är begränsade till anslutningen. En meddelandeinfrastruktur kanske vill ge differentierade åtkomstkontroll på grundval av per enhet; till exempel tillåta att innehavare av en token att skicka till kön A men inte till kön B. Med autentiseringskontexten anslutningen som bas, går det inte att använda en enda anslutning och ännu olika åtkomsttoken för kö A och kön B.
* Åtkomst-token gäller vanligtvis endast för en begränsad tid. Denna giltighet kräver att användaren regelbundet återfå token och ger en möjlighet att token utfärdaren avslå utfärda en ny token om användarens åtkomstbehörighet har ändrats. AMQP anslutningar kan det sista under långa perioder. SASL modellen bara ger möjlighet att ange en token vid anslutningen, vilket innebär att meddelandeinfrastrukturen antingen har du koppla från klienten när token upphör att gälla eller som behövs för att godta risken för fortsatt kommunikation med en klient som har behörighet som krävs kan ha återkallats under tiden.

AMQP CBS-specifikationen som implementeras av Service Bus, kan en elegant lösning för båda dessa problem: Det gör att en klient att associera åtkomsttoken med varje nod och uppdatera de tokens innan de upphör att gälla, utan att avbryta flödet för meddelandet.

CBS definierar en virtuell nod med namnet *$cbs*, för att tillhandahållas av meddelandeinfrastrukturen. Noden hantering accepterar token för andra noder i meddelandeinfrastrukturen.

Protokollet gest är en request/reply-exchange som definieras i management-specifikationen. Att innebär klienten upprättar ett par med länkar med den *$cbs* nod och skickar sedan en förfrågan på utgående länken och väntar sedan svaret på inkommande länken.

Begärandemeddelandet har följande egenskaper för programmet:

| Nyckel | Valfri | Värdetyp | Värdet innehållet |
| --- | --- | --- | --- |
| åtgärden |Nej |sträng |**PUT-token** |
| typ |Nej |sträng |Typ av token som är put. |
| namn |Nej |sträng |Den ”målgruppen” som gäller för token. |
| upphörande |Ja |tidsstämpel |Förfallotid för token. |

Den *namn* egenskapen identifierar entiteten med vilket token vara associerat. Det är sökvägen till kön eller ämne /-prenumeration i Service Bus. Den *typen* egenskapen identifierar token-typer:

| Tokentypen | Token beskrivning | Brödtexten | Anteckningar |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |AMQP värde (sträng) |Ännu ej tillgänglig. |
| amqp:SWT |Enkel Web Token (SWT) |AMQP värde (sträng) |Stöds endast för SWT token som utfärdas av AAD/ACS |
| servicebus.Windows.NET:sastoken |Service Bus SAS-Token |AMQP värde (sträng) |- |

Token ge rättigheter. Service Bus medveten om tre grundläggande rättigheter: ”skicka” gör det möjligt att skicka ”lyssna” kan ta emot och ”hantera” gör att manipulera entiteter. SWT token som utfärdats av AAD/ACS explicit med dessa rättigheter som anspråk. Service Bus SAS-token avser regler som konfigurerats på namnområdet eller enhet och dessa regler är konfigurerade med rättigheter. Signera token med den nyckel som är associerade med regeln vilket gör att token snabb respektive rättigheter. Den token som är associerade med en entitet med *put-token* tillåter ansluten klient kan interagera med entiteten per token rättigheter. En länk där klienten tar på sig den *avsändaren* rollen kräver ”skicka” höger; tar den *mottagare* rollen kräver ”lyssna” höger.

Svarsmeddelandet har följande *egenskaper för program* värden

| Nyckel | Valfri | Värdetyp | Värdet innehållet |
| --- | --- | --- | --- |
| statuskod |Nej |int |HTTP-svarskoden **[RFC2616]**. |
| Beskrivning av status |Ja |sträng |Beskrivning av status. |

Klienten kan anropa *put-token* upprepade gånger och för varje entitet i meddelandeinfrastrukturen. Token är begränsade till den aktuella klienten och fäst på den aktuella anslutningen, vilket innebär att servern utelämnar eventuella tokens som behålls när anslutningen bryts.

Den aktuella Service Bus-implementeringen kan bara CBS tillsammans med metoden SASL ”anonym”. En anslutning för SSL/TLS måste alltid finnas före SASL-handskakningen.

Mekanismen för anonym måste därför stödjas av valda AMQP 1.0-klienten. Anonym åtkomst innebär att handskakningen inledande anslutningen, inklusive att skapa den första sessionen sker utan Service Bus att veta vem som skapar anslutningen.

När anslutningen och sessionen har upprättats kan du bifoga länkar till den *$cbs* nod och skicka den *put-token* begäran är den enda tillåtna åtgärder. En giltig token måste anges med en *put-token* begäran för vissa entitet nod inom 20 sekunder när anslutningen har upprättats, annars anslutningen ensidigt bryts av Service Bus.

Klienten ansvarar senare för att hålla reda på token upphör att gälla. När en token upphör att gälla utelämnar Service Bus omedelbart alla länkar i anslutning till respektive entiteten. Om du vill förhindra detta klienten Ersätt token för noden med en ny när som helst via den virtuella *$cbs* hanteringsnod med samma *put-token* rörelser och utan hämtar form nyttolast-trafik som flödar på olika länkar.

### <a name="send-via-functionality"></a>Skicka via funktioner

[Skicka via / Transfer avsändaren](service-bus-transactions.md#transfers-and-send-via) är en funktion som gör att service bus vidarebefordra ett visst meddelande till målentitet via en annan entitet. Det här används huvudsakligen för att utföra åtgärder för entiteter i en enda transaktion.

Med den här funktionen kan du skapa en avsändare och upprätta en länk till den `via-entity`. Ytterligare information överförs under etablering av länken för att upprätta true målet meddelanden/överföringar på den här länken. När det har lyckats, alla meddelanden som skickas på den här länken automatiskt ska vidarebefordras till den *målentitet* via *via entiteten*. 

> Obs: Autentisering måste utföras för båda *via entiteten* och *målentitet* innan du upprättar den här länken.

| Client | | Service Bus |
| --- | --- | --- |
| Koppla)<br/>Name = {namn},<br/>rollen = avsändarens<br/>källa = {klienten länken id}<br/>Target =**{via entiteten}**,<br/>**Egenskaper för kartan = [(<br/>com.microsoft:transfer måladress =<br/>{målentitet})]** ) | ------> | |
| | <------ | Koppla)<br/>Name = {namn},<br/>rollen = mottagaren.<br/>källa = {klienten länken id}<br/>Target = {via-entiteten}<br/>Egenskaper för kartan [(=<br/>com.Microsoft:Transfer måladress =<br/>{målentitet})] ) |

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-stöd för Service Bus partitionerad köer och ämnen]
* [AMQP i Service Bus för Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-stöd för Service Bus partitionerad köer och ämnen]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP i Service Bus för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
