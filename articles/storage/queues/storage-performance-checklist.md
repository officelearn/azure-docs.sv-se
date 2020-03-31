---
title: Checklista för prestanda och skalbarhet för kölagring – Azure Storage
description: En checklista med beprövade metoder för användning med kölagring för att utveckla högpresterande program.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75750509"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Checklista för prestanda och skalbarhet för kölagring

Microsoft har utvecklat ett antal beprövade metoder för att utveckla högpresterande program med kölagring. Den här checklistan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och under hela processen.

Azure Storage har skalbarhets- och prestandamål för kapacitet, transaktionshastighet och bandbredd. Mer information om Azure Storage-skalbarhetsmål finns i [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) och [skalbarhets- och prestandamål för kölagring](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln organiserar beprövade metoder för prestanda i en checklista som du kan följa när du utvecklar ditt kölagringsprogram.

| Klart | Kategori | Design övervägande |
| --- | --- | --- |
| &nbsp; |Skalbarhetsmål |[Kan du utforma ditt program så att det inte använder mer än det maximala antalet lagringskonton?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalbarhetsmål |[Undviker du att närma dig kapacitets- och transaktionsgränser?](#capacity-and-transaction-targets) |
| &nbsp; |Nätverk |[Har klientenheter tillräckligt hög bandbredd och låg latens för att uppnå den prestanda som behövs?](#throughput) |
| &nbsp; |Nätverk |[Har klientenheter en nätverkslänk av hög kvalitet?](#link-quality) |
| &nbsp; |Nätverk |[Är klientprogrammet i samma region som lagringskontot?](#location) |
| &nbsp; |Direkt klientåtkomst |[Använder du SIGNATURER för delad åtkomst (SAS) och COS (Cross-Origin Resource Sharing) för att möjliggöra direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; |.NET-konfiguration |[Använder du .NET Core 2.1 eller senare för optimal prestanda?](#use-net-core) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat klienten att använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat .NET för att använda ett tillräckligt antal trådar för .NET-program?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellitet |[Har du sett till att parallellism är begränsad på rätt sätt så att du inte överbelastar klientens kapacitet eller närmar dig skalbarhetsmålen?](#unbounded-parallelism) |
| &nbsp; |Verktyg |[Använder du de senaste versionerna av klientbibliotek och verktyg som tillhandahålls av Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Antal försök |[Använder du en återförsöksprincip med en exponentiell backoff för begränsningsfel och tidsutgångar?](#timeout-and-server-busy-errors) |
| &nbsp; |Antal försök |[Undviker ditt program återförsök för fel som inte kan försökas om?](#non-retryable-errors) |
| &nbsp; |Konfiguration |[Har du stängt av Nagle-algoritmen för att förbättra prestanda för små begäranden?](#disable-nagle) |
| &nbsp; |Meddelandestorlek |[Är dina meddelanden kompakta för att förbättra köens prestanda?](#message-size) |
| &nbsp; |Masshämtning |[Hämtar du flera meddelanden i en enda GET-åtgärd?](#batch-retrieval) |
| &nbsp; |Avsökningsfrekvens |[Är du tillräckligt ofta för att minska den upplevda latensen för ditt program?](#queue-polling-interval) |
| &nbsp; |Uppdatera meddelande |[Använder du åtgärden Uppdatera meddelande för att lagra förloppet i bearbetningen av meddelanden, så att du kan undvika att behöva bearbeta hela meddelandet igen om ett fel uppstår?](#use-update-message) |
| &nbsp; |Arkitektur |[Använder du köer för att göra hela ditt program mer skalbart genom att hålla tidskrävande arbetsbelastningar utanför den kritiska linjen och skala sedan självständigt?](#application-architecture) |

## <a name="scalability-targets"></a>Skalbarhetsmål

Om ditt program närmar sig eller överskrider något av skalbarhetsmålen kan det stöta på ökade transaktionsdämpningar eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera 503 (Server upptagen) eller 500 (Operation timeout) felkoder. Att undvika dessa fel genom att hålla sig inom gränserna för skalbarhetsmålen är en viktig del för att förbättra programmets prestanda.

Mer information om skalbarhetsmål för kötjänsten finns i [Azure Storage skalbarhet och prestandamål](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagringskonton

Om du närmar dig det maximala antalet lagringskonton som tillåts för en viss kombination av prenumeration/region, använder du flera lagringskonton för att öka ingående åtgärder, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här fallet rekommenderar Microsoft att du drar nytta av ökade gränser för lagringskonton för att minska antalet lagringskonton som krävs för din arbetsbelastning om möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagringskonto. Mer information finns i [Presentera lagringskonton i större skala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitets- och transaktionsmål

Om ditt program närmar sig skalbarhetsmålen för ett enda lagringskonto kan du överväga att använda någon av följande metoder:  

- Om skalbarhetsmålen för köer är otillräckliga för ditt program använder du flera köer och distribuerar meddelanden över dem.
- Ompröva arbetsbelastningen som gör att ditt program närmar sig eller överskrider skalbarhetsmålet. Kan du utforma det annorlunda för att använda mindre bandbredd eller kapacitet, eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhetsmålen skapar du flera lagringskonton och partitionerar dina programdata över dessa flera lagringskonton. Om du använder det här mönstret måste du utforma programmet så att du kan lägga till fler lagringskonton i framtiden för belastningsutjämning. Lagringskonton själva har ingen annan kostnad än din användning när det gäller lagrade data, transaktioner eller data som överförs.
- Om ditt program närmar sig bandbreddsmålen bör du överväga att komprimera data på klientsidan för att minska den bandbredd som krävs för att skicka data till Azure Storage.
    Komprimera data kan spara bandbredd och förbättra nätverkets prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestandapåverkan av de ytterligare bearbetningskraven för datakomprimering och dekompression på klientsidan. Tänk på att lagring av komprimerade data kan göra det svårare att felsöka eftersom det kan vara svårare att visa data med standardverktyg.
- Om ditt program närmar sig skalbarhetsmålen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhetsmålen genom att implementera rekommendationerna som beskrivs i den här artikeln. Men om du använder en exponentiell backoff för återförsök kommer programmet att förhindra att programmet försöker igen snabbt, vilket kan göra begränsningen värre. Mer information finns i avsnittet [Timeout och Server Busy errors](#timeout-and-server-busy-errors).

## <a name="networking"></a>Nätverk

Programmets fysiska nätverksbegränsningar kan ha en betydande inverkan på prestanda. I följande avsnitt beskrivs några av de begränsningar som användare kan stöta på.  

### <a name="client-network-capability"></a>Klientnätverkskapacitet

Bandbredd och nätverkslänkens kvalitet spelar viktiga roller i programmets prestanda, enligt beskrivningen i följande avsnitt.

#### <a name="throughput"></a>Dataflöde

För bandbredd är problemet ofta klientens funktioner. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller fler virtuella datorer om du behöver högre nätverksgränser från en enda dator. Om du använder Azure Storage från ett lokalt program gäller samma regel: förstå klientenhetens nätverksfunktioner och nätverksanslutningen till Azure Storage-platsen och förbättra dem efter behov eller designa din att arbeta inom deras kapacitet.

#### <a name="link-quality"></a>Länkkvalitet

Precis som med all nätverksanvändning bör du tänka på att nätverksförhållanden som leder till fel och paketförlust kommer att göra effektivt dataflöde långsammare.  Använda WireShark eller NetMon kan hjälpa till att diagnostisera detta problem.  

### <a name="location"></a>Location

I alla distribuerade miljöer ger placera klienten nära servern bästa prestanda. För åtkomst till Azure Storage med den lägsta svarstiden är den bästa platsen för din klient inom samma Azure-region. Om du till exempel har en Azure-webbapp som använder Azure Storage letar du reda på dem både inom en enda region, till exempel USA Väst eller Asien sydost. Samlokalisering av resurser minskar svarstiden och kostnaden, eftersom bandbreddsanvändningen inom en enda region är gratis.  

Om klientprogram kommer åt Azure Storage men inte finns i Azure, till exempel mobilappar eller lokala företagstjänster, kan det minska svarstiden för att hitta lagringskontot i en region nära dessa klienter. Om dina kunder är brett fördelade (till exempel vissa i Nordamerika och några i Europa) kan du överväga att använda ett lagringskonto per region. Den här metoden är enklare att implementera om de data som programmet lagrar är specifika för enskilda användare och inte kräver att data replikeras mellan lagringskonton.

## <a name="sas-and-cors"></a>SAS och CORS

Anta att du behöver auktorisera kod som JavaScript som körs i en användares webbläsare eller i en mobiltelefonapp för att komma åt data i Azure Storage. En metod är att skapa ett tjänstprogram som fungerar som en proxy. Användarens enhet autentiserar med tjänsten, vilket i sin tur ger åtkomst till Azure Storage-resurser. På så sätt kan du undvika att exponera dina lagringskontonycklar på osäkra enheter. Den här metoden placerar dock en betydande omkostnader för tjänstprogrammet, eftersom alla data som överförs mellan användarens enhet och Azure Storage måste passera genom tjänstprogrammet.

Du kan undvika att använda ett tjänstprogram som en proxy för Azure Storage med hjälp av SIGNATUREr för delad åtkomst (SAS). Med SAS kan du aktivera användarens enhet för att göra förfrågningar direkt till Azure Storage med hjälp av en begränsad åtkomsttoken. Om en användare till exempel vill ladda upp ett foto till ditt program kan ditt tjänstprogram generera en SAS och skicka det till användarens enhet. SAS-token kan bevilja behörighet att skriva till en Azure Storage-resurs för ett angivet tidsintervall, varefter SAS-token upphör att gälla. Mer information om SAS finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../common/storage-sas-overview.md)  

Vanligtvis tillåter en webbläsare inte JavaScript på en sida som finns värd för en webbplats på en domän för att utföra vissa åtgärder, till exempel skrivåtgärder, till en annan domän. Den här principen kallas principen för samma ursprung och förhindrar att ett skadligt skript på en sida får åtkomst till data på en annan webbsida. Principen med samma ursprung kan dock vara en begränsning när du skapar en lösning i molnet. Kors-ursprungsresursdelning (CORS) är en webbläsarfunktion som gör att måldomänen kan kommunicera med webbläsaren om att den litar på begäranden som har sitt ursprung i källdomänen.

Anta till exempel att ett webbprogram som körs i Azure gör en begäran om en resurs till ett Azure Storage-konto. Webbprogrammet är källdomänen och lagringskontot är måldomänen. Du kan konfigurera CORS för någon av Azure Storage-tjänsterna för att kommunicera med webbläsaren som begäranden från källdomänen är betrodda av Azure Storage. Mer information om CORS finns i [CORS-stöd (Cross-Origin Resource Sharing) för Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Både SAS och CORS kan hjälpa dig att undvika onödig belastning på ditt webbprogram.  

## <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visas flera snabba konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar.  Om du använder andra språk kontrollerar du om liknande begrepp gäller på det språk du valt.  

### <a name="use-net-core"></a>Använd .NET Core

Utveckla dina Azure Storage-program med .NET Core 2.1 eller senare för att dra nytta av prestandaförbättringar. Användning av .NET Core 3.x rekommenderas när det är möjligt.

Mer information om prestandaförbättringar i .NET Core finns i följande blogginlägg:

- [Prestandaförbättringar i .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestandaförbättringar i .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Öka standardanslutningsgränsen

I .NET ökar följande kod standardanslutningsgränsen (som vanligtvis är 2 i en klientmiljö eller 10 i en servermiljö) till 100. Vanligtvis bör du ange värdet till ungefär antalet trådar som används av ditt program.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Ställ in anslutningsgränsen innan du öppnar några anslutningar.  

För andra programmeringsspråk, se språkets dokumentation för att avgöra hur anslutningsgränsen ska anges.  

Mer information finns i blogginläggen [Webbtjänster: Samtidiga anslutningar](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Öka minsta antal trådar

Om du använder synkrona samtal tillsammans med asynkrona uppgifter kanske du vill öka antalet trådar i trådpoolen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i metoden [ThreadPool.SetMinThreads.](/dotnet/api/system.threading.threadpool.setminthreads)  

## <a name="unbounded-parallelism"></a>Obegränsad parallellism

Även parallellism kan vara bra för prestanda, vara försiktig med att använda obegränsad parallellism, vilket innebär att det inte finns någon gräns tillämpas på antalet trådar eller parallella förfrågningar. Var noga med att begränsa parallella begäranden att ladda upp eller ladda ned data, komma åt flera partitioner i samma lagringskonto eller komma åt flera objekt i samma partition. Om parallellism är obundet kan ditt program överskrida klientenhetens funktioner eller lagringskontots skalbarhetsmål, vilket resulterar i längre fördröjningar och begränsning.  

## <a name="client-libraries-and-tools"></a>Klientbibliotek och verktyg

För bästa prestanda bör du alltid använda de senaste klientbiblioteken och verktygen från Microsoft. Azure Storage-klientbibliotek är tillgängliga för en mängd olika språk. Azure Storage stöder också PowerShell och Azure CLI. Microsoft utvecklar aktivt dessa klientbibliotek och verktyg med prestanda i åtanke, håller dem uppdaterade med de senaste tjänstversionerna och ser till att de hanterar många av de beprövade prestandapraxis internt. Mer information finns i [referensdokumentationen för Azure Storage](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Hantera servicefel

Azure Storage returnerar ett fel när tjänsten inte kan bearbeta en begäran. Att förstå de fel som kan returneras av Azure Storage i ett visst scenario är användbart för att optimera prestanda.

### <a name="timeout-and-server-busy-errors"></a>Timeout och server upptagen fel

Azure Storage kan begränsa ditt program om det närmar sig skalbarhetsgränserna. I vissa fall kan Det hända att Azure Storage inte kan hantera en begäran på grund av vissa tillfälliga villkor. I båda fallen kan tjänsten returnera ett fel på 503 (Server Busy) eller 500 (Timeout). Dessa fel kan också uppstå om tjänsten balanserar om datapartitioner för att möjliggöra högre dataflöde. Klientprogrammet bör vanligtvis försöka igen åtgärden som orsakar ett av dessa fel. Men om Azure Storage begränsar ditt program eftersom det överskrider skalbarhetsmål, eller även om tjänsten inte kunde betjäna begäran av någon annan anledning, kan aggressiva återförsök göra problemet värre. Det rekommenderas att du använder en exponentiell princip för återförsök och klientbiblioteken är som standard för det här beteendet. Programmet kan till exempel försöka igen efter 2 sekunder, sedan 4 sekunder, sedan 10 sekunder, sedan 30 sekunder och ge sedan upp helt. På så sätt minskar ditt program avsevärt belastningen på tjänsten, snarare än att förvärra beteende som kan leda till begränsning.  

Anslutningsfel kan göras om omedelbart, eftersom de inte är resultatet av begränsning och förväntas vara övergående.  

### <a name="non-retryable-errors"></a>Fel som inte kan försökas igen

Klientbiblioteken hanterar återförsök med en medvetenhet om vilka fel som kan göras om och vilka som inte kan göras. Men om du anropar Azure Storage REST API direkt, finns det några fel som du inte bör försöka igen. Ett fel på 400 (felaktig begäran) anger till exempel att klientprogrammet skickade en begäran som inte kunde bearbetas eftersom den inte fanns i det förväntade formuläret. Om du skickar den här begäran igen får du samma svar varje gång, så det är ingen idé att försöka igen. Om du anropar AZURE Storage REST API direkt, vara medveten om potentiella fel och om de ska göras om.

Mer information om Azure Storage-felkoder finns i [Status- och felkoder](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagle"></a>Inaktivera Nagle

Nagles algoritm implementeras i stor utsträckning i TCP/IP-nätverk som ett sätt att förbättra nätverksprestanda. Det är dock inte optimalt under alla omständigheter (t.ex. mycket interaktiva miljöer). Nagles algoritm har en negativ inverkan på prestanda för begäranden till Azure Table-tjänsten, och du bör inaktivera den om möjligt.

## <a name="message-size"></a>Meddelandestorlek

Köprestanda och skalbarhet minskar när meddelandestorleken ökar. Placera bara den information som mottagaren behöver i ett meddelande.  

## <a name="batch-retrieval"></a>Batchhämtning

Du kan hämta upp till 32 meddelanden från en kö i en enda åtgärd. Batch hämtning kan minska antalet tur och returresor från klientprogrammet, vilket är särskilt användbart för miljöer, till exempel mobila enheter, med hög latens.  

## <a name="queue-polling-interval"></a>Avsökningsintervall för kö

De flesta program avsökning för meddelanden från en kö, som kan vara en av de största källorna till transaktioner för det programmet. Välj avsökningsintervallet klokt: avsökning för ofta kan göra att ditt program närmar sig skalbarhetsmålen för kön. Men vid 200.000 transaktioner för $ 0,01 (i skrivande stund), en enda processor röstning en gång i sekunden för en månad skulle kosta mindre än 15 cent så kostnaden är vanligtvis inte en faktor som påverkar ditt val av röstning intervall.  

Aktuell kostnadsinformation finns i [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Använda uppdateringsmeddelande

Du kan använda åtgärden **Uppdatera meddelande** för att öka tidsgränsen för osynlighet eller för att uppdatera tillståndsinformationen för ett meddelande. Att använda **uppdatera meddelande** kan vara en mer effektiv metod än att ha ett arbetsflöde som skickar ett jobb från en kö till nästa, eftersom varje steg i jobbet slutförs. Programmet kan spara jobbtillståndet i meddelandet och sedan fortsätta arbeta, i stället för att köa meddelandet för nästa steg i jobbet varje gång ett steg slutförs. Tänk på att varje **uppdateringsmeddelandeåtgärd** räknas in i skalbarhetsmålet.

## <a name="application-architecture"></a>Programarkitektur

Använd köer för att göra programarkitekturen skalbar. Följande visar några sätt du kan använda köer för att göra ditt program mer skalbart:  

- Du kan använda köer för att skapa eftersläpningar av arbete för bearbetning och jämna ut arbetsbelastningar i ditt program. Du kan till exempel köa begäranden från användare för att utföra processorintensivt arbete, till exempel ändra storlek på uppladdade bilder.
- Du kan använda köer för att frikoppla delar av programmet så att du kan skala dem oberoende av dem. En webbklöver kan till exempel placera undersökningsresultat från användare i en kö för senare analys och lagring. Du kan lägga till fler arbetsrollinstanser för att bearbeta ködata efter behov.  

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets- och prestandamål för kölagring](scalability-targets.md)
- [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status- och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)
