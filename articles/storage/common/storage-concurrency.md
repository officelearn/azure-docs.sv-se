---
title: Hantera samtidighet
titleSuffix: Azure Storage
description: Lär dig hur du hanterar samtidighet för Blob-, Kö-, Table- och File-tjänsterna.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255306"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Hantera samtidighet i Microsoft Azure Storage

Moderna Internetbaserade program har vanligtvis flera användare som visar och uppdaterar data samtidigt. Detta kräver att programutvecklare noga överväger hur de kan ge sina slutanvändare en förutsägbar upplevelse, särskilt för scenarier där flera användare kan uppdatera samma data. Det finns tre huvudsakliga strategier för samtidighet av data som utvecklare vanligtvis anser:  

1. Optimistisk samtidighet – Ett program som utför en uppdatering verifierar som en del av uppdateringen om data har ändrats sedan programmet lästes senast dessa data. Om till exempel två användare som visar en wiki-sida gör en uppdatering till samma sida måste wiki-plattformen se till att den andra uppdateringen inte skriver över den första uppdateringen – och att båda användarna förstår om uppdateringen lyckades eller inte. Den här strategin används oftast i webbapplikationer.
2. Pessimistisk samtidighet – Ett program som vill utföra en uppdatering tar ett lås på ett objekt som hindrar andra användare från att uppdatera data tills låset släpps. I ett huvud-/underordnade datareplikeringsscenario där endast huvuduppsättningen utför uppdateringar innehåller befälhavaren vanligtvis ett exklusivt lås under en längre tid på data för att säkerställa att ingen annan kan uppdatera den.
3. Senaste författare vinner – En metod som gör att alla uppdateringsåtgärder kan fortsätta utan att verifiera om något annat program har uppdaterat data sedan programmet först läste data. Denna strategi (eller brist på en formell strategi) används vanligtvis där data partitioneras på ett sådant sätt att det inte finns någon sannolikhet att flera användare kommer åt samma data. Det kan också vara användbart när kortlivade dataströmmar bearbetas.  

Den här artikeln innehåller en översikt över hur Azure Storage-plattformen förenklar utvecklingen genom att tillhandahålla förstklassigt stöd för alla tre av dessa samtidighetsstrategier.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage förenklar molnutvecklingen

Azure-lagringstjänsten stöder alla tre strategierna, även om den är utmärkande för sin förmåga att ge fullt stöd för optimistisk och pessimistisk samtidighet eftersom den har utformats för att omfatta en stark konsekvensmodell som garanterar att när Lagringstjänsten genomför en datainfogning eller uppdateringsåtgärd alla ytterligare åtkomster till dessa data kommer att se den senaste uppdateringen. Lagringsplattformar som använder en eventuell konsekvensmodell har en fördröjning mellan när en skrivning utförs av en användare och när de uppdaterade data kan ses av andra användare vilket komplicerar utvecklingen av klientprogram för att förhindra inkonsekvenser från som påverkar slutanvändarna.  

Förutom att välja en lämplig samtidighet strategi utvecklare bör också vara medveten om hur en lagringsplattform isolerar förändringar - särskilt ändringar i samma objekt över transaktioner. Azure-lagringstjänsten använder ögonblicksbildisolering för att tillåta att läsåtgärder kan inträffa samtidigt med skrivåtgärder inom en enda partition. Till skillnad från andra isoleringsnivåer garanterar ögonblicksbildisolering att alla läsningar ser en konsekvent ögonblicksbild av data även när uppdateringar sker – huvudsakligen genom att returnera de senast bekräftade värdena medan en uppdateringstransaktion bearbetas.  

## <a name="managing-concurrency-in-blob-storage"></a>Hantera samtidighet i Blob-lagring

Du kan välja att använda antingen optimistiska eller pessimistiska samtidighetsmodeller för att hantera åtkomst till blobbar och behållare i Blob-tjänsten. Om du inte uttryckligen anger en strategi sista skriver vinner är standard.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistisk samtidighet för blobbar och behållare

Lagringstjänsten tilldelar en identifierare till varje objekt som lagras. Den här identifieraren uppdateras varje gång en uppdateringsåtgärd utförs på ett objekt. Identifieraren returneras till klienten som en del av ett HTTP GET-svar med hjälp av ETag-huvudet (entitetstagg) som definieras i HTTP-protokollet. En användare som utför en uppdatering på ett sådant objekt kan skicka in den ursprungliga ETag tillsammans med ett villkorligt huvud för att säkerställa att en uppdatering endast sker om ett visst villkor har uppfyllts – i det här fallet är villkoret ett "If-Match"-huvud, vilket kräver lagringstjänsten för att säkerställa att värdet på den ETag som anges i uppdateringsbegäran är detsamma som det som lagras i lagringstjänsten.  

Konturerna av denna process är följande:  

1. Hämta en blob från lagringstjänsten, svaret innehåller ett HTTP ETag-huvudvärde som identifierar den aktuella versionen av objektet i lagringstjänsten.
2. När du uppdaterar blobben ska du inkludera ETag-värdet som du fick i steg 1 i villkorshuvudet **If-Match** för den begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för bloben.
4. Om det aktuella ETag-värdet för blobben är en annan version än ETag i villkorshuvudet **If-Match** i begäran returnerar tjänsten ett 412-fel till klienten. Detta indikerar för klienten att en annan process har uppdaterat blobben sedan klienten hämtade den.
5. Om det aktuella ETag-värdet för blobben är samma version som ETag i villkorshuvudet **If-Match** i begäran, utför tjänsten den begärda åtgärden och uppdaterar det aktuella ETag-värdet för blobben för att visa att den har skapat en ny version.  

Följande C#-kodavsnitt (med klientlagringsbiblioteket 4.2.0) visar ett enkelt exempel på hur du skapar en **If-Match AccessCondition** baserat på ETag-värdet som nås från egenskaperna för en blob som tidigare antingen hämtades eller infogades. **AccessCondition-objektet** används sedan när blobben **uppdateras: AccessCondition-objektet** lägger till **If-Match-huvudet** i begäran. Om en annan process har uppdaterat blobben returnerar Blob-tjänsten ett HTTP 412-statusmeddelande (förutsättning misslyckades). Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

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

Azure Storage innehåller också stöd för ytterligare villkorliga huvuden som **If-Modified-Since**, **If-Unmodified-Since** och **If-None-Match** samt kombinationer av dessa. Mer information finns i [Ange villkorliga rubriker för Blob Service Operations](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

I följande tabell sammanfattas behållaråtgärder som accepterar villkorliga huvuden, till exempel **If-Match** i begäran och som returnerar ett ETag-värde i svaret.  

| Åtgärd | Returnerar värde för container-ETag | Accepterar villkorliga huvuden |
|:--- |:--- |:--- |
| Skapa container |Ja |Inga |
| Hämta behållaregenskaper |Ja |Inga |
| Hämta behållarmetadata |Ja |Inga |
| Ange behållarmetadata |Ja |Ja |
| Hämta ACL för behållare |Ja |Inga |
| Ange behållare ACL |Ja |Ja (*) |
| Ta bort container |Inga |Ja |
| Lånebehållare |Ja |Ja |
| Lista Blobbar |Inga |Inga |

(*) De behörigheter som definieras av SetContainerACL cachelagras och uppdateringar av dessa behörigheter tar 30 sekunder att sprida under vilka perioduppdateringar inte garanteras vara konsekventa.  

I följande tabell sammanfattas blob-åtgärder som accepterar villkorliga huvuden, till exempel **If-Match** i begäran och som returnerar ett ETag-värde i svaret.

| Åtgärd | Returnerar ETag-värde | Accepterar villkorliga huvuden |
|:--- |:--- |:--- |
| Placera blob |Ja |Ja |
| Hämta Blob |Ja |Ja |
| Hämta blobegenskaper |Ja |Ja |
| Ange blob-egenskaper |Ja |Ja |
| Hämta Blob-metadata |Ja |Ja |
| Ange Blob-metadata |Ja |Ja |
| Lease Blob (*) |Ja |Ja |
| Ta ögonblicksbild av blob |Ja |Ja |
| Kopiera blob |Ja |Ja (för käll- och målblob) |
| Avbryt kopia blob |Inga |Inga |
| Ta bort blob |Inga |Ja |
| Sätt block |Inga |Inga |
| Placera blocklista |Ja |Ja |
| Hämta blockeringslista |Ja |Inga |
| Placera sida |Ja |Ja |
| Hämta sidintervall |Ja |Ja |

(*) Lease Blob ändrar inte ETag på en blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistisk samtidighet för blobbar

Om du vill låsa en blob för exklusiv användning kan du skaffa ett [lån](https://msdn.microsoft.com/library/azure/ee691972.aspx) på den. När du skaffar ett lån anger du hur länge du behöver lånet: det kan vara mellan 15 och 60 sekunder eller oändligt, vilket motsvarar ett exklusivt lås. Du kan förnya ett begränsat lån för att förlänga det, och du kan släppa alla lån när du är klar med det. Blob-tjänsten släpper automatiskt finita lån när de upphör att gälla.  

Leasing gör det möjligt att stödja olika synkroniseringsstrategier, inklusive exklusiv skriv/ delad läsning, exklusiv skriv/ exklusiv läsa och delad skrivning/ exklusiv läsning. Om ett lån finns upprätthåller lagringstjänsten exklusiva skrivningar (put, set and delete-åtgärder), men för att säkerställa exklusivitet för läsåtgärder krävs att utvecklaren ser till att alla klientprogram använder ett låne-ID och att endast en klient åt gången har en giltigt leasat ID. Läsåtgärder som inte innehåller ett låne-ID resulterar i delade läsningar.  

Följande C#-kodavsnitt visar ett exempel på att skaffa ett exklusivt lån i 30 sekunder på en blob, uppdatera innehållet i blobben och sedan frigöra lånet. Om det redan finns ett giltigt lån på blobben när du försöker skaffa ett nytt lån returnerar Blob-tjänsten statusresultatet för statusen "HTTP (409) Conflict". Följande kodavsnitt använder ett **AccessCondition-objekt** för att kapsla in låneinformationen när den gör en begäran om att uppdatera blobben i lagringstjänsten.  Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Om du försöker skriva en skrivåtgärd på en leasad blob utan att passera låne-ID, misslyckas begäran med ett 412-fel. Observera att om lånet löper ut innan du anropar **UploadText-metoden** men du fortfarande klarar låne-ID:n, misslyckas begäran också med ett **412-fel.** Mer information om hur du hanterar lånetider och låne-ID finns i dokumentationen [för REST-rest](https://msdn.microsoft.com/library/azure/ee691972.aspx) för lån.  

Följande blob-åtgärder kan använda lån för att hantera pessimistisk samtidighet:  

* Placera blob
* Hämta Blob
* Hämta blobegenskaper
* Ange blob-egenskaper
* Hämta Blob-metadata
* Ange Blob-metadata
* Ta bort blob
* Sätt block
* Placera blocklista
* Hämta blockeringslista
* Placera sida
* Hämta sidintervall
* Ögonblicksbild Blob - lease-ID valfritt om ett lån finns
* Kopiera Blob - låne-ID krävs om ett lån finns på målblobben
* Avbryt kopiera blob - låne-ID krävs om ett oändligt lån finns på målblobben
* Låna blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistisk samtidighet för behållare

Leasing på behållare gör det möjligt att stödja samma synkroniseringsstrategier som på blobbar (exklusiv skrivning / delad läsning, exklusiv skriv / exklusiv läsa och delad läsning / exklusiv läsning) men till skillnad från blobbar upprätthåller lagringstjänsten endast exklusivitet vid borttagningsåtgärder. Om du vill ta bort en behållare med ett aktivt lån måste en klient inkludera det aktiva låne-ID:et med borttagningsbegäran. Alla andra behållaråtgärder lyckas på en leasad behållare utan att inkludera låne-ID i vilket fall de är delade åtgärder. Om exklusivitet för uppdatering (put or set) eller läsåtgärder krävs bör utvecklare se till att alla klienter använder ett låne-ID och att endast en klient i taget har ett giltigt låne-ID.  

Följande behållaråtgärder kan använda lån för att hantera pessimistisk samtidighet:  

* Ta bort container
* Hämta behållaregenskaper
* Hämta behållarmetadata
* Ange behållarmetadata
* Hämta ACL för behållare
* Ange behållare ACL
* Lånebehållare  

Mer information finns i:  

* [Ange villkorliga rubriker för Blob Service-åtgärder](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lånebehållare](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Låna blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Hantera samtidighet i tabelllagring

Tabelltjänsten använder optimistiska samtidighetskontroller som standardbeteende när du arbetar med entiteter, till skillnad från Blob-tjänsten där du uttryckligen måste välja att utföra optimistiska samtidighetskontroller. Den andra skillnaden mellan tabellen och Blob-tjänsterna är att du bara kan hantera samtidighetsbeteendet för entitetsbeteendet för entitet, medan du med Blob-tjänsten kan hantera samtidigheten hos både behållare och blobbar.  

Om du vill använda optimistisk samtidighet och kontrollera om en annan process har ändrat en entitet sedan du hämtade den från table storage-tjänsten kan du använda ETag-värdet som du får när tabelltjänsten returnerar en entitet. Konturerna av denna process är följande:  

1. Hämta en entitet från table storage-tjänsten, svaret innehåller ett ETag-värde som identifierar den aktuella identifieraren som är associerad med den entiteten i lagringstjänsten.
2. När du uppdaterar entiteten ska du inkludera ETag-värdet som du fick i steg 1 i det obligatoriska **If-Match-huvudet** för den begäran som du skickar till tjänsten.
3. Tjänsten jämför ETag-värdet i begäran med det aktuella ETag-värdet för entiteten.
4. Om det aktuella ETag-värdet för entiteten skiljer sig från ETag i det obligatoriska **If-Match-huvudet** i begäran returnerar tjänsten ett 412-fel till klienten. Detta indikerar för klienten att en annan process har uppdaterat entiteten sedan klienten hämtade den.
5. Om det aktuella ETag-värdet för entiteten är detsamma som ETag i det obligatoriska **If-Match-huvudet** i begäran eller **om-matchningshuvudet** innehåller jokertecknet (*), utför tjänsten den begärda åtgärden och uppdaterar det aktuella ETag-värdet för entiteten för att visa att det har uppdaterats.  

Observera att till skillnad från Blob-tjänsten kräver tabelltjänsten att klienten inkluderar ett **If-Match-huvud** i uppdateringsbegäranden. Det är dock möjligt att tvinga fram en ovillkorlig uppdatering (senaste writer wins-strategi) och kringgå samtidighetskontroller om klienten ställer in **If-Match-huvudet** till jokertecknet (*) i begäran.  

Följande C#-kodavsnitt visar en kundentitet som tidigare antingen har skapats eller hämtats med sin e-postadress uppdaterad. Den första infognings- eller hämtningsåtgärden lagrar ETag-värdet i kundobjektet, och eftersom exemplet använder samma objektinstans när den kör ersättningsåtgärden skickas ETag-värdet automatiskt tillbaka till tabelltjänsten, vilket gör att tjänsten kan kontrollera om det finns samtidiga överträdelser. Om en annan process har uppdaterat entiteten i tabelllagring returnerar tjänsten ett HTTP 412-statusmeddelande (förutsättning misslyckades).  Du kan hämta hela exemplet här: [Hantera samtidighet med Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Om du uttryckligen vill inaktivera samtidighetskontrollen bör du ange egenskapen **ETag** för **medarbetarobjektet** till "*" innan du kör ersättningsåtgärden.  

```csharp
customer.ETag = "*";  
```

I följande tabell sammanfattas hur tabellentitetsoperationer använder ETag-värden:

| Åtgärd | Returnerar ETag-värde | Kräver sidhuvud för begäran om if-match |
|:--- |:--- |:--- |
| Frågeentiteter |Ja |Inga |
| Infoga entitet |Ja |Inga |
| Uppdatera entitet |Ja |Ja |
| Koppla entitet |Ja |Ja |
| Ta bort entitet |Inga |Ja |
| Infoga eller ersätta entitet |Ja |Inga |
| Infoga eller sammanfoga entitet |Ja |Inga |

Observera att transaktionerna **Infoga eller Ersätt entitet** och **Infoga eller Koppla entitet** *inte* utför några samtidiga kontroller eftersom de inte skickar ett ETag-värde till tabelltjänsten.  

I allmänhet utvecklare som använder tabeller bör förlita sig på optimistisk samtidighet när du utvecklar skalbara program. Om pessimistisk låsning behövs kan en metod utvecklare ta när du öppnar tabeller är att tilldela en angiven blob för varje tabell och försöka ta ett lån på blobben innan du arbetar på bordet. Den här metoden kräver att programmet säkerställer att alla dataåtkomstvägar hämtar lånet innan de fungerar i tabellen. Du bör också notera att den minsta lånetiden är 15 sekunder vilket kräver noggrant övervägande för skalbarhet.  

Mer information finns i:  

* [Transaktioner på entiteter](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Hantera samtidighet i kötjänsten

Ett scenario där samtidighet är ett problem i kötjänsten är där flera klienter hämtar meddelanden från en kö. När ett meddelande hämtas från kön innehåller svaret meddelandet och ett popinleveransvärde, som krävs för att ta bort meddelandet. Meddelandet tas inte bort automatiskt från kön, men när det har hämtats är det inte synligt för andra klienter för det tidsintervall som anges av parametern visibilitytimeout. Klienten som hämtar meddelandet förväntas ta bort meddelandet när det har bearbetats och före den tid som anges av timeNextVisible-elementet i svaret, som beräknas baserat på värdet för parametern för visibilitytimeout. Värdet för visibilitytimeout läggs till den tidpunkt då meddelandet hämtas för att bestämma värdet för TimeNextVisible.  

Kötjänsten har inte stöd för antingen optimistisk eller pessimistisk samtidighet och därför ska klienter som bearbetar meddelanden som hämtats från en kö se till att meddelanden bearbetas på ett idempotentigt sätt. En senaste strategi för brännare vinner används för uppdateringsåtgärder som SetQueueServiceProperties, SetQueueMetaData, SetQueueACL och UpdateMessage.  

Mer information finns i:  

* [Kö-tjänstens REST-API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Hantera samtidighet i Azure-filer

Filtjänsten kan nås med hjälp av två olika protokollslutpunkter – SMB och REST. REST-tjänsten har inte stöd för antingen optimistisk låsning eller pessimistisk låsning och alla uppdateringar kommer att följa en sista författare vinner strategi. SMB-klienter som monterar filresurser kan utnyttja filsystemlåsningsmekanismer för att hantera åtkomst till delade filer – inklusive möjligheten att utföra pessimistisk låsning. När en SMB-klient öppnar en fil anger den både filåtkomst- och delningsläget. Om du ställer in alternativet Filåtkomst för "Skriv" eller "Läs/skriv" tillsammans med fildelningsläget "Ingen" kommer filen att låsas av en SMB-klient tills filen stängs. Om REST-åtgärden försökers på en fil där en SMB-klient har filen låst returnerar REST-tjänsten statuskod 409 (Konflikt) med felkod SharingViolation.  

När en SMB-klient öppnar en fil för borttagning markeras filen som väntande borttagning tills alla andra SMB-klientöppna referenser på filen stängs. Medan en fil markeras som väntande borttagning returnerar alla REST-åtgärder på filen statuskoden 409 (Konflikt) med felkoden SMBDeletePending. Statuskod 404 (Hittades inte) returneras inte eftersom SMB-klienten kan ta bort den väntande borttagningsflaggan innan filen stängs. Med andra ord förväntas statuskod 404 (Hittades inte) när filen har tagits bort. Observera att medan en fil är i ett SMB-väntande borttagningstillstånd, kommer den inte att inkluderas i listfilsresultaten. Observera också att rest delete-filen och REST Delete Directory-åtgärderna har genomförts atomärt och inte resulterar i ett väntande borttagningstillstånd.  

Mer information finns i:  

* [Hantera fillås](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Nästa steg

För hela exempel programmet refereras i den här bloggen:  

* [Hantera samtidighet med Azure Storage - Exempelprogram](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Mer information om Azure Storage finns i:  

* [Startsida för Microsoft Azure-lagring](https://azure.microsoft.com/services/storage/)
* [Introduktion till Azure Storage](storage-introduction.md)
* Komma igång för [blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [tabell,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [köer](../storage-dotnet-how-to-use-queues.md)och [filer](../storage-dotnet-how-to-use-files.md)
* Lagringsarkitektur – [Azure Storage: En molnbaserad lagringstjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

