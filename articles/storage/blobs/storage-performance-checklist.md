---
title: Checklista för prestanda och skalbarhet för Blob-lagring – Azure Storage
description: En checklista med beprövade metoder för användning med Blob-lagring för att utveckla högpresterande program.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473945"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Checklista för prestanda och skalbarhet för Blob-lagring

Microsoft har utvecklat ett antal beprövade metoder för att utveckla högpresterande program med Blob-lagring. Den här checklistan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och under hela processen.

Azure Storage har skalbarhets- och prestandamål för kapacitet, transaktionshastighet och bandbredd. Mer information om Azure Storage-skalbarhetsmål finns i [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) och [skalbarhets- och prestandamål för Blob-lagring](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln organiserar beprövade metoder för prestanda i en checklista som du kan följa när du utvecklar ditt Blob-lagringsprogram.

| Klart | Kategori | Design övervägande |
| --- | --- | --- |
| &nbsp; |Skalbarhetsmål |[Kan du utforma ditt program så att det inte använder mer än det maximala antalet lagringskonton?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalbarhetsmål |[Undviker du att närma dig kapacitets- och transaktionsgränser?](#capacity-and-transaction-targets) |
| &nbsp; |Skalbarhetsmål |[Har ett stort antal klienter åtkomst till en enda blob samtidigt?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Skalbarhetsmål |[Håller ditt program dig inom skalbarhetsmålen för en enda blob?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partitionering |[Är din namngivningskonvention utformad för att möjliggöra bättre belastningsutjämning?](#partitioning) |
| &nbsp; |Nätverk |[Har klientenheter tillräckligt hög bandbredd och låg latens för att uppnå den prestanda som behövs?](#throughput) |
| &nbsp; |Nätverk |[Har klientenheter en nätverkslänk av hög kvalitet?](#link-quality) |
| &nbsp; |Nätverk |[Är klientprogrammet i samma region som lagringskontot?](#location) |
| &nbsp; |Direkt klientåtkomst |[Använder du SIGNATURER för delad åtkomst (SAS) och COS (Cross-Origin Resource Sharing) för att möjliggöra direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; |Caching |[Är ditt program cachelagring data som ofta nås och sällan ändras?](#reading-data) |
| &nbsp; |Caching |[Är ditt program batching uppdateringar genom att cachelagring dem på klienten och sedan ladda upp dem i större uppsättningar?](#uploading-data-in-batches) |
| &nbsp; |.NET-konfiguration |[Använder du .NET Core 2.1 eller senare för optimal prestanda?](#use-net-core) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat klienten att använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat .NET för att använda ett tillräckligt antal trådar för .NET-program?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellitet |[Har du sett till att parallellism är begränsad på rätt sätt så att du inte överbelastar klientens kapacitet eller närmar dig skalbarhetsmålen?](#unbounded-parallelism) |
| &nbsp; |Verktyg |[Använder du de senaste versionerna av klientbibliotek och verktyg som tillhandahålls av Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Antal försök |[Använder du en återförsöksprincip med en exponentiell backoff för begränsningsfel och tidsutgångar?](#timeout-and-server-busy-errors) |
| &nbsp; |Antal försök |[Undviker ditt program återförsök för fel som inte kan försökas om?](#non-retryable-errors) |
| &nbsp; |Kopiera blobbar |[Kopierar du blobbar på det mest effektiva sättet?](#blob-copy-apis) |
| &nbsp; |Kopiera blobbar |[Använder du den senaste versionen av AzCopy för masskopiering?](#use-azcopy) |
| &nbsp; |Kopiera blobbar |[Använder du Azure Data Box-familjen för att importera stora mängder data?](#use-azure-data-box) |
| &nbsp; |Distribution av innehåll |[Använder du ett CDN för innehållsdistribution?](#content-distribution) |
| &nbsp; |Använda metadata |[Lagrar du ofta använda metadata om blobbar i deras metadata?](#use-metadata) |
| &nbsp; |Ladda upp snabbt |[När du försöker ladda upp en blob snabbt, laddar du upp block parallellt?](#upload-one-large-blob-quickly) |
| &nbsp; |Ladda upp snabbt |[När du försöker ladda upp många blobbar snabbt, laddar du upp blobbar parallellt?](#upload-many-blobs-quickly) |
| &nbsp; |Blobtyp |[Använder du sidblobar eller blockblobar när det är lämpligt?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Skalbarhetsmål

Om ditt program närmar sig eller överskrider något av skalbarhetsmålen kan det stöta på ökade transaktionsdämpningar eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera 503 (Server upptagen) eller 500 (Operation timeout) felkoder. Att undvika dessa fel genom att hålla sig inom gränserna för skalbarhetsmålen är en viktig del för att förbättra programmets prestanda.

Mer information om skalbarhetsmål för kötjänsten finns i [Azure Storage skalbarhet och prestandamål](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagringskonton

Om du närmar dig det maximala antalet lagringskonton som tillåts för en viss kombination av prenumerationer/regioner utvärderar du scenariot och avgör om något av följande villkor gäller:

- Använder du lagringskonton för att lagra ohanterat diskar och lägga till dessa diskar på dina virtuella datorer? I det här fallet rekommenderar Microsoft att du använder hanterade diskar. Hanterade diskar skalas för dig automatiskt och utan att behöva skapa och hantera enskilda lagringskonton. Mer information finns i [Introduktion till Azure-hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md)
- Använder du ett lagringskonto per kund i syfte att isolera data? I det här scenariot rekommenderar Microsoft att du använder en blob-behållare för varje kund i stället för ett helt lagringskonto. Azure Storage kan du nu tilldela rollbaserade åtkomstkontroll (RBAC) roller per behållare. Mer information finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](../common/storage-auth-aad-rbac-portal.md).
- Använder du flera lagringskonton för att öka inträngnings-, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här fallet rekommenderar Microsoft att du drar nytta av ökade gränser för lagringskonton för att minska antalet lagringskonton som krävs för din arbetsbelastning om möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagringskonto. Mer information finns i [Presentera lagringskonton i större skala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitets- och transaktionsmål

Om ditt program närmar sig skalbarhetsmålen för ett enda lagringskonto kan du överväga att använda någon av följande metoder:  

- Om ditt program når transaktionsmålet kan du överväga att använda block blob storage-konton, som är optimerade för höga transaktionshastigheter och låg och konsekvent svarstid. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).
- Ompröva arbetsbelastningen som gör att ditt program närmar sig eller överskrider skalbarhetsmålet. Kan du utforma det annorlunda för att använda mindre bandbredd eller kapacitet, eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhetsmålen skapar du flera lagringskonton och partitionerar dina programdata över dessa flera lagringskonton. Om du använder det här mönstret måste du utforma programmet så att du kan lägga till fler lagringskonton i framtiden för belastningsutjämning. Lagringskonton själva har ingen annan kostnad än din användning när det gäller lagrade data, transaktioner eller data som överförs.
- Om ditt program närmar sig bandbreddsmålen bör du överväga att komprimera data på klientsidan för att minska den bandbredd som krävs för att skicka data till Azure Storage.
    Komprimera data kan spara bandbredd och förbättra nätverkets prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestandapåverkan av de ytterligare bearbetningskraven för datakomprimering och dekompression på klientsidan. Tänk på att lagring av komprimerade data kan göra det svårare att felsöka eftersom det kan vara svårare att visa data med standardverktyg.
- Om ditt program närmar sig skalbarhetsmålen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhetsmålen genom att implementera rekommendationerna som beskrivs i den här artikeln. Men om du använder en exponentiell backoff för återförsök kommer programmet att förhindra att programmet försöker igen snabbt, vilket kan göra begränsningen värre. Mer information finns i avsnittet [Timeout och Server Busy errors](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Flera klienter som har åtkomst till en enda blob samtidigt

Om du har ett stort antal klienter som har åtkomst till en enda blob samtidigt måste du överväga både skalbarhetsmål per blob och per lagringskonto. Det exakta antalet klienter som kan komma åt en enda blob varierar beroende på faktorer som antalet klienter som begär blob samtidigt, storleken på blobben och nätverksvillkoren.

Om blobben kan distribueras via ett CDN, till exempel bilder eller videor som visas från en webbplats, kan du använda ett CDN. Mer information finns i avsnittet [Innehållsdistribution](#content-distribution).

I andra scenarier, till exempel vetenskapliga simuleringar där data är konfidentiella, har du två alternativ. Den första är att sprida din arbetsbelastnings åtkomst så att blobben nås under en tidsperiod jämfört med åtkomst samtidigt. Alternativt kan du tillfälligt kopiera blobben till flera lagringskonton för att öka den totala IOPS per blob och över lagringskonton. Resultaten varierar beroende på programmets beteende, så se till att testa samtidighetsmönster under designen.

### <a name="bandwidth-and-operations-per-blob"></a>Bandbredd och åtgärder per blob

En enda blob stöder upp till 500 begäranden per sekund. Om du har flera klienter som behöver läsa samma blob och du kan överskrida den här gränsen bör du överväga att använda ett blockbloloblagringskonto. Ett blockbloloblagringskonto ger en högre begärandehastighet eller I/O-åtgärder per sekund (IOPS).

Du kan också använda ett CDN-nätverk (Content Delivery Network), till exempel Azure CDN för att distribuera åtgärder på blobben. Mer information om Azure CDN finns i [Azure CDN översikt](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partitionering

Att förstå hur Azure Storage partitionerar dina blob-data är användbart för att förbättra prestanda. Azure Storage kan hantera data i en enda partition snabbare än data som sträcker sig över flera partitioner. Genom att namnge dina blobbar på rätt sätt kan du förbättra effektiviteten i läsbegäranden.

Blob-lagring använder ett intervallbaserat partitioneringsschema för skalning och belastningsutjämning. Varje blob har en partitionsnyckel som består av det fullständiga blobnamnet (account+container+blob). Partitionsnyckeln används för att partitionera blob-data i områden. Intervallen är sedan belastningsbalanserade över Blob-lagring.

Intervallbaserad partitionering innebär att namngivningskonventioner som använder lexikal beställning (till exempel *mypayroll,* *myperformance*, *myemployees*, etc.) eller tidsstämplar *(log20160101*, *log20160102*, *log20160102*, etc.) är mer benägna att resultera i att partitionerna samlokalgörs på samma partitionsserver. , tills ökad belastning kräver att de delas upp i mindre områden. Samlokalisering av blobbar på samma partitionsserver förbättrar prestanda, så en viktig del av prestandaförbättringarna innebär att namnge blobbar på ett sätt som organiserar dem mest effektivt.

Till exempel kan alla blobbar i en behållare betjänas av en enda server tills belastningen på dessa blobbar kräver ytterligare ombalansering av partitionsintervallen. På samma sätt kan en grupp lättinlästa konton med sina namn ordnade i lexikal ordning betjänas av en enda server tills belastningen på ett eller alla dessa konton kräver att de delas upp mellan flera partitionsservrar.

Varje belastningsutjämning kan påverka svarstiden för lagringsanrop under åtgärden. Tjänstens förmåga att hantera en plötslig explosion av trafik till en partition begränsas av skalbarheten för en enda partitionsserver tills belastningsutjämningsåtgärden startar och balanserar partitionsnyckelintervallet.

Du kan följa några metodtips för att minska frekvensen för sådana åtgärder.  

- Använd om möjligt blob- eller blockstorlekar som är större än 4 MiB för standardlagringskonton och större än 256 KiB för premiumlagringskonton. Större blob- eller blockstorlekar aktiverar automatiskt blockblobar med högt dataflöde. Blockblobar med hög dataflöde ger högpresterande intag som inte påverkas av partitionsnamn.
- Undersök namngivningskonventionen som du använder för konton, behållare, blobbar, tabeller och köer. Överväg att prefixera konto-, behållar- eller blobnamn med en tresiffrig hash med hjälp av en hash-funktion som bäst passar dina behov.
- Om du ordnar dina data med hjälp av tidsstämplar eller numeriska identifierare kontrollerar du att du inte använder ett trafikmönster som endast läggs till (eller endast för prepend). Dessa mönster är inte lämpliga för ett intervallbaserat partitioneringssystem. Dessa mönster kan leda till att all trafik går till en enda partition och begränsar systemet från effektivt belastningsutjämning.

    Om du till exempel har dagliga åtgärder som använder en blob med en tidsstämpel, till exempel *yyyymmdd,* dirigeras all trafik för den dagliga åtgärden till en enda blob, som betjänas av en enda partitionsserver. Överväg om per-blob gränser och per partition gränser uppfyller dina behov, och överväga att dela upp den här åtgärden i flera blobbar om det behövs. Om du lagrar tidsseriedata i tabellerna kan all trafik dirigeras till den sista delen av nyckelnamnområdet. Om du använder numeriska ID:er prefixar ID:et med ett tresiffrigt hash. Om du använder tidsstämplar prefix tidsstämpeln med sekunder värde, till exempel *ssyyyymmdd*. Om ditt program rutinmässigt utför listnings- och frågeåtgärder väljer du en hash-funktion som begränsar antalet frågor. I vissa fall kan ett slumpmässigt prefix vara tillräckligt.
  
- Mer information om partitioneringsschemat som används i Azure Storage finns i [Azure Storage: En molnbaserad molnlagringstjänst med stark konsekvens](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Nätverk

Programmets fysiska nätverksbegränsningar kan ha en betydande inverkan på prestanda. I följande avsnitt beskrivs några av de begränsningar som användare kan stöta på.  

### <a name="client-network-capability"></a>Klientnätverkskapacitet

Bandbredd och nätverkslänkens kvalitet spelar viktiga roller i programmets prestanda, enligt beskrivningen i följande avsnitt.

#### <a name="throughput"></a>Dataflöde

För bandbredd är problemet ofta klientens funktioner. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller fler virtuella datorer om du behöver högre nätverksgränser från en enda dator. Om du använder Azure Storage från ett lokalt program gäller samma regel: förstå klientenhetens nätverksfunktioner och nätverksanslutningen till Azure Storage-platsen och antingen förbättra dem efter behov eller utforma ditt program så att det fungerar inom deras funktioner.

#### <a name="link-quality"></a>Länkkvalitet

Precis som med all nätverksanvändning bör du tänka på att nätverksförhållanden som leder till fel och paketförlust kommer att göra effektivt dataflöde långsammare.  Använda WireShark eller NetMon kan hjälpa till att diagnostisera detta problem.  

### <a name="location"></a>Location

I alla distribuerade miljöer ger placera klienten nära servern bästa prestanda. För åtkomst till Azure Storage med den lägsta svarstiden är den bästa platsen för din klient inom samma Azure-region. Om du till exempel har en Azure-webbapp som använder Azure Storage letar du reda på dem både inom en enda region, till exempel USA Väst eller Asien sydost. Samlokalisering av resurser minskar svarstiden och kostnaden, eftersom bandbreddsanvändningen inom en enda region är gratis.  

Om klientprogram kommer åt Azure Storage men inte finns i Azure, till exempel mobilappar eller lokala företagstjänster, kan det minska svarstiden för att hitta lagringskontot i en region nära dessa klienter. Om dina kunder är brett fördelade (till exempel vissa i Nordamerika och några i Europa) kan du överväga att använda ett lagringskonto per region. Den här metoden är enklare att implementera om de data som programmet lagrar är specifika för enskilda användare och inte kräver att data replikeras mellan lagringskonton.

För bred distribution av blob-innehåll använder du ett nätverk för att leverera innehåll, till exempel Azure CDN. Mer information om Azure CDN finns i [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS och CORS

Anta att du behöver auktorisera kod som JavaScript som körs i en användares webbläsare eller i en mobiltelefonapp för att komma åt data i Azure Storage. En metod är att skapa ett tjänstprogram som fungerar som en proxy. Användarens enhet autentiserar med tjänsten, vilket i sin tur ger åtkomst till Azure Storage-resurser. På så sätt kan du undvika att exponera dina lagringskontonycklar på osäkra enheter. Den här metoden placerar dock en betydande omkostnader för tjänstprogrammet, eftersom alla data som överförs mellan användarens enhet och Azure Storage måste passera genom tjänstprogrammet.

Du kan undvika att använda ett tjänstprogram som en proxy för Azure Storage med hjälp av SIGNATUREr för delad åtkomst (SAS). Med SAS kan du aktivera användarens enhet för att göra förfrågningar direkt till Azure Storage med hjälp av en begränsad åtkomsttoken. Om en användare till exempel vill ladda upp ett foto till ditt program kan ditt tjänstprogram generera en SAS och skicka det till användarens enhet. SAS-token kan bevilja behörighet att skriva till en Azure Storage-resurs för ett angivet tidsintervall, varefter SAS-token upphör att gälla. Mer information om SAS finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../common/storage-sas-overview.md)  

Vanligtvis tillåter en webbläsare inte JavaScript på en sida som finns värd för en webbplats på en domän för att utföra vissa åtgärder, till exempel skrivåtgärder, till en annan domän. Den här principen kallas principen för samma ursprung och förhindrar att ett skadligt skript på en sida får åtkomst till data på en annan webbsida. Principen med samma ursprung kan dock vara en begränsning när du skapar en lösning i molnet. Kors-ursprungsresursdelning (CORS) är en webbläsarfunktion som gör att måldomänen kan kommunicera med webbläsaren om att den litar på begäranden som har sitt ursprung i källdomänen.

Anta till exempel att ett webbprogram som körs i Azure gör en begäran om en resurs till ett Azure Storage-konto. Webbprogrammet är källdomänen och lagringskontot är måldomänen. Du kan konfigurera CORS för någon av Azure Storage-tjänsterna för att kommunicera med webbläsaren som begäranden från källdomänen är betrodda av Azure Storage. Mer information om CORS finns i [CORS-stöd (Cross-Origin Resource Sharing) för Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Både SAS och CORS kan hjälpa dig att undvika onödig belastning på ditt webbprogram.  

## <a name="caching"></a>Caching

Caching spelar en viktig roll i prestanda. I följande avsnitt beskrivs metodtips för cachelagring.

### <a name="reading-data"></a>Läsa data

I allmänhet är läsdata en gång att föredra framför att läsa dem två gånger. Tänk på exemplet med ett webbprogram som har hämtat en 50 MiB-blob från Azure Storage för att fungera som innehåll för en användare. Helst cachelagrar programmet bloben lokalt till disk och hämtar sedan den cachelagrade versionen för efterföljande användarbegäranden.

Ett sätt att undvika att hämta en blob om den inte har ändrats sedan den cachelagrades är att kvalificera GET-åtgärden med ett villkorligt huvud för ändringstid. Om den senaste ändrade tiden är efter den tidpunkt då blobben cachelagrades, hämtas bloben och cachelagras om. Annars hämtas den cachelagrade bloben för optimal prestanda.

Du kan också välja att utforma ditt program för att anta att blobben förblir oförändrad under en kort period efter att ha hämtat den. I det här fallet behöver programmet inte kontrollera om blobben har ändrats under det intervallet.

Konfigurationsdata, uppslagsdata och andra data som ofta används av programmet är bra kandidater för cachelagring.  

Mer information om hur du använder villkorsstyrda huvuden finns i [Ange villkorsstyrda huvuden för Blob-tjänståtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Ladda upp data i batchar

I vissa fall kan du aggregera data lokalt och sedan regelbundet ladda upp dem i en batch i stället för att ladda upp varje bit data omedelbart. Anta till exempel att ett webbprogram behåller en loggfil med aktiviteter. Programmet kan antingen ladda upp information om varje aktivitet när det händer med en tabell (som kräver många lagringsåtgärder), eller så kan det spara aktivitetsinformation till en lokal loggfil och sedan regelbundet överföra alla aktivitetsinformation som en avgränsad fil till en blob. Om varje loggpost är 1 KB i storlek kan du ladda upp tusentals poster i en enda transaktion. En enda transaktion stöder uppladdning av en klump på upp till 64 MiB i storlek. Programutvecklaren måste utforma för möjligheten av klientenhet eller uppladdningsfel. Om aktivitetsdata behöver hämtas för ett tidsintervall i stället för för en enskild aktivitet, rekommenderas bloblagring via tabelllagring.

## <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visas flera snabba konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar.  Om du använder andra språk kontrollerar du om liknande begrepp gäller på det språk du valt.  

### <a name="use-net-core"></a>Använd .NET Core

Utveckla dina Azure Storage-program med .NET Core 2.1 eller senare för att dra nytta av prestandaförbättringar. Användning av .NET Core 3.x rekommenderas när det är möjligt.

Mer information om prestandaförbättringar i .NET Core finns i följande blogginlägg:

- [Prestandaförbättringar i .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestandaförbättringar i .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Öka standardanslutningsgränsen

I .NET ökar följande kod standardanslutningsgränsen (som vanligtvis är två i en klientmiljö eller tio i en servermiljö) till 100. Vanligtvis bör du ange värdet till ungefär antalet trådar som används av ditt program. Ställ in anslutningsgränsen innan du öppnar några anslutningar.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Andra programmeringsspråk finns i dokumentationen för att avgöra hur anslutningsgränsen ska anges.  

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

## <a name="copying-and-moving-blobs"></a>Kopiera och flytta blobbar

Azure Storage innehåller ett antal lösningar för kopiering och flyttning av blobbar i ett lagringskonto, mellan lagringskonton och mellan lokala system och molnet. I det här avsnittet beskrivs några av dessa alternativ när det gäller deras effekter på prestanda. Information om hur du effektivt överför data till eller från Blob-lagring finns i [Välj en Azure-lösning för dataöverföring](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>API:er för blobkopkopiering

Om du vill kopiera blobbar över lagringskonton använder du åtgärden [Placera blockera från URL.](/rest/api/storageservices/put-block-from-url) Den här åtgärden kopierar data synkront från valfri URL-källa till en blockblob. Om `Put Block from URL` du använder åtgärden kan du avsevärt minska den bandbredd som krävs när du migrerar data över lagringskonton. Eftersom kopieringen sker på tjänstsidan behöver du inte hämta och ladda upp data igen.

Om du vill kopiera data i samma lagringskonto använder du åtgärden [Kopiera blob.](/rest/api/storageservices/Copy-Blob) Kopiering av data i samma lagringskonto slutförs vanligtvis snabbt.  

### <a name="use-azcopy"></a>Använda AzCopy

Kommandoradsverktyget AzCopy är ett enkelt och effektivt alternativ för massöverföring av blobbar till, från och över lagringskonton. AzCopy är optimerad för det här scenariot och kan uppnå höga överföringshastigheter. AzCopy version 10 `Put Block From URL` använder åtgärden för att kopiera blob-data över lagringskonton. Mer information finns i [Kopiera eller flytta data till Azure Storage med hjälp av AzCopy v10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Använda Azure-dataruta

För att importera stora mängder data till Blob-lagring bör du överväga att använda Azure Data Box-familjen för offlineöverföringar. Microsoft-medföljande Data Box-enheter är ett bra val för att flytta stora mängder data till Azure när du är begränsad av tid, nätverkstillgänglighet eller kostnader. Mer information finns i [Azure DataBox Documentation](/azure/databox/).

## <a name="content-distribution"></a>Distribution av innehåll

Ibland måste ett program visa samma innehåll för många användare (till exempel en produktdemovideo som används på en webbplatss startsida) som finns i antingen samma eller flera regioner. I det här fallet använder du ett CONTENT Delivery Network (CDN) till exempel Azure CDN för att distribuera blob-innehåll geografiskt. Till skillnad från ett Azure Storage-konto som finns i en enda region och som inte kan leverera innehåll med låg latens till andra regioner, använder Azure CDN servrar i flera datacenter runt om i världen. Dessutom kan ett CDN vanligtvis stödja mycket högre utgående gränser än ett enda lagringskonto.  

Mer information om Azure CDN finns i [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Använda metadata

Blob-tjänsten stöder HEAD-begäranden, som kan innehålla blob-egenskaper eller metadata. Om ditt program till exempel behöver Exif-data (utbytbart bildformat) från ett foto kan det hämta fotot och extrahera det. För att spara bandbredd och förbättra prestanda kan ditt program lagra Exif-data i blobens metadata när programmet laddar upp fotot. Du kan sedan hämta Exif-data i metadata med endast en HEAD-begäran. Om du hämtar endast metadata och inte det fullständiga innehållet i blobben sparas betydande bandbredd och bearbetningstiden som krävs för att extrahera Exif-data. Tänk på att 8 KiB av metadata kan lagras per blob.  

## <a name="upload-blobs-quickly"></a>Ladda upp blobbar snabbt

Om du snabbt vill ladda upp blobbar bestämmer du först om du ska ladda upp en blob eller många. Använd anvisningarna nedan för att bestämma rätt metod att använda beroende på ditt scenario.  

### <a name="upload-one-large-blob-quickly"></a>Ladda upp en stor blob snabbt

Om du snabbt vill ladda upp en enda stor blob kan ett klientprogram ladda upp sina block eller sidor parallellt, med tanke på skalbarhetsmålen för enskilda blobbar och lagringskontot som helhet. Azure Storage-klientbiblioteken har stöd för att ladda upp parallellt. Du kan till exempel använda följande egenskaper för att ange antalet samtidiga begäranden som tillåts i .NET eller Java. Klientbibliotek för andra språk som stöds ger liknande alternativ.

- För .NET anger du egenskapen [BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- För Java/Android anropar du metoden [BlobRequestOptions.setConcurrentRequestCount(final Integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Ladda upp många blobbar snabbt

Om du snabbt vill ladda upp många blobbar laddar du upp blobbar parallellt. Det går snabbare att ladda upp parallellt än att ladda upp enstaka blobbar åt gången med parallella blocköverföringar eftersom det sprider överföringen över flera partitioner i lagringstjänsten. AzCopy utför uppladdningar parallellt som standard och rekommenderas för det här scenariot. Mer information finns i [Komma igång med AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Välj rätt typ av blob

Azure Storage stöder blockblobar, tilläggsblobar och sidblobar. För ett visst användningsscenario påverkar ditt val av blob-typ lösningens prestanda och skalbarhet.

Blockblobar är lämpliga när du vill överföra stora mängder data effektivt. Ett klientprogram som laddar upp foton eller video till Blob-lagring skulle till exempel inriktas på blockblobar.

Lägg till blobbar liknar blockera blobbar genom att de består av block. När du ändrar en tilläggsblob läggs block endast till i slutet av bloben. Tilläggsblobbar är användbara för scenarier som loggning, när ett program behöver lägga till data i en befintlig blob.

Sidblobar är lämpliga om programmet behöver utföra slumpmässiga skrivningar på data. Azure-diskar för virtuella datorer lagras till exempel som sidblobar. Mer information finns i [Förstå blockblobar, tilläggsblobbar och sidblobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets- och prestandamål för Blob-lagring](scalability-targets.md)
- [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status- och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)
