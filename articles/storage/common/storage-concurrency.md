---
title: Hantera samtidighet
titleSuffix: Azure Storage
description: Lär dig hur du hanterar samtidighet i Azure Storage för blobb-, kö-, tabell-och fil tjänster. Förstå de tre huvudsakliga data samtidighets strategierna som används.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b83a8bfbc79af344c4d158ee65134034db714e9c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008913"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Hantera samtidighet i Microsoft Azure Storage

Moderna Internetbaserade program har vanligt vis flera användare som visar och uppdaterar data samtidigt. Programutvecklare måste tänka noga om att tillhandahålla en förutsägbar användar upplevelse, särskilt där flera användare kan uppdatera samma data. Det finns tre huvudsakliga data samtidighets strategier som utvecklare vanligt vis funderar på:

1. Optimistisk samtidighet – ett program som gör en uppdatering, som en del av uppdateringen, kontrollerar om data har ändrats sedan programmet senast läste dessa data. Om exempelvis två användare som visar en wiki-sida gör en uppdatering av samma sida, måste wiki-plattformen se till att den andra uppdateringen inte skriver över den första uppdateringen, och att båda användarna förstår om deras uppdatering lyckades eller inte. Den här strategin används oftast i webb program.
2. Pessimistisk concurrency – ett program som söker efter en uppdatering tar ett lås på ett objekt, vilket hindrar andra användare från att uppdatera data tills låset släpps.
3. Senaste skrivarens WINS – en metod som gör det möjligt för alla uppdaterings åtgärder att fortsätta utan att kontrol lera om något annat program har uppdaterat data sedan programmet först läst data. Den här strategin används ofta när data har partitionerats, så det är ingen sannolikhet att flera användare kommer åt samma data. Det kan också vara användbart när korta data strömmar bearbetas.

Den här artikeln innehåller en översikt över hur Azure Storage fören klar utvecklingen genom att stödja alla tre samtidiga strategierna.

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage fören klar moln utvecklingen

Azure Storage har stöd för alla tre strategier, även om det är en tydlig möjlighet att ge fullständig support för optimistisk och pessimistisk samtidighet. Azure Storage har utformats för att omfatta en stark konsekvens modell, vilket garanterar att när en data infogning eller uppdaterings åtgärd bearbetas, kommer alla ytterligare åtkomst till dessa data att se den senaste uppdateringen. Lagringspooler som använder en eventuell konsekvens modell har en fördröjning mellan när en skrivning utförs av en användare och när de uppdaterade data kan ses av andra användare.

Utvecklare bör också vara medvetna om hur en lagrings plattform isolerar ändring – särskilt ändringar av samma objekt i transaktioner. Den Azure Storage tjänsten använder ögonblicks bild isolering för att tillåta att Läs åtgärder sker samtidigt med Skriv åtgärder inom en enda partition. Till skillnad från andra isolerings nivåer garanterar isolering av ögonblicks bilder att alla läsningar ser en konsekvent ögonblicks bild av data även när uppdateringar sker – i huvudsak genom att returnera de senaste allokerade värdena medan en uppdaterings transaktion bearbetas.

## <a name="managing-concurrency-in-blob-storage"></a>Hantera samtidighet i Blob Storage

Du kan välja att använda antingen optimistisk eller pessimistisk samtidighets modell för att hantera åtkomst till blobbar och behållare i Blob Service. Om du inte uttryckligen anger en strategi är den senaste skrivningen av WINS standardvärdet.

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistisk samtidighet för blobbar och behållare

Lagrings tjänsten tilldelar en identifierare till varje objekt som lagras. Den här identifieraren uppdateras varje gång en uppdaterings åtgärd utförs på ett objekt. Identifieraren returneras till klienten som en del av ett HTTP GET-svar med hjälp av ETag-huvudet (Entity tag) som definieras i HTTP-protokollet. En användare som gör en uppdatering av ett sådant objekt kan skicka i den ursprungliga ETagen tillsammans med en villkorlig rubrik för att säkerställa att en uppdatering endast sker om ett visst villkor har uppfyllts – i det här fallet är villkoret ett "If-Match"-huvud, som kräver lagrings tjänsten för att säkerställa att värdet för ETag som anges i uppdateringsbegäran är detsamma som det som lagras i lagrings

Den här processens översikt är följande:

1. Hämta en BLOB från lagrings tjänsten, svaret innehåller ett HTTP ETag-huvud värde som identifierar den aktuella versionen av objektet i lagrings tjänsten.
2. När du uppdaterar blobben inkluderar du ETag-värdet du fick i steg 1 i villkors rubriken **If-Match** för begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för blobben.
4. Om det aktuella ETag-värdet för blobben är en annan version än ETag i villkors rubriken **If-Match** i begäran, returnerar tjänsten ett 412-fel till klienten. Det här felet anger att klienten som en annan process har uppdaterat blobben sedan klienten hämtade den.
5. Om det aktuella ETag-värdet för blobben har samma version som ETag i villkors rubriken **If-Match** i begäran, utför tjänsten den begärda åtgärden och uppdaterar det aktuella etag-värdet för blobben för att visa att den har skapat en ny version.

Följande C#-kodfragment (med hjälp av 4.2.0 för klient lagrings bibliotek) visar ett enkelt exempel på hur du skapar en **If-Match-AccessCondition** baserat på etag-värdet som nås från egenskaperna för en blob som tidigare har hämtats eller infogats. Den använder sedan **AccessCondition** -objektet när det uppdaterar blobben: **AccessCondition** -objektet lägger till **If-Match-** huvudet i begäran. Om en annan process har uppdaterat blobben, returnerar Blob Service ett HTTP-412 (Precondition Failed) status meddelande. Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
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

Azure Storage innehåller även stöd för villkorliga huvuden som **If-Modified-sedan**, **IF-Unmodified-sedan**, **If-None-Match** och kombination av dessa huvuden. Mer information finns i [ange villkorliga huvuden för BLOB service-åtgärder](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations).

I följande tabell sammanfattas de behållar åtgärder som accepterar villkorliga huvuden som **If-Match** i begäran och som returnerar ett ETag-värde i svaret.

| Åtgärd | Returnerar ETag-värde för behållare | Accepterar villkorliga rubriker |
|:--- |:--- |:--- |
| Skapa container |Ja |Nej |
| Hämta egenskaper för behållare |Ja |Nej |
| Hämta metadata för behållare |Ja |Nej |
| Ange metadata för behållare |Ja |Ja |
| Hämta ACL för behållare |Ja |Nej |
| Ange behållar-ACL |Yes |Ja (*) |
| Ta bort container |Nej |Ja |
| Lease container |Ja |Ja |
| Lista blobbar |Nej |Nej |

(*) Behörigheterna som definieras av SetContainerACL cachelagras och uppdateringar av de här behörigheterna tar 30 sekunder att spridas under vilka period uppdateringar inte garanterar att de är konsekventa.

I följande tabell sammanfattas de BLOB-åtgärder som accepterar villkorliga huvuden som **If-Match** i begäran och som returnerar ett ETag-värde i svaret.

| Åtgärd | Returnerar ETag-värde | Accepterar villkorliga rubriker |
|:--- |:--- |:--- |
| Placera blob |Ja |Ja |
| Hämta BLOB |Ja |Ja |
| Hämta blobegenskaper |Ja |Ja |
| Ange BLOB-egenskaper |Ja |Ja |
| Hämta BLOB-metadata |Ja |Ja |
| Ange BLOB-metadata |Ja |Ja |
| Låna BLOB (*) |Ja |Ja |
| Ta ögonblicksbild av blob |Ja |Ja |
| Kopiera blob |Yes |Ja (för käll-och mål-BLOB) |
| Avbryt kopiering av BLOB |Nej |Nej |
| Ta bort blob |Nej |Ja |
| Spärra block |Nej |Nej |
| Lista över blockerade |Ja |Ja |
| Hämta blockeringslistan |Ja |Nej |
| Placerings sida |Ja |Ja |
| Hämta sid intervall |Ja |Ja |

(*) Leasing-Bloben ändrar inte ETag i en blob.

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistisk samtidighet för blobbar

Du kan låsa en BLOB för exklusiv användning genom att skaffa ett [lån](/rest/api/storageservices/Lease-Blob) på den. När du skaffar ett lån anger du en tids period för lånet. Tids perioden sträcker sig från 15 till 60 sekunder eller oändlig, vilket uppgår till ett exklusivt lås. Förnya ett begränsat lån för att utöka det. Frigör ett lån när du är klar. Blob Storage automatiskt frigör begränsade lån när de upphör att gälla.

Lån möjliggör olika typer av synkronisering. Strategierna omfattar *exklusiv Skriv-/delad läsning*, *exklusiv skrivning/exklusiv läsning* och *delad skrivning/exklusiv läsning*. Om det finns ett lån måste Azure Storage tillämpa exklusiva skrivningar (åtgärder för att lägga till, ange och ta bort), men för att se till att alla klient program använder ett låne-ID och att endast en klient i taget har ett giltigt låne-ID. Läs åtgärder som inte innehåller ett låne-ID resulterar i delade läsningar.

Följande C#-kodfragment visar ett exempel på att förvärva ett exklusivt lån i 30 sekunder på en BLOB, uppdatera innehållet i blobben och sedan släppa lånet. Om det redan finns ett giltigt lån i blobben när du försöker hämta ett nytt lån, returnerar Blob Service status resultatet "HTTP (409) konflikt". I följande kodfragment används ett **AccessCondition** -objekt för att kapsla in låne informationen när en begäran om att uppdatera bloben i lagrings tjänsten görs.  Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Om du försöker utföra en Skriv åtgärd på en lånad BLOB utan att skicka låne-ID: t Miss lyckas begäran med ett 412-fel. Om lånet upphör att gälla innan **UploadText** -metoden anropas men du fortfarande skickar låne-ID: t Miss lyckas begäran med ett **412** -fel. Mer information om hur du hanterar förfallo tider för lån och låne-ID finns i rest-dokumentationen för [Lease-BLOB](/rest/api/storageservices/Lease-Blob) .

Följande BLOB-åtgärder kan använda lån för att hantera pessimistisk samtidighet:

* Placera blob
* Hämta BLOB
* Hämta blobegenskaper
* Ange BLOB-egenskaper
* Hämta BLOB-metadata
* Ange BLOB-metadata
* Ta bort blob
* Spärra block
* Lista över blockerade
* Hämta blockeringslistan
* Placerings sida
* Hämta sid intervall
* Ögonblicks bilds-BLOB – låne-ID valfritt om det finns ett lån
* Kopiera BLOB-låne-ID krävs om ett lån finns på mål-bloben
* Avbryt kopierings-BLOB-låne-ID krävs om det finns ett oändligt lån på mål-bloben
* Låna blob

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistisk samtidighet för behållare

Lån i behållare gör det möjligt för samma synkroniserings strategier att stödjas på blobbar (*exklusiv Skriv-/delad läsning*, *exklusiv skrivning/exklusiv läsning* och *delad Skriv-/exklusiv läsning*) men till skillnad från blobar lagrings tjänsten tillämpar exklusivitet vid borttagnings åtgärder. Om du vill ta bort en behållare med ett aktivt lån måste en klient inkludera det aktiva låne-ID: t med begäran om borttagning. Alla andra behållar åtgärder lyckas på en lånad behållare utan att inkludera låne-ID i vilket fall de delas. Om exklusivitet av uppdatering (placering eller uppsättning) eller Läs åtgärder krävs bör utvecklarna se till att alla klienter använder ett låne-ID och att endast en klient i taget har ett giltigt låne-ID.

Följande behållar åtgärder kan använda lån för att hantera pessimistisk samtidighet:

* Ta bort container
* Hämta egenskaper för behållare
* Hämta metadata för behållare
* Ange metadata för behållare
* Hämta ACL för behållare
* Ange behållar-ACL
* Lease container

Mer information finns i:

* [Ange villkorliga rubriker för Blob Service-åtgärder](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations)
* [Lease container](/rest/api/storageservices/Lease-Container)
* [Låna blob](/rest/api/storageservices/Lease-Blob)

## <a name="managing-concurrency-in-table-storage"></a>Hantera samtidighet i tabell lagring

Table service använder optimistisk samtidighets kontroller som standard beteende när du arbetar med entiteter, till skillnad från Blob Service där du uttryckligen måste välja att göra optimistiska samtidighets kontroller. Den andra skillnaden mellan tabellerna och blob-tjänsterna är att du bara kan hantera samtidighet i entiteter, men med Blob Service kan du hantera samtidigheten för både behållare och blobbar.

Om du vill använda optimistisk samtidighet och kontrol lera om en annan process har ändrat en entitet sedan du hämtade den från tabellen Storage-tjänsten, kan du använda ETag-värdet som du får när tabell tjänsten returnerar en entitet. Den här processens översikt är följande:

1. Hämta en entitet från tabellen Storage-tjänsten, svaret innehåller ett ETag-värde som identifierar den aktuella identifierare som är kopplad till entiteten i lagrings tjänsten.
2. När du uppdaterar entiteten inkluderar du ETag-värdet som du fick i steg 1 i det obligatoriska **If-Match-** huvudet för begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för entiteten.
4. Om det aktuella ETag-värdet för entiteten skiljer sig från ETag i det obligatoriska **If-Match-** huvudet i begäran returnerar tjänsten ett 412-fel till klienten. Detta indikerar klienten att en annan process har uppdaterat entiteten sedan klienten hämtade den.
5. Om det aktuella ETag-värdet för entiteten är detsamma som ETag i det obligatoriska **If-Match-** huvudet i begäran eller **om IF-match-** huvudet innehåller jokertecknet (*) gör tjänsten den begärda åtgärden och uppdaterar det aktuella etag-värdet för entiteten för att visa att den har uppdaterats.

För tabell tjänsten krävs att klienten inkluderar ett **If-Match-** huvud i uppdaterings begär Anden. Det är dock möjligt att tvinga en ovillkorlig uppdatering (senaste skrivarens WINS-strategi) och kringgå samtidighets kontroller om klienten anger **If-Match-** huvudet till jokertecknet (*) i begäran.

I följande C#-kodfragment visas en kundentitet som tidigare har skapats eller hämtats med e-postadressen uppdaterad. Den inledande INSERT-eller hämtnings åtgärden lagrar ETag-värdet i objektet kund, och eftersom exemplet använder samma objekt instans när den utför ersättnings åtgärden skickar den automatiskt ETag-värdet tillbaka till tabell tjänsten, vilket gör att tjänsten kan söka efter samtidiga överträdelser. Om en annan process har uppdaterat entiteten i Table Storage, returnerar tjänsten ett HTTP 412-status meddelande (Precondition Failed).  Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Om du vill inaktivera samtidigheten explicit måste du ange egenskapen **etag** för objektet **anställda** till "*" innan du kör ersättnings åtgärden.

```csharp
customer.ETag = "*";
```

I följande tabell sammanfattas hur tabell enhets åtgärder använder ETag-värden:

| Åtgärd | Returnerar ETag-värde | Kräver huvud för If-Match begär Ande |
|:--- |:--- |:--- |
| Fråga entiteter |Ja |Nej |
| Infoga entitet |Ja |Nej |
| Uppdatera entitet |Ja |Ja |
| Sammanfoga entitet |Ja |Ja |
| Ta bort entitet |Nej |Ja |
| Infoga eller Ersätt entitet |Ja |Nej |
| Infoga eller sammanfoga entitet |Ja |Nej |

Observera att åtgärderna **Infoga eller Ersätt** entiteter och **Infoga eller sammanfoga entiteter** *inte gör några* samtidighets kontroller eftersom de inte skickar ett ETag-värde till tabell tjänsten.

I allmänhet bör utvecklare som använder tabeller förlitar sig på optimistisk samtidighet. Om du behöver pessimistisk låsning vid åtkomst till tabeller, tilldelar du en vald BLOB för varje tabell och försöker att ta ett lån på blobben innan du börjar använda tabellen. Den här metoden kräver att programmet ser till att alla data åtkomst Sök vägar får lånet innan det körs på tabellen. Observera också att den minsta låne tiden är 15 sekunder, vilket kräver noggrann hänsyn till skalbarhet.

Mer information finns i:

* [Åtgärder på entiteter](/rest/api/storageservices/Operations-on-Entities)

## <a name="managing-concurrency-in-the-queue-service"></a>Hantera samtidighet i Queue Service

Ett scenario där samtidighet är ett problem i kötjänsten är att flera klienter hämtar meddelanden från en kö. När ett meddelande hämtas från kön innehåller svaret meddelandet och ett värde för pop-kvitto, vilket krävs för att ta bort meddelandet. Meddelandet tas inte bort automatiskt från kön, men när det har hämtats visas det inte för andra klienter för det tidsintervall som anges av timeout-parametern för synlighet. Klienten som hämtar meddelandet förväntas ta bort meddelandet efter att det har bearbetats och före den tid som anges av TimeNextVisible-elementet i svaret, som beräknas utifrån värdet för timeout-parametern för synlighet. Värdet för Synlighets-timeout läggs till i den tid då meddelandet hämtas för att fastställa värdet för TimeNextVisible.

Queue Service har inte stöd för antingen optimistisk eller pessimistisk samtidighet och för den här orsaken bör klienter som bearbetar meddelanden som hämtats från en kö se till att meddelanden bearbetas på ett idempotenta sätt. En sista skrivares WINS-strategi används för uppdaterings åtgärder som SetQueueServiceProperties, SetQueueMetaData, SetQueueACL och UpdateMessage.

Mer information finns i:

* [Kö-tjänstens REST-API](/rest/api/storageservices/Queue-Service-REST-API)
* [Hämta meddelanden](/rest/api/storageservices/Get-Messages)

## <a name="managing-concurrency-in-azure-files"></a>Hantera samtidighet i Azure Files

Fil tjänsten kan nås med två olika protokoll slut punkter – SMB och REST. REST-tjänsten har inte stöd för antingen optimistisk låsning eller pessimistisk låsning, och alla uppdateringar kommer att följa en senaste skrivares WINS-strategi. SMB-klienter som monterar fil resurser kan använda fil systemets lås mekanismer för att hantera åtkomst till delade filer, inklusive möjligheten att utföra pessimistisk låsning. När en SMB-klient öppnar en fil, anges både fil åtkomst och resurs läge. Om du anger ett fil åtkomst alternativ för "Write" eller "Läs/skriv" tillsammans med ett fil resurs läge "ingen" resulterar det i att filen låses av en SMB-klient tills filen stängs. Om REST-åtgärden försöker utföras på en fil där en SMB-klient har filen låst, returnerar REST-tjänsten status koden 409 (konflikt) med felkoden SharingViolation.

När en SMB-klient öppnar en fil för borttagning, markeras filen som väntande borttagning tills alla andra SMB-klientens öppna referenser till den aktuella filen är stängda. När en fil markeras som väntande borttagning, kommer alla REST-åtgärder i filen att returnera status kod 409 (konflikt) med felkoden SMBDeletePending. Status kod 404 (hittades inte) returnerades inte eftersom det är möjligt för SMB-klienten att ta bort flaggan för väntande borttagning innan filen stängs. Med andra ord förväntas status kod 404 (hittades inte) när filen har tagits bort. Observera att när en fil är i ett SMB-tillstånd som väntar på borttagning, tas den inte med i listan över filer. Tänk också på att REST DELETE-filen och REST Delete-åtgärderna allokeras och inte resulterar i ett tillstånd som väntar på borttagning.

Mer information finns i:

* [Hantera fillås](/rest/api/storageservices/Managing-File-Locks)

## <a name="next-steps"></a>Nästa steg

För det fullständiga exempel programmet som refereras i den här bloggen:

* [Hantera samtidighet med hjälp av Azure Storage-exempel program](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Mer information om Azure Storage finns i:

* [Microsoft Azure Storage start sida](https://azure.microsoft.com/services/storage/)
* [Introduktion till Azure Storage](storage-introduction.md)
* Lagrings Komma igång för [BLOB](../blobs/storage-quickstart-blobs-dotnet.md), [tabell](../../cosmos-db/tutorial-develop-table-dotnet.md),  [köer](../queues/storage-dotnet-how-to-use-queues.md)och [filer](../files/storage-dotnet-how-to-use-files.md)
* Lagrings arkitektur – [Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)