---
title: Checklista för prestanda och skalbarhet för tabelllagring – Azure Storage
description: En checklista med beprövad praxis för användning med tabelllagring för att utveckla högpresterande program.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749549"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Checklista för prestanda och skalbarhet för tabelllagring

Microsoft har utvecklat ett antal beprövade metoder för att utveckla högpresterande program med tabelllagring. Den här checklistan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och under hela processen.

Azure Storage har skalbarhets- och prestandamål för kapacitet, transaktionshastighet och bandbredd. Mer information om Azure Storage-skalbarhetsmål finns i [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) och [skalbarhets- och prestandamål för tabelllagring](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln organiserar beprövade metoder för prestanda i en checklista som du kan följa när du utvecklar ditt table storage-program.

| Klart | Kategori | Design övervägande |
| --- | --- | --- |
| &nbsp; |Skalbarhetsmål |[Kan du utforma ditt program så att det inte använder mer än det maximala antalet lagringskonton?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalbarhetsmål |[Undviker du att närma dig kapacitets- och transaktionsgränser?](#capacity-and-transaction-targets) |
| &nbsp; |Skalbarhetsmål |[Närmar du dig skalbarhetsmålen för entiteter per sekund?](#targets-for-data-operations) |
| &nbsp; |Nätverk |[Har klientenheter tillräckligt hög bandbredd och låg latens för att uppnå den prestanda som behövs?](#throughput) |
| &nbsp; |Nätverk |[Har klientenheter en nätverkslänk av hög kvalitet?](#link-quality) |
| &nbsp; |Nätverk |[Är klientprogrammet i samma region som lagringskontot?](#location) |
| &nbsp; |Direkt klientåtkomst |[Använder du SIGNATURER för delad åtkomst (SAS) och COS (Cross-Origin Resource Sharing) för att möjliggöra direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; |Batchbearbetning |[Är ditt program batching uppdateringar med hjälp av entitetsgrupp transaktioner?](#batch-transactions) |
| &nbsp; |.NET-konfiguration |[Använder du .NET Core 2.1 eller senare för optimal prestanda?](#use-net-core) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat klienten att använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat .NET för att använda ett tillräckligt antal trådar för .NET-program?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellitet |[Har du sett till att parallellism är begränsad på rätt sätt så att du inte överbelastar klientens kapacitet eller närmar dig skalbarhetsmålen?](#unbounded-parallelism) |
| &nbsp; |Verktyg |[Använder du de senaste versionerna av klientbibliotek och verktyg som tillhandahålls av Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Antal försök |[Använder du en återförsöksprincip med en exponentiell backoff för begränsningsfel och tidsutgångar?](#timeout-and-server-busy-errors) |
| &nbsp; |Antal försök |[Undviker ditt program återförsök för fel som inte kan försökas om?](#non-retryable-errors) |
| &nbsp; |Konfiguration |[Använder du JSON för dina bordsförfrågningar?](#use-json) |
| &nbsp; |Konfiguration |[Har du stängt av Nagle-algoritmen för att förbättra prestanda för små begäranden?](#disable-nagle) |
| &nbsp; |Tabeller och partitioner |[Har du partitionerat dina data på rätt sätt?](#schema) |
| &nbsp; |Heta partitioner |[Undviker du tilläggs- och prepend-bara-mönster?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Heta partitioner |[Är dina skär/uppdateringar spridda över många partitioner?](#high-traffic-data) |
| &nbsp; |Frågeomfång |[Har du utformat ditt schema för att möjliggöra punktfrågor som ska användas i de flesta fall, och tabellfrågor som ska användas sparsamt?](#query-scope) |
| &nbsp; |Frågedensitet |[Söker dina frågor vanligtvis bara igenom och returnerar rader som programmet kommer att använda?](#query-density) |
| &nbsp; |Begränsa returnerade data |[Använder du filtrering för att undvika att returnera entiteter som inte behövs?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Begränsa returnerade data |[Använder du projektion för att undvika att returnera egenskaper som inte behövs?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisering |[Har du denormalized dina data så att du undviker ineffektiva frågor eller flera läsbegäranden när du försöker få data?](#denormalization) |
| &nbsp; |Infoga, uppdatera och ta bort |[Är du batching förfrågningar som behöver vara transaktionella eller kan göras samtidigt för att minska rundturer?](#batching) |
| &nbsp; |Infoga, uppdatera och ta bort |[Undviker du att hämta en entitet bara för att avgöra om du vill anropa infoga eller uppdatera?](#upsert) |
| &nbsp; |Infoga, uppdatera och ta bort |[Har du övervägt att lagra dataserier som ofta hämtas tillsammans i en enda entitet som egenskaper i stället för flera entiteter?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Infoga, uppdatera och ta bort |[För entiteter som alltid hämtas tillsammans och kan skrivas i batchar (till exempel tidsseriedata), har du övervägt att använda blobbar i stället för tabeller?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Skalbarhetsmål

Om ditt program närmar sig eller överskrider något av skalbarhetsmålen kan det stöta på ökade transaktionsdämpningar eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera 503 (Server upptagen) eller 500 (Operation timeout) felkoder. Att undvika dessa fel genom att hålla sig inom gränserna för skalbarhetsmålen är en viktig del för att förbättra programmets prestanda.

Mer information om skalbarhetsmål för tabelltjänsten finns i [Skalbarhets- och prestandamål för tabelllagring](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagringskonton

Om du närmar dig det maximala antalet lagringskonton som tillåts för en viss kombination av prenumeration/region, använder du flera lagringskonton för att öka ingående åtgärder, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här fallet rekommenderar Microsoft att du drar nytta av ökade gränser för lagringskonton för att minska antalet lagringskonton som krävs för din arbetsbelastning om möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagringskonto. Mer information finns i [Presentera lagringskonton i större skala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitets- och transaktionsmål

Om ditt program närmar sig skalbarhetsmålen för ett enda lagringskonto kan du överväga att använda någon av följande metoder:  

- Ompröva arbetsbelastningen som gör att ditt program närmar sig eller överskrider skalbarhetsmålet. Kan du utforma det annorlunda för att använda mindre bandbredd eller kapacitet, eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhetsmålen skapar du flera lagringskonton och partitionerar dina programdata över dessa flera lagringskonton. Om du använder det här mönstret måste du utforma programmet så att du kan lägga till fler lagringskonton i framtiden för belastningsutjämning. Lagringskonton själva har ingen annan kostnad än din användning när det gäller lagrade data, transaktioner eller data som överförs.
- Om ditt program närmar sig bandbreddsmålen bör du överväga att komprimera data på klientsidan för att minska den bandbredd som krävs för att skicka data till Azure Storage.
    Komprimera data kan spara bandbredd och förbättra nätverkets prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestandapåverkan av de ytterligare bearbetningskraven för datakomprimering och dekompression på klientsidan. Tänk på att lagring av komprimerade data kan göra det svårare att felsöka eftersom det kan vara svårare att visa data med standardverktyg.
- Om ditt program närmar sig skalbarhetsmålen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhetsmålen genom att implementera rekommendationerna som beskrivs i den här artikeln. Men om du använder en exponentiell backoff för återförsök kommer programmet att förhindra att programmet försöker igen snabbt, vilket kan göra begränsningen värre. Mer information finns i avsnittet [Timeout och Server Busy errors](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Mål för dataoperationer

Azure Storage belastningsaldon som trafiken till ditt lagringskonto ökar, men om trafiken uppvisar plötsliga skurar, kanske du inte kan få den här volymen av dataflöde omedelbart. Räkna med att se begränsning och/eller timeout under burst som Azure Storage automatiskt belastning balanserar din tabell. Rampning upp långsamt i allmänhet ger bättre resultat, eftersom systemet har tid att belastningsbalansen på lämpligt sätt.

#### <a name="entities-per-second-storage-account"></a>Entiteter per sekund (lagringskonto)

Skalbarhetsgränsen för åtkomst till tabeller är upp till 20 000 entiteter (1 KB per sekund) per sekund för ett konto. I allmänhet räknas varje entitet som infogas, uppdateras, tas bort eller skannas mot det här målet. Så en batch infoga som innehåller 100 entiteter skulle räknas som 100 entiteter. En fråga som söker igenom 1000 entiteter och returnerar 5 räknas som 1 000 entiteter.

#### <a name="entities-per-second-partition"></a>Entiteter per sekund (partition)

Inom en enda partition är skalbarhetsmålet för åtkomst till tabeller 2 000 entiteter (1 KB per sekund, med samma räkning som beskrivs i föregående avsnitt.

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

## <a name="batch-transactions"></a>Batchtransaktioner

Tabelltjänsten stöder batchtransaktioner på entiteter som finns i samma tabell och tillhör samma partitionsgrupp. Mer information finns i [Utföra entitetsgruppstransaktioner](/rest/api/storageservices/performing-entity-group-transactions).

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

## <a name="configuration"></a>Konfiguration

I det här avsnittet visas flera snabba konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar i tabelltjänsten:

### <a name="use-json"></a>Använd JSON

Från och med lagringstjänstversionen 2013-08-15 stöder tabelltjänsten att använda JSON i stället för det XML-baserade AtomPub-formatet för överföring av tabelldata. Använda JSON kan minska nyttolasten storlekar med så mycket som 75% och kan avsevärt förbättra prestanda för ditt program.

Mer information finns i inlägget [Microsoft Azure-tabeller: Introduktion till JSON-](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) [och Nyttolastformat för table service-åtgärder](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Inaktivera Nagle

Nagles algoritm implementeras i stor utsträckning i TCP/IP-nätverk som ett sätt att förbättra nätverksprestanda. Det är dock inte optimalt under alla omständigheter (t.ex. mycket interaktiva miljöer). Nagles algoritm har en negativ inverkan på prestanda för begäranden till Azure Table-tjänsten, och du bör inaktivera den om möjligt.

## <a name="schema"></a>Schema

Hur du representerar och frågar dina data är den största enskilda faktorn som påverkar tabelltjänstens prestanda. Även om varje program är olika, beskriver det här avsnittet några allmänna beprövade metoder som relaterar till:

- Tabell design
- Effektiva frågor
- Effektiva datauppdateringar

### <a name="tables-and-partitions"></a>Tabeller och partitioner

Tabeller är indelade i partitioner. Varje entitet som lagras i en partition delar samma partitionsnyckel och har en unik radnyckel för att identifiera den i den partitionen. Partitioner ger fördelar men även införa skalbarhetsgränser.

- Fördelar: Du kan uppdatera entiteter i samma partition i en enda, atomär batchtransaktion som innehåller upp till 100 separata lagringsåtgärder (en gräns på 4 MB total storlek). Om du antar att samma antal entiteter ska hämtas kan du också fråga data inom en enda partition mer effektivt än data som sträcker sig över partitioner (även om de läs vidare för ytterligare rekommendationer om att fråga tabelldata).
- Skalbarhetsgräns: Åtkomst till entiteter som lagras i en enda partition kan inte belastningsutjämnas eftersom partitioner stöder atombatchtransaktioner. Därför är skalbarhetsmålet för en enskild tabellpartition lägre än för tabelltjänsten som helhet.

På grund av dessa egenskaper hos tabeller och partitioner bör du anta följande designprinciper:

- Leta reda på data som klientprogrammet ofta uppdaterar eller frågar i samma logiska arbetsenhet i samma partition. Leta till exempel upp data i samma partition om ditt program samlar skrivningar eller om du utför atombatchåtgärder. Data i en enda partition kan också efterfrågas mer effektivt i en enda fråga än data mellan partitioner.
- Leta reda på data som klientprogrammet inte infogar, uppdaterar eller frågar i samma logiska arbetsenhet (det vill ha i en enda fråga eller batchuppdatering) i separata partitioner. Tänk på att det inte finns någon gräns för antalet partitionsnycklar i en enda tabell, så att ha miljontals partitionsnycklar är inte ett problem och påverkar inte prestanda. Om ditt program till exempel är en populär webbplats med användarinloggning kan det vara ett bra val att använda användar-ID som partitionsnyckel.

#### <a name="hot-partitions"></a>Heta partitioner

En het partition är en som tar emot en oproportionerligt stor andel av trafiken till ett konto och kan inte belastningsbalanseras eftersom det är en enda partition. I allmänhet skapas heta partitioner på två sätt:

#### <a name="append-only-and-prepend-only-patterns"></a>Lägg till endast och prepend-mönster

Mönstret "Lägg till endast" är ett där all (eller nästan alla) av trafiken till en viss partitionsnyckel ökar och minskar beroende på aktuell tid. Anta till exempel att programmet använder det aktuella datumet som en partitionsnyckel för loggdata. Den här designen resulterar i att alla skär går till den sista partitionen i tabellen och systemet kan inte belastningsbalansen korrekt. Om trafikvolymen till den partitionen överskrider skalbarhetsmålet på partitionsnivå, resulterar det i begränsning. Det är bättre att se till att trafiken skickas till flera partitioner, så att belastningsbalansen kan balanseras i tabellen.

#### <a name="high-traffic-data"></a>Data med hög trafik

Om partitioneringsschemat resulterar i en enda partition som bara har data som används mycket mer än andra partitioner, kan du också se begränsning när partitionen närmar sig skalbarhetsmålet för en enda partition. Det är bättre att se till att partitionsschemat resulterar i att ingen enskild partition närmar sig skalbarhetsmålen.

### <a name="querying"></a>Fråga

I det här avsnittet beskrivs beprövade metoder för att fråga tabelltjänsten.

#### <a name="query-scope"></a>Frågeomfång

Det finns flera sätt att ange hur du anger hur du frågar efter entiteter. I följande lista beskrivs varje alternativ för frågeomfattning.

- **Punktfrågor:**- En punktfråga hämtar exakt en entitet genom att ange både partitionsnyckeln och radnyckeln för entiteten som ska hämtas. Dessa frågor är effektiva och du bör använda dem där det är möjligt.
- **Partitionsfrågor:** En partitionsfråga är en fråga som hämtar en uppsättning data som delar en gemensam partitionsnyckel. Vanligtvis anger frågan ett område med radnyckelvärden eller ett värdeintervall för vissa entitetsegenskap utöver en partitionsnyckel. Dessa frågor är mindre effektiva än punktfrågor och bör användas sparsamt.
- **Tabellfrågor:** En tabellfråga är en fråga som hämtar en uppsättning entiteter som inte delar en gemensam partitionsnyckel. Dessa frågor är inte effektiva och du bör undvika dem om möjligt.

Undvik i allmänhet genomsökningar (frågor som är större än en enda entitet), men om du måste skanna, försök att ordna dina data så att dina genomsökningar hämtar de data du behöver utan att skanna eller returnera betydande mängder entiteter som du inte behöver.

#### <a name="query-density"></a>Frågedensitet

En annan viktig faktor i frågeeffektiviteten är antalet entiteter som returneras jämfört med antalet entiteter som genomsöks för att hitta den returnerade uppsättningen. Om ditt program utför en tabellfråga med ett filter för ett egenskapsvärde som endast 1 % av dataresurserna, söker frågan igenom 100 entiteter för var och entitet som returneras. De tabellskalbarhetsmål som diskuterats tidigare avser alla antalet skannade entiteter och inte antalet enheter som returneras: en låg frågedensitet kan enkelt leda till att tabelltjänsten begränsar ditt program eftersom den måste söka igenom så många entiteter för att hämta den entitet du letar efter. Mer information om hur du undviker begränsning finns i avsnittet [Denormalisering](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Begränsa mängden data som returneras

När du vet att en fråga returnerar entiteter som du inte behöver i klientprogrammet bör du överväga att använda ett filter för att minska storleken på den returnerade uppsättningen. Även om entiteterna som inte returneras till klienten fortfarande räknas in i skalbarhetsgränserna förbättras programprestandan på grund av den minskade nätverksnyttolastens storlek och det minskade antalet entiteter som klientprogrammet måste bearbeta. Tänk på att skalbarhetsmålen relaterar till antalet skannade entiteter, så en fråga som filtrerar bort många entiteter kan fortfarande resultera i begränsning, även om få entiteter returneras. Mer information om hur du gör frågor effektiva finns i avsnittet [Frågedensitet](#query-density).

Om klientprogrammet bara behöver en begränsad uppsättning egenskaper från entiteterna i tabellen kan du använda projektionen för att begränsa storleken på den returnerade datauppsättningen. Precis som med filtrering bidrar projektionen till att minska nätverksbelastningen och klientbearbetningen.

#### <a name="denormalization"></a>Denormalisering

Till skillnad från att arbeta med relationsdatabaser leder de beprövade metoderna för att effektivt fråga tabelldata till att avnormalisera dina data. Det innebär att du duplicerar samma data i flera entiteter (en för varje nyckel som du kan använda för att hitta data) för att minimera antalet entiteter som en fråga måste söka efter för att hitta de data som klienten behöver, i stället för att behöva skanna ett stort antal entiteter för att hitta de data som applikationsbehov. Till exempel, i en e-handel webbplats, Du kanske vill hitta en order både av kund-ID (ge mig denna kunds order) och datum (ge mig order på ett datum). I Tabelllagring är det bäst att lagra entiteten (eller en referens till den) två gånger – en gång med tabellnamn, PK och RK för att underlätta att hitta efter kund-ID, en gång för att underlätta att hitta den före datumet.  

### <a name="insert-update-and-delete"></a>Infoga, uppdatera och ta bort

I det här avsnittet beskrivs beprövad praxis för att ändra entiteter som lagras i tabelltjänsten.  

#### <a name="batching"></a>Batchbearbetning

Batchtransaktioner kallas entitetsgruppstransaktioner i Azure Storage. Alla operationer inom en entitetsgruppstransaktion måste finnas på en enda partition i en enda tabell. Om möjligt kan du använda entitetsgruppstransaktioner för att utföra infogningar, uppdateringar och borttagningar i batchar. Om du använder entitetsgruppstransaktioner minskar antalet tur-och-retur-resor från klientprogrammet till servern, antalet fakturerbara transaktioner minskar (en entitetsgruppstransaktion räknas som en enda transaktion för faktureringsändamål och kan innehålla upp till 100 lagringsåtgärder) och aktiverar atomuppdateringar (alla åtgärder lyckas eller alla misslyckas inom en entitetsgruppstransaktion). Miljöer med höga svarstider som mobila enheter kommer att ha stor nytta av att använda entitetsgruppstransaktioner.  

#### <a name="upsert"></a>Upsert

Använd **tabelluppseringsåtgärder** där det är möjligt. Det finns två typer av **Upsert**, som båda kan vara effektivare än en traditionell **Infoga** och **uppdatera** åtgärder:  

- **InsertOrMerge**: Använd den här åtgärden när du vill överföra en delmängd av entitetens egenskaper, men är osäker på om entiteten redan finns. Om entiteten finns uppdaterar det här anropet egenskaperna som ingår i **upsert-åtgärden** och lämnar alla befintliga egenskaper som de är, om entiteten inte finns infogar den nya entiteten. Detta liknar att använda projektion i en fråga, eftersom du bara behöver ladda upp de egenskaper som ändras.
- **InsertOrReplace**: Använd den här åtgärden när du vill ladda upp en helt ny entitet, men du är osäker på om den redan finns. Använd den här åtgärden när du vet att den nyligen uppladdade entiteten är helt korrekt eftersom den helt skriver över den gamla entiteten. Du vill till exempel uppdatera entiteten som lagrar en användares aktuella plats oavsett om programmet tidigare har lagrat platsdata för användaren eller inte. Den nya platsentiteten är klar och du behöver ingen information från någon tidigare entitet.

#### <a name="storing-data-series-in-a-single-entity"></a>Lagra dataserier i en enda entitet

Ibland lagrar ett program en serie data som det ofta behöver hämta alla på en gång: till exempel kan ett program spåra CPU-användning över tid för att rita ett rullande diagram över data från de senaste 24 timmarna. En metod är att ha en tabellentitet per timme, där varje entitet representerar en viss timme och lagrar CPU-användningen för den timmen. För att kunna rita dessa data måste programmet hämta entiteterna som innehåller data från de 24 senaste timmarna.  

Alternativt kan ditt program lagra CPU-användningen för varje timme som en separat egenskap för en enda entitet: för att uppdatera varje timme kan ditt program använda ett enda **InsertOrMerge Upsert-anrop** för att uppdatera värdet för den senaste timmen. För att rita data behöver programmet bara hämta en enda entitet i stället för 24, vilket gör en effektiv fråga. Mer information om frågeeffektivitet finns i avsnittet [Frågeomfattning](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Lagra strukturerade data i blobbar

Om du utför batchinfogningar och sedan hämtar intervall för entiteter tillsammans kan du använda blobbar i stället för tabeller. Ett bra exempel är en loggfil. Du kan gruppera flera minuter med loggar och infoga dem och sedan hämta flera minuter loggar åt gången. I det här fallet är prestanda bättre om du använder blobbar i stället för tabeller, eftersom du avsevärt kan minska antalet objekt som skrivs till eller läst, och eventuellt även antalet begäranden som behöver göras.  

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets- och prestandamål för tabelllagring](scalability-targets.md)
- [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status- och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)
