---
title: Hantera samtidighet i Microsoft Azure Storage
description: "Så här hanterar du samtidighet för Blob, kön, tabell och filen tjänster"
services: storage
documentationcenter: 
author: jasontang501
manager: tadb
editor: tysonn
ms.assetid: cc6429c4-23ee-46e3-b22d-50dd68bd4680
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.openlocfilehash: 937cca66a0af0674b868e6a87681adbea330e91c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Hantera samtidighet i Microsoft Azure Storage
## <a name="overview"></a>Översikt
Moderna Internet-baserade program har vanligtvis flera användare visa och uppdatera data samtidigt. Detta kräver programutvecklare att tänka igenom hur du skapar en förutsägbar användarupplevelse till sina slutanvändare, särskilt för scenarier där flera användare kan uppdatera samma data. Det finns tre huvudsakliga databassamtidighet strategier som utvecklare vanligtvis tänka på:  

1. Optimistisk samtidighet – ett program som utför en uppdatering som en del av dess uppdatering verifierar om data har ändrats sedan programmet senast läsa data. Till exempel om två användare som visar en wiki-sida gör en uppdatering på samma sida sedan wiki-plattformen måste se till att den andra uppdateringen inte skriver över den första uppdateringen – och att båda användarna förstår om uppdateringen lyckades eller inte. Den här strategin används oftast i webbprogram.
2. Sämsta samtidighet – ett program behöver utföra en uppdatering tar ett lås på ett objekt som förhindrar att andra användare uppdaterar data förrän låset frigörs. Till exempel i över-/ underordnade data replikering scenario där endast master utför uppdateringar innehåller huvudservern vanligtvis ett exklusivt lås för en längre tidsperiod data för att se till att ingen annan kan uppdatera den.
3. Den senaste skrivaren wins – en metod som gör att alla uppdateringsåtgärder fortsätta utan att verifiera om något annat program har uppdaterats data eftersom programmet först läsa data. Den här strategin (eller brist på en formell strategi) används vanligtvis när data är partitionerad så att det finns inga sannolikheten att flera användare får åtkomst till samma data. Det kan också vara användbara där tillfällig dataströmmar bearbetas.  

Den här artikeln innehåller en översikt över hur Azure Storage-plattformen förenklar utvecklingen som har förstklassigt stöd för alla tre strategierna samtidighet.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure-lagring – förenklar utvecklingen i molnet
Azure storage-tjänst stöder alla tre strategier, men det är olika i förmåga att har fullständigt stöd för bästa och sämsta samtidighet eftersom den har utformats för att omfatta en stark konsekvens-modell som säkerställer att när lagringstjänsten genomför en data-insert eller update-åtgärden alla ytterligare har åtkomst till till att data kommer att se den senaste uppdateringen. Storage-plattformar som använder en modell för slutliga konsekvensen har en fördröjning mellan när en skrivning utförs av en användare och uppdaterade data kan ses av andra användare således vilket komplicerar utvecklingen av klientprogram för att förhindra att inkonsekvenser påverkar slutanvändarna.  

Förutom att välja en lämplig samtidighetsstrategi bör utvecklare också känna till hur en plattform för lagring isolerar ändringar – särskilt ändringar till samma objekt över transaktioner. Azure storage-tjänst använder ögonblicksbildisolering för att tillåta läsåtgärder ske samtidigt skrivåtgärder inom en partition. Till skillnad från andra isoleringsnivåer garanterar ögonblicksbildisolering att alla Läs finns en programkonsekvent ögonblicksbild av data medan uppdateringar sker – i princip av returnerar de sista allokerat värdena när en uppdatering transaktion bearbetas.  

## <a name="managing-concurrency-in-blob-storage"></a>Hantera samtidighet i Blob storage
Du kan välja för att använda antingen bästa eller sämsta samtidighet modeller för att hantera åtkomst till blobbar och behållare i blob-tjänsten. Om du inte uttryckligen anger en strategi för senaste skrivningar wins är standardinställningen.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistisk samtidighet för blobbar och behållare
Lagringstjänsten tilldelar varje objekt som lagras en identifierare. Den här identifieraren uppdateras varje gång en update-åtgärd utförs på ett objekt. Identifieraren returneras till klienten som en del av en HTTP GET-svar med rubriken ETag (entitetstaggen) som har angetts i HTTP-protokollet. En användare som utför en uppdatering på sådant objekt kan skicka i den ursprungliga ETag tillsammans med ett villkorat huvud så att en uppdatering görs bara om ett visst villkor har uppfyllts – i det här fallet villkoret är en ”If-Match”-huvudet som Storage Service-t krävs o Kontrollera värdet för ETag som anges i begäran om uppdatering är samma som lagras i lagringstjänsten.  

Beskrivning av den här processen är följande:  

1. Hämta en blob från lagringstjänsten, svaret innehåller ett HTTP-huvud för ETag-värde som anger den aktuella versionen av objektet i lagringstjänsten.
2. När du uppdaterar blob inkluderar ETag-värde som du fick i steg 1 i den **If-Match** villkorat huvud i begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värde i förfrågan med blob aktuella ETag-värde.
4. Om det aktuella ETag-värdet för blobben är en annan version än ETag i den **If-Match** villkorat huvud i begäran, tjänsten returnerar 412 fel till klienten. Detta anger att klienten en annan process har uppdaterat blob eftersom klienten hämtades.
5. Om det aktuella ETag-värdet för blobben är samma version som ETag i den **If-Match** villkorat huvud i begäran, tjänsten utför den begärda åtgärden och uppdaterar det aktuella ETag-värdet för blob att visa att det har skapat en ny version.  

Följande på C# utdrag (med Storage-klientbiblioteket 4.2.0) visar ett enkelt exempel på hur du skapar en **If-Match AccessCondition** baserat på ETag-värde som används i egenskaperna för en blob som tagits tidigare hämtade eller infogas. Därefter använder den **AccessCondition** objekt när den uppdaterar blob: den **AccessCondition** objekt lägger till den **If-Match** sidhuvud på begäran. Om en annan process har uppdaterat blob, returnerar blob-tjänsten ett statusmeddelande HTTP 412 (villkor misslyckades). Du kan ladda ned i det fullständiga exemplet: [hantera samtidighet med hjälp av Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the etag in response.
string orignalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No etag, provided so orignal blob is overwritten (thus generating a new etag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the orignal ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(orignalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Lagringstjänsten finns också stöd för ytterligare villkorlig huvuden som **If-Modified-Since**, **If-Unmodified-Since** och **If-None-Match** samt kombinationer av dessa. Mer information finns i [ange villkorlig huvuden för Blob-tjänståtgärder](http://msdn.microsoft.com/library/azure/dd179371.aspx) på MSDN.  

I följande tabell sammanfattas åtgärderna behållare som accepterar villkorlig huvuden som **If-Match** begäran och som returnerar ett ETag-värde i svaret.  

| Åtgärd | Returnerar behållare ETag-värde | Accepterar villkorlig rubriker |
|:--- |:--- |:--- |
| Skapa en behållare |Ja |Nej |
| Hämta egenskaper för behållaren |Ja |Nej |
| Hämta Metadata för behållaren |Ja |Nej |
| Ange Metadata för behållaren |Ja |Ja |
| Hämta behållar-ACL |Ja |Nej |
| Ange behållar-ACL |Ja |Ja (*) |
| Ta bort behållaren |Nej |Ja |
| Lånet behållare |Ja |Ja |
| Lista över Blobbar |Nej |Nej |

(*) Behörigheter som definieras av SetContainerACL cachelagras och uppdateringar för dessa behörigheter ta 30 sekunder att sprida under vilka uppdateringar inte är garanterat stämma överens.  

I följande tabell sammanfattas blob-åtgärder som accepterar villkorlig huvuden som **If-Match** begäran och som returnerar ett ETag-värde i svaret.

| Åtgärd | Returnerar ETag-värde | Accepterar villkorlig rubriker |
|:--- |:--- |:--- |
| Placera Blob |Ja |Ja |
| Hämta Blob |Ja |Ja |
| Hämta Blob-egenskaper |Ja |Ja |
| Ange Blob-egenskaper |Ja |Ja |
| Hämta Blobbmetadata |Ja |Ja |
| Ange Blobbmetadata |Ja |Ja |
| Lånet Blob (*) |Ja |Ja |
| Snapshot-Blob |Ja |Ja |
| Kopiera Blob |Ja |Ja (för käll- och blob) |
| Avbryt kopiera Blob |Nej |Nej |
| Ta bort blobben |Nej |Ja |
| Placera Block |Nej |Nej |
| Placera Blockeringslista |Ja |Ja |
| Hämta listan över blockerade |Ja |Nej |
| Placera sida |Ja |Ja |
| Get-sidintervall |Ja |Ja |

(*) Lånet Blob ändras inte ETag i en blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Sämsta samtidighet för blobbar
Om du vill låsa en blob för exklusiv användning, kan du hämta en [lån](http://msdn.microsoft.com/library/azure/ee691972.aspx) på den. När du skaffa en leasing anger du hur länge du behöver lånet: Detta kan vara för mellan 15 och 60 sekunder eller oändliga som motsvarar ett exklusivt lås. Du kan förnya ett begränsat lån att utöka det och du kan släppa ett lån när du är klar med den. Blob-tjänsten Frigör ändligt lån automatiskt när de upphör att gälla.  

Lån Aktivera synkronisering av olika strategier för att stödjas, inklusive exklusiv skrivning / delade skrivskyddade, exklusiv skrivning / exklusiv Läs- och delade skrivning / läsa samtidigt. Om ett lån finns tillämpar lagringstjänsten exklusiv skrivningar (put, ange och delete-åtgärder) men säkerställt ensamrätt för läsåtgärder kräver utvecklare som gör att alla klientprogram ska använda ett lån-ID och att endast en klient i taget har ett giltigt adresslån-ID. Läs-och skrivåtgärder som inte innehåller ett lån ID resultat i delade läsningar.  

Följande C# utdrag visar ett exempel på att erhålla ett exklusiv adresslån i 30 sekunder på en blob, uppdaterar innehållet i blob och släppa lånet. Om det finns redan ett giltigt lån på blob när du försöker hämta ett nytt adresslån, returnerar blob-tjänsten ett resultat för ”HTTP (409) konflikt” status. Följande kodavsnitt använder en **AccessCondition** objekt kapsla in lease-information när den gör en begäran om att uppdatera blob storage-tjänsten.  Du kan ladda ned i det fullständiga exemplet: [hantera samtidighet med hjälp av Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Om du försöker en skrivåtgärd i en blob-lånade utan att skicka lease-ID, misslyckas denna begäran med ett 412 fel. Observera att om lånet går ut innan du anropar den **UploadText** metoden men du fortfarande skicka lease-ID, även misslyckas begäran med en **412** fel. Mer information om hur du hanterar lånetid för förfallodatum och lånetiden ID: N finns i [lån Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) REST-dokumentationen.  

Följande blob-åtgärder kan använda lån för att hantera sämsta samtidighet:  

* Placera Blob
* Hämta Blob
* Hämta Blob-egenskaper
* Ange Blob-egenskaper
* Hämta Blobbmetadata
* Ange Blobbmetadata
* Ta bort blobben
* Placera Block
* Placera Blockeringslista
* Hämta listan över blockerade
* Placera sida
* Get-sidintervall
* Ögonblicksbild Blob - lån-ID: T är valfri om ett lån finns
* Kopiera Blob - lån ID krävs om det finns ett lån på mål-blob
* Avbryt kopiera Blob - lån ID krävs om det finns en oändlig lån på mål-blob
* Lånet Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Sämsta samtidighet för behållare
Lån på behållare aktivera men samma strategier synkroniseringen ska stödjas på blobbar (exklusiv Skriv- / delade skrivskyddade, exklusiv skrivning / exklusiv Läs- och delade skrivning / samtidigt läsa) men till skillnad från BLOB storage-tjänst endast tillämpar ensamrätt på delete-åtgärder. En klient måste innehålla ID för aktiva lån med begäran om borttagning för att ta bort en behållare med en aktiv livslängd. Alla andra åtgärder i behållaren lyckas på lånade behållaren utan att inkludera lease-ID då de delas åtgärder. Om ensamrätt av update (put eller set) eller läsåtgärder krävs ska utvecklare Kontrollera alla klienter använder ett lån-ID och som endast en klient i taget har ett giltigt lease-ID.  

Följande åtgärder för behållaren kan använda lån för att hantera sämsta samtidighet:  

* Ta bort behållaren
* Hämta egenskaper för behållaren
* Hämta Metadata för behållaren
* Ange Metadata för behållaren
* Hämta behållar-ACL
* Ange behållar-ACL
* Lånet behållare  

Mer information finns i:  

* [Ange villkorlig huvuden för Blob-tjänståtgärder](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lånet behållare](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lånet Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Hantera samtidighet i Tabelltjänsten
Tabelltjänsten använder Optimistisk samtidighet kontrollerar som standard när du arbetar med entiteter, till skillnad från blob-tjänsten där du måste uttryckligen välja att utföra Optimistisk samtidighet kontroller. Skillnaden mellan tabellen och blob-tjänsterna är att du kan endast hantera samtidighet beteendet för entiteter i blob-tjänsten kan du hantera samtidighet på både behållare och blobbar.  

Använd Optimistisk samtidighet och kontrollera om en annan process har ändrats för en entitet eftersom hämtades från table storage-tjänsten, kan du använda ETag-värde som du får när tabelltjänsten returnerar en entitet. Beskrivning av den här processen är följande:  

1. Hämta en entitet från tabelltjänsten för lagring, svaret innehåller ett ETag-värde som anger den aktuella identifieraren som är associerade med den entiteten i lagringstjänsten.
2. När du uppdaterar entiteten innehåller ETag-värde som du fick i steg 1 i obligatoriska **If-Match** huvudet i begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värde i förfrågan med det aktuella ETag-värdet för entiteten.
4. Om det aktuella ETag-värdet för entiteten skiljer sig ETag i obligatoriska **If-Match** huvud i begäran tjänsten returnerar 412 fel till klienten. Detta anger att klienten en annan process har uppdaterat entiteten eftersom klienten hämtades.
5. Om det aktuella ETag-värdet för entiteten är samma som ETag i obligatoriska **If-Match** huvud i begäran eller **If-Match** huvudet innehåller jokertecken (*), tjänsten utför den begärda åtgärden och uppdaterar det aktuella ETag-värdet för entiteten ska visa att det har uppdaterats.  

Observera att tabelltjänsten till skillnad från blob-tjänsten kräver att klienten kan innehålla en **If-Match** huvudet i begäranden om att uppdatera. Det är dock möjligt att framtvinga en ovillkorlig uppdatera (senaste skrivaren WINS-strategi) och kringgå samtidighet kontrollerar om klienten anger den **If-Match** sidhuvud till jokertecknet (*) i begäran.  

Följande C#-fragment visar en kundentitet som skapades tidigare antingen eller hämtas med deras e-postadress som uppdateras. Första infoga eller hämta åtgärden lagrar ETag-värde i kund-objektet och eftersom samma objektinstansen används när den körs ersättningsåtgärden, skickas automatiskt ETag-värdet tillbaka till tabelltjänsten att aktivera tjänsten för att söka efter samtidighet överträdelser. Om en annan process har uppdaterat entiteten i table storage, returnerar tjänsten ett statusmeddelande HTTP 412 (villkor misslyckades).  Du kan ladda ned i det fullständiga exemplet: [hantera samtidighet med hjälp av Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

För att uttryckligen inaktivera kontrollen av samtidighet, bör du ange den **ETag** -egenskapen för den **medarbetare** objekt ”*” innan du kan köra ersättningsåtgärden.  

```csharp
customer.ETag = "*";  
```

I följande tabell sammanfattas hur entiteten tabellåtgärder använda ETag-värden:

| Åtgärd | Returnerar ETag-värde | Kräver If-Match huvudet i begäran |
|:--- |:--- |:--- |
| Fråga entiteter |Ja |Nej |
| Infoga entitet |Ja |Nej |
| Uppdatera entitet |Ja |Ja |
| Sammanfoga entitet |Ja |Ja |
| Ta bort entiteten |Nej |Ja |
| Infoga eller ersätta en entitet |Ja |Nej |
| Infoga- eller Merge-entitet |Ja |Nej |

Observera att den **infoga eller ersätta entiteten** och **Insert- eller Merge-entiteten** operations vill *inte* utföra några kontroller av samtidighet eftersom ett ETag-värde inte skickas till tabelltjänsten.  

I allmänhet utvecklare som använder tabeller bör förlitar sig på Optimistisk samtidighet när du utvecklar skalbara program. Om sämsta låsning krävs kan en metod som utvecklare ta när åtkomst till tabeller är att koppla en avsedda blob för varje tabell och försök att ta ett lån på blob innan operativsystemet i tabellen. Den här metoden kräver programmet för att se till att alla data access-sökvägar får lån innan operativsystemet i tabellen. Observera även som minsta lånetiden är 15 sekunder som kräver noggrant övervägande för skalbarhet.  

Mer information finns i:  

* [Åtgärder på enheter](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Hantera samtidighet i kötjänsten
Ett scenario i vilket concurrency är ett problem i tjänsten kö är där flera klienter hämtar meddelanden från en kö. När ett meddelande har hämtats från kön innehåller svaret meddelandet och ett pop inleverans-värde som krävs för att ta bort meddelandet. Meddelandet tas inte bort automatiskt från kön, men när den har hämtats, det är inte synliga för andra klienter i tidsintervall som anges av parametern visibilitytimeout. Klienten som hämtar meddelanden som förväntas ta bort meddelandet när den har bearbetats och innan den tid som anges av TimeNextVisible element i svaret, som beräknas baserat på värdet för parametern visibilitytimeout. Värdet för visibilitytimeout har lagts till den tid då meddelandet hämtas för att fastställa värdet för TimeNextVisible.  

Kötjänsten har inte stöd för bästa eller sämsta samtidighet och för den här orsak klienter bearbetar meddelanden som hämtas från en kö ska kontrollera meddelanden bearbetas på ett idempotent sätt. En senaste skrivaren WINS-strategi används för update-åtgärder som till exempel SetQueueServiceProperties, SetQueueMetaData, SetQueueACL och UpdateMessage.  

Mer information finns i:  

* [Kön Service REST-API](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Hämta meddelanden](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Hantera samtidighet i tjänsten
Tjänsten kan nås med hjälp av två olika protokollslutpunkterna – SMB- och REST. REST-tjänsten har inte stöd för antingen optimistisk låsning eller sämsta låser och alla uppdateringar följer en senaste skrivaren WINS-strategi. SMB-klienter som montera filresurser kan utnyttja filen system låsning mekanismer för att hantera åtkomst till delade filer – inklusive möjligheten att utföra sämsta låsning. När en SMB-klienten öppnar en fil, anger både åtkomst till filen och dela läge. Inställning åtkomst till filen för ”skriva” eller ”läsa/skriva” tillsammans med en filresurs läge ”NONE” resulterar i filen är låst av en SMB-klienten förrän filen är stängd. Om REST-åtgärden utförs på en fil där en SMB-klient har låst filen returnerar REST-tjänsten statuskod 409 (konflikt) med felkoden SharingViolation.  

När en SMB-klienten öppnar en fil att ta bort, markerar filen som väntar på borttagning förrän alla andra SMB-klienten med öppna referenser i filen är stängd. När en fil är markerad som väntar på borttagning, returneras alla REST-åtgärden på filen statuskod 409 (konflikt) med felkoden SMBDeletePending. Statuskod 404 (inget hittas) returneras inte eftersom det är möjligt för SMB-klienten att ta bort flaggan borttagning väntar innan denna fil stängdes. Statuskod 404 (inget hittas) förväntas med andra ord bara när filen har tagits bort. Observera att när en fil i en SMB väntetillstånd ta bort den inte tas med i resultatet lista filer. Observera också att åtgärderna REST ta bort fil- och REST ta bort genomförs automatiskt och inte resulterar i ett tillstånd med väntande borttagning.  

Mer information finns i:  

* [Hantera filen låser](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Sammanfattning och nästa steg.
Microsoft Azure Storage-tjänsten har utformats för att uppfylla behoven hos de mest komplexa online program utan att utvecklare att kompromettera eller Tänk om viktiga designbeslut antaganden som samtidighet och data konsekvent som de kommer att ta beviljas.  

Fullständig exempelprogrammet som hänvisas till i den här bloggen:  

* [Hantera samtidighet med hjälp av Azure Storage - exempelprogram](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Mer information om Azure Storage finns:  

* [Startsida för Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Introduktion till Azure Storage](storage-introduction.md)
* Komma igång för lagring [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabell](../../cosmos-db/table-storage-how-to-use-dotnet.md), [köer](../storage-dotnet-how-to-use-queues.md), och [filer](../storage-dotnet-how-to-use-files.md)
* Lagringsarkitektur – [Azure Storage: en högtillgänglig Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

