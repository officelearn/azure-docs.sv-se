---
title: AMQP 1.0 i Azure Service Bus och Event Hubs protokollguide | Microsoft-dokument
description: Protokollguide till uttryck och beskrivning av AMQP 1.0 i Azure Service Bus and Event Hubs
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
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086144"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 i azure service bus och event hubs protokollguide

Advanced Message Queueing Protocol 1.0 är ett standardiserat inramnings- och överföringsprotokoll för asynkront, säkert och tillförlitligt överföra meddelanden mellan två parter. Det är det primära protokollet för Azure Service Bus Messaging och Azure Event Hubs. Båda tjänsterna stöder också HTTPS. Det egna SBMP-protokollet som också stöds håller på att fasas ut till förmån för AMQP.

AMQP 1.0 är resultatet av ett brett branschsamarbete som samlade mellanprogramleverantörer, såsom Microsoft och Red Hat, med många meddelandetjänster mellanprogram användare som JP Morgan Chase som representerar den finansiella tjänstesektorn. Den tekniska standardisering forum för AMQP protokoll och specifikationer förlängning är OASIS, och det har uppnått formellt godkännande som en internationell standard som ISO / IEC 19494.

## <a name="goals"></a>Mål

I den här artikeln sammanfattas kortfattat de centrala begreppen i meddelandespecifikationen AMQP 1.0 tillsammans med en liten uppsättning utkast till tilläggsspecifikationer som för närvarande slutförs i DEN TEKNISKA KOMMITTÉN OASIS AMQP och förklarar hur Azure Service Bus och bygger på dessa specifikationer.

Målet är att alla utvecklare som använder en befintlig AMQP 1.0-klientstack på valfri plattform ska kunna interagera med Azure Service Bus via AMQP 1.0.

Vanliga AMQP 1.0-stackar för allmänna ändamål, till exempel Apache Proton eller AMQP.NET Lite, implementerar redan alla centrala AMQP 1.0-protokoll. Dessa grundläggande gester är ibland insvept med en högre nivå API; Apache Proton erbjuder även två, den tvingande Messenger API och den reaktiva Reactor API.

I följande diskussion antar vi att hanteringen av AMQP-anslutningar, sessioner och länkar och hantering av ramöverföringar och flödeskontroll hanteras av respektive stack (till exempel Apache Proton-C) och inte kräver mycket om någon särskild uppmärksamhet från applikationsutvecklare. Vi antar abstrakt förekomsten av några API primitiver som förmågan att ansluta, och *receiver* att skapa någon form av `send()` *avsändare* och mottagare abstraktion objekt, som sedan har viss form av och `receive()` operationer, respektive.

När du diskuterar avancerade funktioner i Azure Service Bus, till exempel meddelandebläddring eller hantering av sessioner, förklaras dessa funktioner i AMQP-termer, men också som en pseudoimplementering i flera lager ovanpå den här förmodade API-abstraktionen.

## <a name="what-is-amqp"></a>Vad är AMQP?

AMQP är ett inramnings- och överföringsprotokoll. Inramning innebär att det ger struktur för binära dataströmmar som flödar i båda riktningarna av en nätverksanslutning. Strukturen ger avgränsning för olika datablock, så kallade *ramar,* som ska utbytas mellan de anslutna parterna. Överföringskapaciteten ser till att båda kommunicerande parterna kan skapa en gemensam förståelse för när ramar ska överföras, och när överföringar ska anses vara fullständiga.

Till skillnad från tidigare utgångna utkast versioner som produceras av AMQP arbetsgrupp som fortfarande används av några meddelande mäklare, arbetsgruppens slutliga, och standardiserade AMQP 1.0 protokoll föreskriver inte förekomsten av en meddelandemäklare eller någon särskild topologi för enheter i en meddelandemäklare.

Protokollet kan användas för symmetrisk peer-to-peer-kommunikation, för interaktion med meddelandemäklare som stöder köer och publicera/prenumererande entiteter, som Azure Service Bus gör. Den kan också användas för interaktion med meddelandeinfrastruktur där interaktionsmönstren skiljer sig från vanliga köer, vilket är fallet med Azure Event Hubs. En eventhubb fungerar som en kö när händelser skickas till den, men fungerar mer som en seriell lagringstjänst när händelser läss från den. det liknar något en bandenhet. Klienten väljer en förskjutning till den tillgängliga dataströmmen och visas sedan alla händelser från den förskjutningen till den senast tillgängliga.

AMQP 1.0-protokollet är utformat för att vara utbyggbart, vilket möjliggör ytterligare specifikationer för att förbättra dess kapacitet. De tre tilläggsspecifikationer som diskuteras i detta dokument illustrerar detta. För kommunikation över befintlig HTTPS/WebSockets-infrastruktur kan det vara svårt att konfigurera de inbyggda AMQP TCP-portarna. En bindningsspecifikation definierar hur AMQP ska lagerställas över WebSockets. För att interagera med meddelandeinfrastrukturen på ett begärans-/svarssätt för hanteringsändamål eller för att tillhandahålla avancerade funktioner, definierar AMQP-hanteringsspecifikationen de grundläggande interaktionsprimitiver som krävs. För integrering av federerade auktoriseringsmodeller definierar AMQP-anspråksbaserad säkerhetsspecifikation hur du associerar och förnyar auktoriseringstoken som är associerade med länkar.

## <a name="basic-amqp-scenarios"></a>Grundläggande AMQP-scenarier

I det här avsnittet beskrivs den grundläggande användningen av AMQP 1.0 med Azure Service Bus, som omfattar att skapa anslutningar, sessioner och länkar och överföra meddelanden till och från Service Bus-entiteter som köer, ämnen och prenumerationer.

Den mest auktoritativa källan att lära sig om hur AMQP fungerar är AMQP 1.0 specifikationen, men specifikationen skrevs för att exakt vägleda genomförandet och inte att lära protokollet. Det här avsnittet fokuserar på att införa så mycket terminologi som behövs för att beskriva hur Service Bus använder AMQP 1.0. För en mer omfattande introduktion till AMQP, samt en bredare diskussion om AMQP 1.0, kan du granska [denna video kurs][this video course].

### <a name="connections-and-sessions"></a>Anslutningar och sessioner

AMQP anropar de kommunicerande *programbehållarna*; de innehåller *noder*, som är de kommunicerande enheterna inuti dessa behållare. En kö kan vara en sådan nod. AMQP möjliggör multiplexering, så en enda anslutning kan användas för många kommunikationsvägar mellan noder. En programklient kan till exempel samtidigt ta emot från en kö och skicka till en annan kö via samma nätverksanslutning.

![][1]

Nätverksanslutningen är därmed förankrad i behållaren. Den initieras av behållaren i klientrollen som gör en utgående TCP-socketanslutning till en behållare i mottagarrollen, som lyssnar efter och accepterar inkommande TCP-anslutningar. Anslutningskakningen omfattar förhandlingar om protokollversionen, deklarera eller förhandla om användning av transportnivåsäkerhet (TLS/SSL) och ett handslag för autentisering/auktorisering vid anslutningsomfånget som baseras på SASL.

Azure Service Bus kräver användning av TLS hela tiden. Den stöder anslutningar över TCP-port 5671, där TCP-anslutningen först överlagras med TLS innan amqp-protokollet handskakning, och stöder även anslutningar över TCP-port 5672 där servern omedelbart erbjuder en obligatorisk uppgradering av anslutningen TLS med hjälp av den AMQP-föreskrivna modellen. AMQP WebSockets-bindningen skapar en tunnel över TCP-port 443 som sedan motsvarar AMQP 5671-anslutningar.

När du har konfigurerat anslutningen och TLS erbjuder Service Bus två SASL-alternativ:

* SASL PLAIN används ofta för att skicka användarnamn och lösenord till en server. Service Bus har inga konton, men med namnet [Säkerhetsregler för delad åtkomst](service-bus-sas.md), som ger rättigheter och är associerade med en nyckel. Namnet på en regel används som användarnamn och nyckeln (som base64-kodad text) används som lösenord. De rättigheter som är associerade med den valda regeln styr de åtgärder som tillåts på anslutningen.
* SASL ANONYMOUS används för att kringgå SASL-auktorisering när klienten vill använda den anspråksbaserade säkerhetsmodellen (CBS) som beskrivs senare. Med det här alternativet kan en klientanslutning upprättas anonymt under en kort tid under vilken klienten bara kan interagera med CBS-slutpunkten och CBS-handskakningen måste slutföras.

När transportanslutningen har upprättats deklarerar behållarna var och en den maximala bildrutestorlek som de är villiga att hantera, och efter en inaktiv timeout kopplar de ensidigt bort om det inte finns någon aktivitet på anslutningen.

De deklarerar också hur många samtidiga kanaler som stöds. En kanal är en enkelriktad, utgående, virtuell överföringssökväg ovanpå anslutningen. En session tar en kanal från var och en av de sammankopplade behållarna för att skapa en dubbelriktad kommunikationsväg.

Sessioner har en fönsterbaserad flödeskontrollmodell. När en session skapas deklarerar varje part hur många bildrutor den är villig att acceptera i mottagningsfönstret. När parterna utbyter ramar fyller överförda ramar fönstret och överföringarna stoppas när fönstret är fullt och tills fönstret återställs eller utökas med hjälp av *flödets performativa* (*performativ* är AMQP-termen för protokollnivågester som utbyts mellan de två parterna).

Den här fönsterbaserade modellen är ungefär jämförbar med TCP-konceptet med fönsterbaserad flödeskontroll, men på sessionsnivå inuti uttaget. Protokollets koncept att tillåta flera samtidiga sessioner finns så att hög prioritet trafik kan skyndas förbi strypt normal trafik, som på en motorväg express körfält.

Azure Service Bus använder för närvarande exakt en session för varje anslutning. ServiceBussens maximala ramstorlek är 262 144 byte (256 K byte) för Service Bus Standard och Event Hubs. Det är 1.048.576 (1 MB) för Service Bus Premium. Service Bus innebär inte några särskilda strypningsfönster på sessionsnivå, men återställer fönstret regelbundet som en del av flödeskontroll på länknivå (se [nästa avsnitt](#links)).

Anslutningar, kanaler och sessioner är efemära. Om den underliggande anslutningen komprimeras måste anslutningar, TLS-tunnel, SASL-auktoriseringskontext och sessioner återupprättas.

### <a name="amqp-outbound-port-requirements"></a>AMQP-krav på utgående port

Klienter som använder AMQP-anslutningar via TCP kräver att portarna 5671 och 5672 öppnas i den lokala brandväggen. Tillsammans med dessa portar kan det vara nödvändigt att öppna ytterligare portar om [EnableLinkRedirect-funktionen](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) är aktiverad. `EnableLinkRedirect`är en ny meddelandefunktion som hjälper till att hoppa över en hopp medan du tar emot meddelanden, vilket bidrar till att öka dataflödet. Klienten skulle börja kommunicera direkt med backend-tjänsten över portintervall 104XX som visas i följande bild. 

![Lista över målportar][4]

En .NET-klient skulle misslyckas med en SocketException ("Ett försök gjordes att komma åt en socket på ett sätt som är förbjudet av dess åtkomstbehörigheter") om dessa portar blockeras av brandväggen. Funktionen kan inaktiveras `EnableAmqpLinkRedirect=false` genom att ställa in i connectiong-strängen, vilket tvingar klienterna att kommunicera med fjärrtjänsten över port 5671.


### <a name="links"></a>Länkar

AMQP överför meddelanden via länkar. En länk är en kommunikationsväg som skapats under en session som gör det möjligt att överföra meddelanden i en riktning. förhandlingarna om överföringsstatus är över länken och dubbelriktad mellan de anslutna parterna.

![][2]

Länkar kan skapas av endera behållaren när som helst och under en befintlig session, vilket gör att AMQP skiljer sig från många andra protokoll, inklusive HTTP och MQTT, där inledandet av överföringar och överföringssökvägen är ett exklusivt privilegium för den part som skapar uttagsanslutning.

Den länkinitierande behållaren ber den motsatta behållaren att acceptera en länk och väljer en roll som antingen avsändare eller mottagare. Därför kan antingen behållaren initiera att skapa enkelriktade eller dubbelriktade kommunikationsvägar, med den senare modelleras som länkar.

Länkar namnges och associeras med noder. Som anges i början är noder de kommunicerande entiteterna i en behållare.

I Service Bus motsvarar en nod direkt en kö, ett ämne, en prenumeration eller en underkö för en kö eller prenumeration. Nodnamnet som används i AMQP är därför det relativa namnet på entiteten i servicebussnamnområdet. Om en kö `myqueue`heter är det också dess AMQP-nodnamn. En ämnesprenumeration följer HTTP API-konventionen genom att sorteras i en "prenumerationer" resurssamling och därmed en **prenumerationsunder på** ett ämne **mytopic** har AMQP-noden namnet **mytopic/subscriptions/sub**.

Anslutningsklienten måste också använda ett lokalt nodnamn för att skapa länkar. Service Bus är inte föreskrivande om dessa nodnamn och tolkar dem inte. AMQP 1.0-klientstaplar använder vanligtvis ett schema för att säkerställa att dessa efemära nodnamn är unika i klientens omfattning.

### <a name="transfers"></a>Överföringar

När en länk har upprättats kan meddelanden överföras via den länken. I AMQP körs en överföring med en explicit *transfer* protokollgest (överföringsutövning) som flyttar ett meddelande från avsändare till mottagare via en länk. En överföring är klar när den är "avgjord", vilket innebär att båda parter har etablerat en gemensam förståelse för resultatet av denna överföring.

![][3]

I det enklaste fallet kan avsändaren välja att skicka meddelanden "i förutreglerade", vilket innebär att klienten inte är intresserad av resultatet och mottagaren inte ger någon feedback om resultatet av åtgärden. Det här läget stöds av Service Bus på AMQP-protokollnivå, men inte exponeras i någon av klient-API:erna.

Det vanliga fallet är att meddelanden skickas oreglerade och mottagaren anger sedan godkännande eller avslag med hjälp av *dispositionen* performativ. Avslag sker när mottagaren inte kan acceptera meddelandet av någon anledning, och avvisningsmeddelandet innehåller information om orsaken, vilket är en felstruktur som definieras av AMQP. Om meddelanden avvisas på grund av interna fel i Service Bus returnerar tjänsten extra information inuti den struktur som kan användas för att ge diagnostiktips till supportpersonal om du skickar supportförfrågningar. Du lär dig mer information om fel senare.

En särskild form av avslag är den *frisläppta* staten, som visar att mottagaren inte har några tekniska invändningar mot överföringen, men inte heller något intresse av att lösa överföringen. Det fallet finns till exempel när ett meddelande levereras till en Service Bus-klient och klienten väljer att "överge" meddelandet eftersom det inte kan utföra det arbete som följer av bearbetningen av meddelandet. själva meddelandeleveransen inte är fel. En variant av det tillståndet är det *ändrade* tillståndet, vilket gör att meddelandet ändras när det släpps. Detta tillstånd används för närvarande inte av Service Bus.

AMQP 1.0-specifikationen definierar ytterligare ett dispositionstillstånd som kallas *mottaget*, som specifikt hjälper till att hantera länkåterställning. Länkåterställning gör det möjligt att återskapa tillståndet för en länk och eventuella väntande leveranser ovanpå en ny anslutning och session, när den tidigare anslutningen och sessionen förlorades.

Service Bus stöder inte länkåterställning. Om klienten förlorar anslutningen till Service Bus med en oreglerade meddelandeöverföring väntande, går meddelandeöverföringen förlorad och klienten måste återansluta, återupprätta länken och försöka överföra igen.

Service bus- och eventhubbar stöder därför "minst en gång" överföring där avsändaren kan garanteras för att meddelandet har lagrats och accepterats, men stöder inte "exakt en gång"-överföringar på AMQP-nivå, där systemet skulle försöka återställa länken och fortsätta att förhandla om leveranstillståndet för att undvika dubbelarbete av meddelandeöverföringen.

För att kompensera för eventuella dubblettsskick stöder Service Bus dubblettidentifiering som en valfri funktion i köer och ämnen. Dubblettidentifiering registrerar meddelande-ID:t för alla inkommande meddelanden under ett användardefinierat tidsfönster och släpper sedan tyst alla meddelanden som skickas med samma meddelande-ID under samma fönster.

### <a name="flow-control"></a>Flödeskontroll

Förutom flödeskontrollmodellen på sessionsnivå som tidigare diskuterats har varje länk en egen flödeskontrollmodell. Flödeskontroll på sessionsnivå skyddar behållaren från att behöva hantera för många bildrutor samtidigt, flödeskontroll på länknivå ger programmet ansvaret för hur många meddelanden den vill hantera från en länk och när.

![][4]

På en länk kan överföringar endast ske när avsändaren har tillräckligt med *länkkredit*. Länkkredit är en räknare som anges av mottagaren med hjälp av *flödet* performativ, som är begränsad till en länk. När avsändaren tilldelas länkkredit försöker den använda krediten genom att leverera meddelanden. Varje meddelande leverans decrements den återstående länken kredit med 1. När länkkrediten är upphör leveranserna.

När Service Bus är i mottagaren roll, ger det omedelbart avsändaren med gott om länk kredit, så att meddelanden kan skickas omedelbart. När länkkredit används skickar Service Bus *flow* ibland ett flödesuppnörande till avsändaren för att uppdatera länkkreditsaldot.

I avsändanderollen skickar Service Bus meddelanden för att använda eventuell utestående länkkrediter.

Ett "ta emot"-anrop på *flow* API-nivå översätts till ett flödesuppnjänande som skickas till Service Bus av klienten, och Service Bus förbrukar den krediten genom att ta det första tillgängliga, olåsta meddelandet från kön, låsa det och överföra den. Om det inte finns något meddelande lätt tillgängligt för leverans, förblir eventuell utestående kredit av någon länk som upprättats med den specifika enheten registreras i ankomstordning, och meddelanden låses och överförs när de blir tillgängliga för att använda eventuella utestående krediter.

Låset på ett meddelande frigörs när överföringen rättas i ett av de terminaltillstånd *som accepteras,* *avvisats*eller *släpps*. Meddelandet tas bort från Service Bus när terminaltillståndet *accepteras*. Den finns kvar i Service Bus och levereras till nästa mottagare när överföringen når någon av de andra staterna. Service Bus flyttar automatiskt meddelandet till entitetens deadletter-kö när det når det maximala antalet leveranser som tillåts för entiteten på grund av upprepade avslag eller utgåvor.

Även om Service Bus API:er inte direkt exponerar ett sådant alternativ idag, kan en amqp-protokollklient på lägre nivå använda länkkreditmodellen för att vända interaktionen "pull-style" mellan att utfärda en kreditenhet för varje begäran om mottagning till en "push-style"-modell genom att utfärda ett stort antal länkkrediter och sedan ta emot meddelanden när de blir tillgängliga utan ytterligare interaktion. Push stöds via inställningarna [För MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) eller [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) När de inte är noll använder AMQP-klienten den som länkkredit.

I det här sammanhanget är det viktigt att förstå att klockan för förfallodatum för låset på meddelandet inuti entiteten startar när meddelandet tas från entiteten, inte när meddelandet läggs på kabeln. När klienten anger beredskap att ta emot meddelanden genom att utfärda länkkrediter förväntas den därför aktivt dra meddelanden över nätverket och vara redo att hantera dem. Annars kan meddelandelåset ha upphört att gälla innan meddelandet ens levereras. Användningen av kontroll av länkkreditflödet bör direkt återspegla den omedelbara beredskapen att hantera tillgängliga meddelanden som skickas till mottagaren.

Sammanfattningsvis ger följande avsnitt en schematisk översikt över det performativa flödet under olika API-interaktioner. Varje avsnitt beskriver en annan logisk åtgärd. Vissa av dessa interaktioner kan vara "lata", vilket innebär att de endast kan utföras när det behövs. Om du skapar en meddelandeavsändare kanske det inte orsakar en nätverksinteraktion förrän det första meddelandet har skickats eller begärts.

Pilarna i följande tabell visar den performativa flödesriktningen.

#### <a name="create-message-receiver"></a>Skapa meddelandemottagare

| Client | Service Bus |
| --- | --- |
| --> bifoga(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**mottagare**,<br/>source={entitetsnamn},<br/>target={klientlänk-ID}<br/>) |Klienten kopplar till entiteten som mottagare |
| Service Bus svarar bifoga sin ände av länken |<... fäst(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**avsändare**,<br/>source={entitetsnamn},<br/>target={klientlänk-ID}<br/>) |

#### <a name="create-message-sender"></a>Skapa meddelandeavsändare

| Client | Service Bus |
| --- | --- |
| --> bifoga(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**avsändare**,<br/>source={klientlänk-ID},<br/>namn på target={entitet}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<... fäst(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**mottagare**,<br/>source={klientlänk-ID},<br/>namn på target={entitet}<br/>) |

#### <a name="create-message-sender-error"></a>Skapa meddelandeavsändare (fel)

| Client | Service Bus |
| --- | --- |
| --> bifoga(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**avsändare**,<br/>source={klientlänk-ID},<br/>namn på target={entitet}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<... fäst(<br/>name={link name},<br/>handle={numeriskt handtag},<br/>roll=**mottagare**,<br/>källa=null.<br/>target=null<br/>)<br/><br/><.<br/>handle={numeriskt handtag},<br/>stängd=**sant**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Stäng meddelandemottagare/avsändare

| Client | Service Bus |
| --- | --- |
| --> lossnar(<br/>handle={numeriskt handtag},<br/>stängd=**sant**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<.<br/>handle={numeriskt handtag},<br/>stängd=**sant**<br/>) |

#### <a name="send-success"></a>Skicka (framgång)

| Client | Service Bus |
| --- | --- |
| --> överföring(<br/>leverans-id={numerisk handtag},<br/>delivery-tag={binär handtag},<br/>fast=**falskt**,,mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<- disposition(<br/>role=mottagare.<br/>först={leverans-ID},<br/>senaste={leverans-ID},<br/>fast =**sant**,<br/>state=**accepterad**<br/>) |

#### <a name="send-error"></a>Skicka (fel)

| Client | Service Bus |
| --- | --- |
| --> överföring(<br/>leverans-id={numerisk handtag},<br/>delivery-tag={binär handtag},<br/>fast=**falskt**,,mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<- disposition(<br/>role=mottagare.<br/>först={leverans-ID},<br/>senaste={leverans-ID},<br/>fast =**sant**,<br/>state=**avvisas**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Ta emot

| Client | Service Bus |
| --- | --- |
| --> flöde(<br/>länk-kredit=1<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföring(<br/>leverans-id={numerisk handtag},<br/>delivery-tag={binär handtag},<br/>fast =**falskt**,<br/>mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |
| --> disposition(<br/>roll=**mottagare**,<br/>först={leverans-ID},<br/>senaste={leverans-ID},<br/>fast =**sant**,<br/>state=**accepterad**<br/>) |Ingen åtgärd |

#### <a name="multi-message-receive"></a>Ta emot flera meddelanden

| Client | Service Bus |
| --- | --- |
| --> flöde(<br/>länk-kredit=3<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföring(<br/>leverans-id={numerisk handtag},<br/>delivery-tag={binär handtag},<br/>fast =**falskt**,<br/>mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |
| Ingen åtgärd |< överföring(<br/>leverans-id={numeriskt handtag+1},<br/>delivery-tag={binär handtag},<br/>fast =**falskt**,<br/>mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |
| Ingen åtgärd |< överföring(<br/>leverans-id={numeriskt handtag+2},<br/>delivery-tag={binär handtag},<br/>fast =**falskt**,<br/>mer=**falskt**,<br/>tillstånd=**null**,<br/>återuppta =**falskt**<br/>) |
| --> disposition(<br/>role=mottagare.<br/>först={leverans-ID},<br/>senaste={leverans-ID+2},<br/>fast =**sant**,<br/>state=**accepterad**<br/>) |Ingen åtgärd |

### <a name="messages"></a>Meddelanden

I följande avsnitt beskrivs vilka egenskaper från standardavsnitten för AMQP-meddelanden som används av Service Bus och hur de mappas till SERVICE Bus API-uppsättningen.

Alla egenskaper som programmet behöver definiera ska mappas `application-properties` till AMQP:s karta.

#### <a name="header"></a>sidhuvud

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| Hållbara |- |- |
| prioritet |- |- |
| ttl |Dags att leva för det här meddelandet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| första förvärvaren |- |- |
| leverans-antal |- |[LeveransPlats](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| meddelande-id |Programdefinierad, frihandsidentifierare för det här meddelandet. Används för dubblettidentifiering. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| användar-id |Programdefinierad användaridentifierare, som inte tolkas av Service Bus. |Inte tillgängligt via Service Bus API. |
| till |Programdefinierad målidentifierare, som inte tolkas av Service Bus. |[Att](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Ämne |Programdefinierad meddelandesyftesidentifierare, som inte tolkas av Service Bus. |[Etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| svara på |Indikator för programdefinierad svarssökväg, som inte tolkas av Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| korrelations-id |Programdefinierad korrelationsidentifierare, som inte tolkas av Service Bus. |[KorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| innehållstyp |Programdefinierad innehållstypsindikator för brödtexten, som inte tolkas av Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| innehållskodning |Programdefinierad innehållskodningsindikator för brödtexten, som inte tolkas av Service Bus. |Inte tillgängligt via Service Bus API. |
| absolut utgångstid |Deklarerar vid vilket absolut ögonblick meddelandet upphör att gälla. Ignoreras vid indata (huvud TTL observeras), auktoritärt vid utdata. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| skapande-tid |Deklarerar då meddelandet skapades. Används inte av Service Bus |Inte tillgängligt via Service Bus API. |
| grupp-ID |Programdefinierad identifierare för en relaterad uppsättning meddelanden. Används för servicebusssessioner. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| gruppsekvens |Räknare som identifierar meddelandets relativa sekvensnummer i en session. Ignoreras av Service Bus. |Inte tillgängligt via Service Bus API. |
| svara på grupp-id |- |[Svara TillSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Meddelandeanteckningar

Det finns få andra servicebussmeddelandeegenskaper, som inte ingår i AMQP-meddelandeegenskaper, och skickas vidare som `MessageAnnotations` på meddelandet.

| Kartnyckel för anteckning | Användning | API-namn |
| --- | --- | --- |
| x-opt-scheduled-enqueue-tid | Deklarerar vid vilken tidpunkt meddelandet ska visas på entiteten |[SchemalagdaEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-nyckel | Programdefinierad nyckel som avgör vilken partition meddelandet ska landa i. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-nyckel | Programdefinierat partitionsnyckelvärde när en transaktion ska användas för att skicka meddelanden via en överföringskö. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-tid | Tjänstdefinierad UTC-tid som representerar den faktiska tiden för att avgränsa meddelandet. Ignoreras vid indata. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sekvens-nummer | Servicedefinierat unikt nummer som tilldelats ett meddelande. | [Sekvensnummer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Meddelandes tjänstdefinierade enqueued-sekvensnummer. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-låst-tills | Servicedefinierad. Datum och tid tills meddelandet kommer att låsas i kön/prenumerationen. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-källa | Servicedefinierad. Om meddelandet tas emot från kön för obeställbara brev visas källan till det ursprungliga meddelandet. | [DeadLetterKälla](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transaktionskapacitet

En transaktion grupperar två eller flera åtgärder tillsammans i en körning. En sådan transaktion måste till sin natur säkerställa att alla transaktioner som tillhör en viss grupp av transaktioner antingen lyckas eller misslyckas gemensamt.
Åtgärderna grupperas efter en `txn-id`identifierare .

För transaktionsinteraktion fungerar `transaction controller` klienten som en , som styr de åtgärder som ska grupperas tillsammans. Service Bus Service `transactional resource` fungerar som en och `transaction controller`utför arbete som begärts av .

Klienten och tjänsten `control link` kommunicerar via en , som upprättas av kunden. `declare` Meddelandena `discharge` och skickas av styrenheten över kontrolllänken för att allokera respektive slutföra transaktioner (de representerar inte avgränsningen av transaktionsarbete). Den faktiska skicka/ta emot utförs inte på denna länk. Varje begärd transaktion identifieras uttryckligen `txn-id` med den önskade och kan därför förekomma på vilken länk som helst på Anslutningen. Om kontrolllänken stängs medan det finns icke-urladdade transaktioner som den skapade, återställs alla sådana transaktioner omedelbart och försök att utföra ytterligare transaktionsarbete på dem kommer att leda till fel. Meddelanden på kontrolllänken får inte vara avgjorda i förskott.

Varje anslutning måste initiera sin egen kontrolllänk för att kunna starta och avsluta transaktioner. Tjänsten definierar ett särskilt mål `coordinator`som fungerar som en . Klienten/styrenheten upprättar en kontrolllänk till det här målet. Kontrolllänken ligger utanför gränsen för en entitet, det vill än samma kontrolllänk, kan användas för att initiera och utföra transaktioner för flera entiteter.

#### <a name="starting-a-transaction"></a>Starta en transaktion

För att påbörja transaktionsarbete. den registeransvarige `txn-id` måste få en från samordnaren. Detta gör det `declare` genom att skicka ett typmeddelande. Om deklarationen lyckas svarar samordnaren med ett dispositionsresultat `txn-id`som bär det tilldelade .

| Klient (controller) | | Servicebuss (samordnare) |
| --- | --- | --- |
| bifoga(<br/>name={link name},<br/>... ,<br/>roll=**avsändare**,<br/>target=**Samordnare**<br/>) | ------> |  |
|  | <------ | bifoga(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| överföring(<br/>leverans-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> första=0, sista=0, <br/>state=**Deklarerad**(<br/>**txn-id**={transaktions-ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Urladdning av en transaktion

Kontrollanten avslutar transaktionsarbetet genom `discharge` att skicka ett meddelande till samordnaren. Styrenheten anger att den vill utföra eller rulla tillbaka `fail` transaktionsarbetet genom att ställa flaggan på utloppsorganet. Om samordnaren inte kan slutföra urladdningen avvisas meddelandet `transaction-error`med detta resultat som bär .

> Fel= misslyckas=sant refererar till Återställning av en transaktion och fail=false refererar till Commit.

| Klient (controller) | | Servicebuss (samordnare) |
| --- | --- | --- |
| överföring(<br/>leverans-id=0, ...)<br/>{ AmqpValue (Deklarera())}| ------> |  |
|  | <------ | disposition( <br/> första=0, sista=0, <br/>state=Deklarerad(<br/>txn-id={transaktions-ID}<br/>))|
| | . . . <br/>Transaktionsarbete<br/>på andra länkar<br/> . . . |
| överföring(<br/>leverans-id=57, ...)<br/>{ AmqpValue (<br/>**Urladdning(txn-id=0,<br/>misslyckas=falskt)**)}| ------> |  |
| | <------ | disposition( <br/> första=57, sista=57, <br/>state=**Accepterad()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Skicka ett meddelande i en transaktion

Allt transaktionsarbete utförs med transaktionsleveranstillståndet `transactional-state` som bär txn-id. När det gäller att skicka meddelanden överförs transaktionstillståndet av meddelandets överföringsram. 

| Klient (controller) | | Servicebuss (samordnare) |
| --- | --- | --- |
| överföring(<br/>leverans-id=0, ...)<br/>{ AmqpValue (Deklarera())}| ------> |  |
|  | <------ | disposition( <br/> första=0, sista=0, <br/>state=Deklarerad(<br/>txn-id={transaktions-ID}<br/>))|
| överföring(<br/>handtag=1.<br/>leverans-id=1. <br/>**tillstånd=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ Nyttolast }| ------> |  |
| | <------ | disposition( <br/> första=1, sista=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Inaktivera ett meddelande i en transaktion

Meddelandedispositionen `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`innehåller åtgärder som . Om du vill utföra dessa `transactional-state` åtgärder i en transaktion skickar du dispositionen med dispositionen.

| Klient (controller) | | Servicebuss (samordnare) |
| --- | --- | --- |
| överföring(<br/>leverans-id=0, ...)<br/>{ AmqpValue (Deklarera())}| ------> |  |
|  | <------ | disposition( <br/> första=0, sista=0, <br/>state=Deklarerad(<br/>txn-id={transaktions-ID}<br/>))|
| | <------ |överföring(<br/>handtag=2,<br/>leverans-id=11. <br/>state=null)<br/>{ Nyttolast }|  
| disposition( <br/> första=11, sista=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Avancerade servicebussfunktioner

Det här avsnittet beskriver avancerade funktioner för Azure Service Bus som baseras på utkast till tillägg till AMQP, som för närvarande utvecklas i OASIS tekniska kommitté för AMQP. Service Bus implementerar de senaste versionerna av dessa utkast och antar ändringar som införts när dessa utkast når standardstatus.

> [!NOTE]
> Avancerade åtgärder för Service Bus Messaging stöds via ett svarsmönster för begäran/svar. Informationen om dessa åtgärder beskrivs i artikeln [AMQP 1.0 i Service Bus: request-response-based operations](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP-hantering

AMQP-hanteringsspecifikationen är den första av de utkast till tillägg som beskrivs i den här artikeln. Den här specifikationen definierar en uppsättning protokoll som finns ovanpå AMQP-protokollet som tillåter hanteringsinteraktioner med meddelandeinfrastrukturen över AMQP. Specifikationen definierar allmänna åtgärder som *att skapa*, *läsa,* *uppdatera*och *ta bort* för hantering av entiteter i en meddelandeinfrastruktur och en uppsättning frågeåtgärder.

Alla dessa gester kräver en interaktion mellan klienten och meddelandeinfrastrukturen, och därför definierar specifikationen hur du modellerar interaktionsmönstret ovanpå AMQP: klienten ansluter till meddelandeinfrastrukturen, initierar en session och skapar sedan ett par länkar. På en länk fungerar klienten som avsändare och å andra sidan fungerar den som mottagare, vilket skapar ett par länkar som kan fungera som en dubbelriktad kanal.

| Logisk åtgärd | Client | Service Bus |
| --- | --- | --- |
| Skapa sökväg för begäran svar |--> bifoga(<br/>name={*länknamn*},<br/>handle={*numeriskt handtag*},<br/>roll=**avsändare**,<br/>källa=**null**,<br/>target="myentity/$management"<br/>) |Ingen åtgärd |
| Skapa sökväg för begäran svar |Ingen åtgärd |\<-- fäst(<br/>name={*länknamn*},<br/>handle={*numeriskt handtag*},<br/>roll=**mottagare**,<br/>källa=null.<br/>target="myentity"<br/>) |
| Skapa sökväg för begäran svar |--> bifoga(<br/>name={*länknamn*},<br/>handle={*numeriskt handtag*},<br/>roll=**mottagare**,<br/>Källa="myentitet/$management".<br/>target="myclient$id"<br/>) | |
| Skapa sökväg för begäran svar |Ingen åtgärd |\<-- fäst(<br/>name={*länknamn*},<br/>handle={*numeriskt handtag*},<br/>roll=**avsändare**,<br/>källa="myentitet",<br/>target="myclient$id"<br/>) |

Med det par länkar på plats, begäran / svar genomförandet är enkelt: en begäran är ett meddelande som skickas till en entitet i meddelandeinfrastruktur som förstår detta mönster. I det meddelande om begäran ställs *svarsfältet* i *egenskapsavsnittet* in på *målidentifieraren* för länken som svaret ska levereras till. Hanteringsentiteten bearbetar begäran och levererar sedan *target* svaret över länken vars målidentifierare matchar den angivna *svarsidentifieraren.*

Mönstret kräver naturligtvis att klientbehållaren och klientgenererade identifieraren för svarsmålet är unika för alla klienter och, av säkerhetsskäl, också svårt att förutsäga.

Meddelandeutbyten som används för hanteringsprotokollet och för alla andra protokoll som använder samma mönster sker på programnivå. De definierar inte nya AMQP-protokollnivågester. Det är avsiktligt, så att program kan dra omedelbar nytta av dessa tillägg med kompatibla AMQP 1.0 stackar.

Service Bus implementerar för närvarande inte någon av huvudfunktionerna i hanteringsspecifikationen, men det begäran/svarsmönster som definieras av hanteringsspecifikationen är grundläggande för den skadebaserade säkerhetsfunktionen och för nästan alla avancerade funktioner som diskuteras i följande avsnitt:

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

AMQP Claims-Based-Authorization (CBS) specifikation utkast bygger på hanteringsspecifikationen begäran / svar mönster och beskriver en generaliserad modell för hur man använder federerade säkerhetstoken med AMQP.

Standardsäkerhetsmodellen för AMQP som beskrivs i inledningen baseras på SASL och integreras med AMQP-anslutningshandskakningen. Att använda SASL har fördelen att det ger en utökningsbar modell för vilken en uppsättning mekanismer har definierats från vilka alla protokoll som formellt lutar sig mot SASL kan dra nytta av. Bland dessa mekanismer är "PLAIN" för överföring av användarnamn och lösenord, "EXTERN" för att binda till TLS-nivå säkerhet, "ANONYM" för att uttrycka avsaknaden av explicit autentisering / auktorisering, och ett brett utbud av ytterligare mekanismer som tillåter passerar autentisering och / eller autentiseringsuppgifter auktorisering eller tokens.

AMQP:s SASL-integrering har två nackdelar:

* Alla autentiseringsuppgifter och tokens begränsas till anslutningen. En meddelandeinfrastruktur kanske vill tillhandahålla differentierad åtkomstkontroll per enhet. till exempel att låta innehavaren av en token skicka till kö A men inte köa B. Med auktoriseringskontexten förankrad i anslutningen går det inte att använda en enda anslutning och ändå använda olika åtkomsttoken för kö A och kö B.
* Åtkomsttoken är vanligtvis endast giltiga under en begränsad tid. Den här giltigheten kräver att användaren regelbundet återerar token och ger tokenutfärdaren möjlighet att vägra utfärda en ny token om användarens åtkomstbehörigheter har ändrats. AMQP-anslutningar kan pågå under en längre tid. SASL-modellen ger bara en chans att ställa in en token vid anslutningstid, vilket innebär att meddelandeinfrastrukturen antingen måste koppla bort klienten när token upphör att gälla eller att den måste acceptera risken för att tillåta fortsatt kommunikation med en klient som tillträdesrätt kan ha återkallats under tiden.

AMQP CBS-specifikationen, som implementeras av Service Bus, möjliggör en elegant lösning för båda dessa problem: Det gör att en klient kan associera åtkomsttoken med varje nod och uppdatera dessa token innan de upphör att gälla, utan att avbryta meddelandeflödet.

CBS definierar en virtuell hanteringsnod, med namnet *$cbs*, som ska tillhandahållas av meddelandeinfrastrukturen. Hanteringsnoden accepterar token för andra noder i meddelandeinfrastrukturen.

Protokollgesten är ett utbyte av begäran/svar enligt vad som definieras i hanteringsspecifikationen. Det innebär att klienten upprättar ett par länkar med *den $cbs* noden och sedan skickar en begäran på den utgående länken och väntar sedan på svaret på den inkommande länken.

Meddelandet om begäran har följande programegenskaper:

| Nyckel | Valfri | Värdetyp | Värdeinnehåll |
| --- | --- | --- | --- |
| Drift |Inga |sträng |**put-token** |
| typ |Inga |sträng |Typen av token som sätts. |
| namn |Inga |sträng |Den "målgrupp" som token gäller för. |
| Förfallodagen |Ja |timestamp |Tokens utgångstid. |

Egenskapen *name* identifierar den enhet som token ska associeras med. I Service Bus är det sökvägen till kön eller ämne/prenumeration. Typegenskapen identifierar tokentypen: *type*

| Tokentyp | Beskrivning av token | Kroppstyp | Anteckningar |
| --- | --- | --- | --- |
| amqp:jwt |JSON-webbtoken (JVT) |AMQP-värde (sträng) |Ännu inte tillgänglig. |
| amqp:swt |Enkel webbtoken (SWT) |AMQP-värde (sträng) |Stöds endast för SWT-token som utfärdats av AAD/ACS |
| servicebus.windows.net:sastoken |Service buss SAS-token |AMQP-värde (sträng) |- |

Tokens ger rättigheter. Service Bus känner till tre grundläggande rättigheter: "Skicka" gör det möjligt att skicka, "Lyssna" gör det möjligt att ta emot och "Hantera" gör det möjligt att manipulera entiteter. SWT-token som utfärdats av AAD/ACS innehåller uttryckligen dessa rättigheter som anspråk. Service Bus SAS-token refererar till regler som konfigurerats på namnområdet eller entiteten och dessa regler är konfigurerade med rättigheter. Om du signerar token med nyckeln som är associerad med den regeln får token att uttrycka respektive rättigheter. Token som är associerad med en entitet med *put-token* gör det möjligt för den anslutna klienten att interagera med entiteten enligt tokenrättigheterna. En länk där klienten tar på *sig rollen avsändare* kräver "Skicka"-rättigheten. att ta på *sig mottagaren* roll kräver "Lyssna" rätt.

Svarsmeddelandet har följande *programegenskaper*

| Nyckel | Valfri | Värdetyp | Värdeinnehåll |
| --- | --- | --- | --- |
| status-kod |Inga |int |HTTP-svarskod **[RFC2616]**. |
| statusbeskrivning |Ja |sträng |Beskrivning av status. |

Klienten kan anropa *put-token* upprepade gånger och för alla entiteter i meddelandeinfrastrukturen. Tokens är begränsade till den aktuella klienten och förankrade på den aktuella anslutningen, vilket innebär att servern släpper alla kvarhållna token när anslutningen släpps.

Den aktuella Service Bus-implementeringen tillåter endast CBS i samband med SASL-metoden "ANONYM". En SSL/TLS-anslutning måste alltid finnas före SASL-handskakningen.

ANONYM-mekanismen måste därför stödjas av den valda AMQP 1.0-klienten. Anonym åtkomst innebär att det första anslutningshandskakningen, inklusive skapandet av den första sessionen sker utan att Service Bus vet vem som skapar anslutningen.

När anslutningen och sessionen har upprättats är det bara tillåtna åtgärder att koppla länkarna till *den $cbs* noden och skicka begäran om *put-token.* En giltig token måste ställas in med hjälp av en *put-token-begäran* för vissa entitetsnod inom 20 sekunder efter att anslutningen har upprättats, annars släpps anslutningen ensidigt av Service Bus.

Klienten är därefter ansvarig för att hålla reda på token förfallodatum. När en token upphör att gälla tappar Service Bus snabbt alla länkar på anslutningen till respektive entitet. För att förhindra att problem uppstår kan klienten ersätta token för noden med en ny när som helst via den virtuella *$cbs* hanteringsnoden med samma *put-token-gest* och utan att komma i vägen för nyttolasttrafiken som flödar på olika länkar.

### <a name="send-via-functionality"></a>Skicka via-funktioner

[Send-via / Transfer avsändare](service-bus-transactions.md#transfers-and-send-via) är en funktion som låter servicebussen vidarebefordra ett visst meddelande till en målentitet via en annan entitet. Den här funktionen används för att utföra åtgärder över entiteter i en enda transaktion.

Med den här funktionen skapar du en avsändare `via-entity`och upprättar länken till . När länken upprättas skickas ytterligare information för att fastställa den verkliga destinationen för meddelandena/överföringarna på den här länken. När bifogan har lyckats vidarebefordras alla meddelanden som skickas på den här länken automatiskt till *målenheten* via *via entitet.* 

> Autentisering måste utföras för både *via-entitet* och *målentitet* innan den här länken upprättas.

| Client | | Service Bus |
| --- | --- | --- |
| bifoga(<br/>name={link name},<br/>role=avsändare,<br/>source={klientlänk-ID},<br/>target=**{via-entitet}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) ) | ------> | |
| | <------ | bifoga(<br/>name={link name},<br/>role=mottagare.<br/>source={klientlänk-ID},<br/>target={via entitet},<br/>properties=karta [(<br/>com.microsoft:transfer-destination-adress=<br/>{mål-entitet} )] ) |

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-stöd för delade köer och ämnen för servicebuss]
* [AMQP i servicebuss för Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-stöd för delade köer och ämnen för servicebuss]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP i servicebuss för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
