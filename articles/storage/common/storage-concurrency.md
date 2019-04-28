---
title: Hantera samtidighet i Microsoft Azure Storage
description: Hantera samtidighet för tjänsterna Blob, kö, tabell och fil
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.subservice: common
ms.openlocfilehash: 9e786aed031d528b8ae574444b71753ac538cf47
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766190"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Hantera samtidighet i Microsoft Azure Storage
## <a name="overview"></a>Översikt
Moderna Internet-baserade program har vanligtvis flera användare visa och uppdatera data samtidigt. Detta kräver programutvecklare att tänka igenom hur du ger en förutsägbar upplevelse till slutanvändarna, särskilt för scenarier där flera användare kan uppdatera samma data. Det finns tre huvudsakliga samtidighet datastrategierna som utvecklare normalt tänka på:  

1. Optimistisk samtidighet – ett program som utför en uppdatering som en del av dess update kontrollerar om data har ändrats sedan programmet senast läsa dessa data. Till exempel om två användare som visar en wiki-sida gör en uppdatering i samma sida sedan wiki-plattformen måste se till att den andra uppdateringen inte skriver över den första uppdateringen – och att både användare förstår om uppdateringen lyckades eller inte. Den här strategin används oftast i webbprogram.
2. Pessimistisk samtidighet – ett program vill utföra en uppdatering tar ett lås på ett objekt som förhindrar att andra användare uppdaterar data förrän låset har släppts. Till exempel i ett scenario för replikering av huvudreplikering och underordnad data där endast master utför uppdateringar innehåller huvudservern vanligtvis ett exklusivt lås under en längre tid på informationen för att se till att ingen annan kan uppdatera den.
3. Den senaste skrivaren wins – en metod som gör att alla uppdateringsåtgärder att fortsätta utan att verifiera om något annat program har uppdaterats data sedan programmet först läsa data. Den här strategin (eller brist på en formell strategi) används vanligtvis där data är partitionerad så att det finns inga sannolikheten att flera användare kommer åt samma data. Det kan också vara användbar där tillfällig dataströmmar bearbetas.  

Den här artikeln innehåller en översikt över hur Azure Storage-plattformen förenklar utveckling genom att tillhandahålla förstklassigt stöd för alla tre av följande metoder för samtidighet.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure Storage – förenklar molnutveckling
Azure storage-tjänsten stöder alla tre strategier, men det är tydligt programvarusystem att har fullständigt stöd för optimistisk och pessimistisk samtidighet eftersom den har utformats för att omfatta en stark konsekvensmodell som garanterar att om den Storage-tjänsten genomför en data-insert eller uppdateringsåtgärden alla ytterligare får åtkomst till att data kommer att se den senaste uppdateringen. Storage-plattformar som använder en eventuell konsekvens har en fördröjning mellan när en skrivning utförs av en person och uppdaterade data kan ses av andra användare därför vilket komplicerar utvecklingen av klientprogram för att förhindra att inkonsekvenser från påverka slutanvändarna.  

Utöver att välja en lämplig samtidighetsstrategi vara utvecklare också medveten om hur en plattform för lagring isolerar ändringar – särskilt ändringar till samma objekt över transaktioner. Azure storage-tjänsten använder ögonblicksbildisolering för att tillåta läsåtgärder ske samtidigt med skrivåtgärder inom en enda partition. Till skillnad från andra isoleringsnivåer garanterar ögonblicksbildisolering att alla läsningar ser en konsekvent ögonblicksbild av data även om uppdateringar sker – i princip genom att returnera de sista allokerade värdena vid en uppdatering transaktioner bearbetas.  

## <a name="managing-concurrency-in-blob-storage"></a>Hantera samtidighet i Blob storage
Du kan välja för att använda antingen optimistisk eller pessimistisk samtidighet modeller för att hantera åtkomst till blobbar och behållare i blob-tjänsten. Om du inte uttryckligen anger en strategi för senaste skrivningar wins är standardinställningen.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistisk samtidighet för blobbar och behållare
Storage-tjänsten tilldelar en identifierare för varje objekt som lagras. Den här identifieraren uppdateras varje gång en uppdateringsåtgärd utförs på ett objekt. Identifieraren returneras till klienten som en del av en HTTP GET-svar med rubriken ETag (entitetstagg) som definieras i HTTP-protokollet. En användare som utför en uppdatering på sådant objekt kan skicka i den ursprungliga ETag tillsammans med en villkorlig rubrik så att en uppdatering görs bara om ett visst villkor har uppfyllts – i det här fallet villkoret är ett ”If-Match”-huvud som kräver lagringstjänst t o Kontrollera värdet för den ETag som anges i begäran om uppdatering är samma som lagras i lagringstjänsten.  

Översikt över den här processen är följande:  

1. Hämta en blob från lagringstjänsten, svaret innehåller ett HTTP-huvud för ETag-värde som anger den aktuella versionen av objektet i lagringstjänsten.
2. När du uppdaterar blobben som innehåller ETag-värdet som du fick i steg 1 i den **If-Match** villkorlig huvudet för begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för blobben.
4. Om det aktuella ETag-värdet för bloben är en annan version än ETag i den **If-Match** villkorlig huvudet i begäran, tjänsten returnerar en 412 fel till klienten. Detta anger att klienten att en annan process har uppdaterats blob eftersom klienten har hämtat den.
5. Om det aktuella ETag-värdet för bloben är samma version som ETag i den **If-Match** villkorlig huvudet i begäran, tjänsten utförs den begärda åtgärden och uppdaterar det aktuella ETag-värdet för blobben som ska visa att den har skapats en ny version.  

C# kodfragmentet nedan (med Storage-klientbiblioteket 4.2.0) visar ett enkelt exempel på hur du skapar en **If-Match AccessCondition** baserat på ETag-värdet som kan nås från egenskaperna för en blob som tidigare var antingen Hämta eller infogas. Därefter använder den **AccessCondition** objekt när den uppdaterar blob: den **AccessCondition** objektet lägger till den **If-Match** huvud i begäran. Om en annan process har uppdaterat blob, returnerar blob service ett statusmeddelande för HTTP 412 (Förhandsvillkoret misslyckades). Du kan hämta det fullständiga exemplet: [Hantera samtidighet med hjälp av Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Lagringstjänsten finns också stöd för ytterligare villkorlig rubriker som **om ändras sedan**, **om ska ändras sedan** och **If-None-Match** samt kombinationer av dessa. Mer information finns i [att ange villkorlig rubriker för Blob-tjänståtgärder](https://msdn.microsoft.com/library/azure/dd179371.aspx) på MSDN.  

I följande tabell sammanfattas container-åtgärder som accepterar villkorlig rubriker som **If-Match** i begäran och att returnera ett ETag-värdet i svaret.  

| Åtgärd | Returnerar behållare ETag-värdet | Accepterar villkorlig rubriker |
|:--- |:--- |:--- |
| Skapa behållare |Ja |Nej |
| Hämta egenskaper för behållare |Ja |Nej |
| Hämta Metadata för behållaren |Ja |Nej |
| Ställ in Metadata för behållaren |Ja |Ja |
| Hämta ACL-behållare |Ja |Nej |
| Ange behållaren ACL |Ja |Ja (*) |
| Ta bort behållare |Nej |Ja |
| Lånet behållare |Ja |Ja |
| Lista Blobar |Nej |Nej |

(*) De behörigheter som definierats av SetContainerACL cachelagras och uppdateringar av dessa behörigheter ta 30 sekunder att sprida uppdateringar inte garanteras under denna tid är konsekvent.  

I följande tabell sammanfattas blobåtgärder som accepterar villkorlig rubriker som **If-Match** i begäran och att returnera ett ETag-värdet i svaret.

| Åtgärd | Returnerar ETag-värdet | Accepterar villkorlig rubriker |
|:--- |:--- |:--- |
| Put Blob |Ja |Ja |
| Hämta Blob |Ja |Ja |
| Hämta Blobegenskaper |Ja |Ja |
| Ange Blob-egenskaper |Ja |Ja |
| Hämta blob-metadata |Ja |Ja |
| Ange Blob-Metadata |Ja |Ja |
| Lease Blob (*) |Ja |Ja |
| Ta ögonblicksbild av Blob |Ja |Ja |
| Kopiera Blob |Ja |Ja (för käll- och blob) |
| Avbryt kopiering av Blob |Nej |Nej |
| Ta bort Blob |Nej |Ja |
| Placera Block |Nej |Nej |
| Placera Blockeringslista |Ja |Ja |
| Hämta lista över blockerade |Ja |Nej |
| Placera sidan |Ja |Ja |
| Get Page Ranges |Ja |Ja |

(*) Lånet Blob ändras inte ETag för en blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistisk samtidighet för BLOB
Om du vill låsa en blob för exklusiv användning, kan du hämta en [lånet](https://msdn.microsoft.com/library/azure/ee691972.aspx) på den. När du skaffa en leasing, anger du hur länge du behöver lånet: Detta kan vara för mellan 15 till 60 sekunder eller oändligt, vilket motsvarar ett exklusivt lås. Du kan förnya ett begränsad lån att utöka den och du kan släppa ett lån när du är klar med den. Blob service Frigör automatiskt begränsad lån när de går ut.  

Lån Aktivera synkronisering av olika strategier för att stödjas, inklusive exklusiva skrivning / delade skrivskyddade, exklusiva skrivning / exklusiv läsa och delade skrivning / exklusiv läsa. Om ett lån finns lagringstjänsten tillämpar exklusiv skrivåtgärder (put, ange och ta bort) men säkerställer exklusivitet för läsåtgärder kräver utvecklare så att alla klientprogram används ett lån-ID och att endast en klienten åt gången har en giltigt lån-ID. Läs-och skrivåtgärder som inte innehåller ett lån-ID resultat i delade läsningar.  

Följande C#-kodavsnitt visar ett exempel på erhålla ett exklusiv lån i 30 sekunder för en blob, uppdaterar innehållet i blobben och sedan släppa lånet. Om det finns redan ett giltigt lån i blobben när du försöker hämta ett nytt adresslån, returnerar blob service ett resultat för ”HTTP (409) konflikt” status. I följande kodfragment används en **AccessCondition** objekt att kapsla in lease-information när den gör en begäran att uppdatera blob i lagringstjänsten.  Du kan hämta det fullständiga exemplet: [Hantera samtidighet med hjälp av Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Om du försöker utföra en skrivåtgärd i en utlånat blob utan att passera lån-ID, misslyckas denna begäran med ett 412 fel. Observera att om lånet går ut innan du anropar den **UploadText** metoden, men du fortfarande skicka lån-ID, förfrågan genererar ett fel med en **412** fel. Mer information om hur du hanterar lånetid för förfallodatum och lånetiden ID: N finns i den [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST-dokumentation.  

Följande blobåtgärder för kan använda lån för att hantera pessimistisk samtidighet:  

* Put Blob
* Hämta Blob
* Hämta Blobegenskaper
* Ange Blob-egenskaper
* Hämta blob-metadata
* Ange Blob-Metadata
* Ta bort Blob
* Placera Block
* Placera Blockeringslista
* Hämta lista över blockerade
* Placera sidan
* Get Page Ranges
* Ta ögonblicksbild av Blob - lån-ID som är valfri om ett lån finns
* Kopiering av Blob - lån-ID krävs om det finns ett lån på mål-blob
* Avbryt kopiering av Blob - lån-ID krävs om det finns en oändlig livslängd på mål-blob
* Lånet Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistisk samtidighet för behållare
Lån på behållare aktivera men samma strategier för synkronisering för stöd som på BLOB-objekt (exklusiva Skriv- / delade skrivskyddade, exklusiva skrivning / exklusiv läsa och delade skrivning / exklusiv läsa) men till skillnad från BLOB storage-tjänsten kommer bara att verkställa exklusivitet på ta bort åtgärder. Om du vill ta bort en behållare med ett aktivt lån, måste en klient med aktivt lån-ID med delete-begäran. Alla andra åtgärder i behållaren lyckas på en utlånat behållare utan att inkludera lån-ID i vilket fall de delas åtgärder. Om exklusivitet av update (put eller uppsättning) eller läsåtgärder krävs sedan utvecklare bör se till alla klienter använder ett lån-ID och som endast en klient i taget har ett giltigt lån-ID.  

Följande åtgärder för behållare kan använda lån för att hantera pessimistisk samtidighet:  

* Ta bort behållare
* Hämta egenskaper för behållare
* Hämta Metadata för behållaren
* Ställ in Metadata för behållaren
* Hämta ACL-behållare
* Ange behållaren ACL
* Lånet behållare  

Mer information finns i:  

* [Ange villkorlig rubriker för Blob Service-åtgärder](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lånet behållare](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lånet Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Hantera samtidighet i Table Service
Table service använder Optimistisk samtidighet kontrollerar som standard när du arbetar med entiteter, till skillnad från blob-tjänsten där du måste uttryckligen välja att utföra Optimistisk samtidighet kontroller. Skillnaden mellan tjänsterna som tabell- och blob är att du bara kan hantera samtidighet beteendet för entiteter medan du kan hantera samtidighet på både behållare och blobbar med blob-tjänsten.  

Att använda Optimistisk samtidighet och kontrollera om en annan process ändrats en entitet, eftersom du har hämtat den från table storage-tjänsten kan du använda ETag-värdet som du får när tabelltjänsten returnerar en entitet. Översikt över den här processen är följande:  

1. Hämta en entitet från table storage-tjänsten, svaret innehåller ett ETag-värde som anger den aktuella identifieraren som är associerade med entiteten på storage-tjänsten.
2. När du uppdaterar entiteten innehåller ETag-värdet som du fick i steg 1 i obligatoriska **If-Match** huvudet i begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för entiteten.
4. Om det aktuella ETag-värdet för entiteten skiljer sig från ETag i obligatoriska **If-Match** huvudet i begäran, tjänsten returnerar en 412 fel till klienten. Detta anger att klienten att en annan process har uppdaterats entiteten eftersom klienten har hämtat den.
5. Om det aktuella ETag-värdet för entiteten är samma som ETag i obligatoriska **If-Match** Värdrubriken i begäran eller **If-Match** huvudet innehåller jokertecken (*), utför tjänsten den den begärda åtgärden och uppdaterar det aktuella ETag-värdet för entiteten för att visa att den har uppdaterats.  

Observera att tabelltjänsten till skillnad från blob-tjänsten kräver att klienten kan innehålla en **If-Match** huvud i begäranden om att uppdatera. Det är dock möjligt att framtvinga en ovillkorlig uppdatera (senaste skrivaren wins strategi) och kringgå samtidighet kontrollerar om klienten anger den **If-Match** sidhuvud till jokertecknet (*) i begäran.  

C# kodfragmentet nedan visar en kundentitet som skapades tidigare antingen eller hämtas med e-postadressen uppdateras. Första infoga eller och hämta åtgärden butiker ETag-värdet i kund-objektet, eftersom i exemplet används samma objektinstans när ersättningsåtgärden körs, som skickar automatiskt ETag-värdet tillbaka till table service, aktivera tjänsten Sök efter samtidighet överträdelser. Om en annan process har uppdaterat entiteten i tabellagring, returnerar tjänsten ett statusmeddelande för HTTP 412 (Förhandsvillkoret misslyckades).  Du kan hämta det fullständiga exemplet: [Hantera samtidighet med hjälp av Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Om du vill inaktivera simultankontroll, bör du ange den **ETag** egenskapen för den **medarbetare** objektet till ”*” innan du kan köra ersättningsåtgärden.  

```csharp
customer.ETag = "*";  
```

I följande tabell sammanfattas hur entiteten tabellåtgärder använda ETag värden:

| Åtgärd | Returnerar ETag-värdet | Kräver att If-Match-huvudet för begäran |
|:--- |:--- |:--- |
| Kör frågor mot entiteter |Ja |Nej |
| Infoga enhet |Ja |Nej |
| Uppdatera entitet |Ja |Ja |
| Sammanslå enhet |Ja |Ja |
| Ta bort enheten |Nej |Ja |
| Infoga eller ersätta enhet |Ja |Nej |
| Infoga eller sammanslå enhet |Ja |Nej |

Observera att den **infoga eller ersätta entitet** och **Insert eller sammanfoga entitet** operations gör *inte* utföra eventuella samtidighet kontroller eftersom de inte skickar en ETag-värdet till tabellen tjänsten.  

I allmänhet utvecklare som använder tabeller bör förlita dig på Optimistisk samtidighet när du utvecklar skalbara program. Om pessimistisk låsning krävs kan en metod utvecklarna dessutom när åtkomst till tabeller är att koppla en blob som är avsedda för varje tabell och försök att ta ett lån på blobben innan körs på tabellen. Den här metoden kräver programmet så att alla data access-sökvägar får lån innan de körs på tabellen. Observera även som minsta lånetiden är 15 sekunder som kräver noggrann överväganden för skalbarhet.  

Mer information finns i:  

* [Åtgärder för entiteter](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Hantera samtidighet i kötjänsten
Ett scenario som är viktig i kö-tjänsten i vilken samtidighet är där flera klienter hämtar meddelanden från en kö. När du hämtar ett meddelande från kön, innehåller svaret meddelandet och en pop-kvittovärde, vilket krävs för att ta bort meddelandet. Meddelandet tas inte bort automatiskt från kön, men när det har hämtats, det är inte synliga för andra klienter i tidsintervall som anges av parametern visibilitytimeout. Klienten som hämtar meddelanden som förväntas ta bort meddelandet efter att de har bearbetats och innan den tid som anges av TimeNextVisible element i svaret, som beräknas baserat på värdet för parametern visibilitytimeout. Värdet för visibilitytimeout läggs till den tidpunkt då meddelandet hämtas för att fastställa värdet för TimeNextVisible.  

Kötjänsten har inte stöd för optimistisk eller pessimistisk samtidighet och för den här orsak klienter bearbetning av meddelanden som har hämtats från en kö bör kontrollera meddelandena behandlas på ett sätt som idempotenta. En strategi för senaste skrivaren wins används för update-åtgärder som till exempel SetQueueServiceProperties, SetQueueMetaData, SetQueueACL och UpdateMessage.  

Mer information finns i:  

* [REST API för kötjänst](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Hantera samtidighet i tjänsten
Tjänsten kan nås med hjälp av två olika protokollslutpunkterna – SMB- och REST. REST-tjänst har inte stöd för optimistisk låsning eller pessimistisk låsning och alla uppdateringar kommer att följa en strategi för senaste skrivaren wins. SMB-klienter som montera filresurser kan använda filen system låsning mekanismer för att hantera åtkomst till delade filer – inklusive möjligheten att utföra pessimistisk låsning. När en SMB-klient öppnar en fil, anger både filåtkomst och dela läge. Ange ett alternativ för åtkomst till filen för ”skriva” eller ”full” tillsammans med en filresurs läge ”NONE” resulterar i filen är låst av en SMB-klient förrän filen stängs. Om REST-åtgärden utförs på en fil där en SMB-klient har låst filen kommer REST-tjänst returnera statuskod 409 (konflikt) med felkoden SharingViolation.  

När en SMB-klient öppnar en fil att ta bort, markerar filen som väntar på borttagning tills alla andra SMB-klienten öppna referenser i filen är stängd. När en fil har markerats som väntar på borttagning, returneras alla REST-åtgärden på filen statuskod 409 (konflikt) med felkoden SMBDeletePending. Statuskod 404 (hittades inte) returneras inte eftersom det är möjligt för SMB-klienten att ta bort flaggan väntande borttagning innan du stänga den. Statuskod 404 (hittades inte) förväntas med andra ord bara när filen har tagits bort. Observera att när en fil är i ett SMB väntetillstånd ta bort, inte tas den med i resultatet lista filer. Observera också att REST ta bort fil- och REST ta bort operations genomförs atomiskt och resulterar inte i ett tillstånd med väntande borttagning.  

Mer information finns i:  

* [Hantera filen låser](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Sammanfattning och nästa steg
Tjänsten Microsoft Azure Storage har utformats för att uppfylla behoven hos de mest komplexa online program utan att utvecklare kan äventyra eller omvärderar viktiga designbeslut antaganden, till exempel samtidighet och datakonsekvens som de har du kommit till för beviljas.  

För komplett exempelprogrammet refereras till i den här bloggen:  

* [Hantera samtidighet med hjälp av Azure Storage - exempelprogrammet](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Mer information om Azure Storage finns:  

* [Startsida för Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Introduktion till Azure Storage](storage-introduction.md)
* Kom igång för Storage [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabell](../../cosmos-db/table-storage-how-to-use-dotnet.md), [köer](../storage-dotnet-how-to-use-queues.md), och [filer](../storage-dotnet-how-to-use-files.md)
* Lagringsarkitektur – [Azure Storage: En högtillgänglig Molnlagringstjänst med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

