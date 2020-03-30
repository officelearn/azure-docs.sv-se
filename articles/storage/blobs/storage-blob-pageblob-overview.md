---
title: Översikt över Azure-sidblobar | Microsoft-dokument
description: En översikt över Azure-sidblobar och deras fördelar, inklusive användningsfall med exempelskript.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985622"
---
# <a name="overview-of-azure-page-blobs"></a>Översikt över Azure-sidblobar

Azure Storage erbjuder tre typer av blob-lagring: Block Blobbar, Lägg till Blobbar och sidblobar. Blockblobar består av block och är idealiska för lagring av textfiler eller binära filer och för att ladda upp stora filer effektivt. Lägg till blobbar består också av block, men de är optimerade för tilläggsåtgärder, vilket gör dem idealiska för loggningsscenarier. Sidblobar består av 512 byte sidor upp till 8 TB i total storlek och är utformade för frekventa slumpmässiga läs-/skrivåtgärder. Sidblobar är grunden för Azure IaaS-diskar. Den här artikeln fokuserar på att förklara funktionerna och fördelarna med sidblobar.

Sidblobar är en samling med 512 byte-sidor, som ger möjlighet att läsa/skriva godtyckliga intervall för byte. Därför är sidblobar idealiska för lagring av indexbaserade och glesa datastrukturer som operativsystem och datadiskar för virtuella datorer och databaser. Azure SQL DB använder till exempel sidblobar som underliggande beständig lagring för sina databaser. Dessutom används sidblobar ofta för filer med intervallbaserade uppdateringar.  

Viktiga funktioner i Azure-sidblobar är dess REST-gränssnitt, varaktigheten för det underliggande lagringsutrymmet och de sömlösa migreringsfunktionerna till Azure. Dessa funktioner diskuteras mer i detalj i nästa avsnitt. Dessutom stöds Azure-sidblobar för närvarande på två typer av lagring: Premium Storage och Standard Storage. Premium Storage är utformat speciellt för arbetsbelastningar som kräver konsekvent hög prestanda och låg latens vilket gör premium-sidblobar idealiska för lagringsscenarier med hög prestanda. Standardlagringskonton är mer kostnadseffektiva för att köra svarskänsliga arbetsbelastningar.

## <a name="sample-use-cases"></a>Exempel på användningsfall

Låt oss diskutera ett par användningsfall för sidblobar som börjar med Azure IaaS-diskar. Azure-sidblobar är ryggraden i den virtuella diskplattformen för Azure IaaS. Både Azure OS och datadiskar implementeras som virtuella diskar där data sparas på ett durably i Azure Storage-plattformen och sedan levereras till de virtuella datorerna för maximal prestanda. Azure-diskar sparas i Hyper-V [VHD-format](https://technet.microsoft.com/library/dd979539.aspx) och lagras som en [sidblob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) i Azure Storage. Förutom att använda virtuella diskar för virtuella Azure IaaS-datorer aktiverar sidblobar även PaaS- och DBaaS-scenarier som Azure SQL DB-tjänst, som för närvarande använder sidblobar för lagring av SQL-data, vilket möjliggör snabba slumpmässiga läs- och skrivåtgärder för databasen. Ett annat exempel är om du har en PaaS-tjänst för delad medieåtkomst för samarbetsprogram för videoredigeringsprogram, sidblobar möjliggör snabb åtkomst till slumpmässiga platser i mediet. Det möjliggör också snabb och effektiv redigering och sammanslagning av samma media av flera användare. 

Microsoft-tjänster från första part som Azure Site Recovery, Azure Backup och många tredjepartsutvecklare har implementerat branschledande innovationer med hjälp av sidblobbens REST-gränssnitt. Nedan följer några av de unika scenarier som implementeras på Azure: 

* Programstyrd inkrementell ögonblicksbildhantering: Program kan utnyttja ögonblicksbilder av sidblobb och REST-API:er för att spara programkontrollpunkterna utan att medföra kostsam dubblering av data. Azure Storage stöder lokala ögonblicksbilder för sidblobar, som inte kräver att du kopierar hela blobben. Dessa offentliga ögonblicksbild API: er gör det också möjligt att komma åt och kopiera delta mellan ögonblicksbilder.
* Live migrering av program och data från lokalt till moln: Kopiera lokala data och använda REST API:er för att skriva direkt till en Azure-sidblob medan den lokala virtuella datorn fortsätter att köras. När målet har hunnit ikapp kan du snabbt växla över till Azure VM med hjälp av dessa data. På så sätt kan du migrera dina virtuella datorer och virtuella diskar från lokalt till moln med minimal stilleståndstid eftersom datamigreringen sker i bakgrunden medan du fortsätter att använda den virtuella datorn och driftstoppet som krävs för redundans blir kort (på några minuter).
* [SAS-baserad](../common/storage-sas-overview.md) delad åtkomst, som möjliggör scenarier som flera läsare och enskrivare med stöd för samtidighetskontroll.

## <a name="page-blob-features"></a>Funktioner för sidblob

### <a name="rest-api"></a>REST API

Se följande dokument för att komma igång med [att utveckla med hjälp av sidblobar](storage-dotnet-how-to-use-blobs.md). Du kan till exempel titta på hur du kommer åt sidblobar med lagringsklientbiblioteket för .NET. 

I följande diagram beskrivs de övergripande relationerna mellan konto-, behållare och sidblobar.

![Skärmbild som visar relationer mellan konto-, behållare och sidblobar](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Skapa en tom sidblob av angiven storlek

Om du vill skapa en sidblob skapar vi först ett **CloudBlobClient-objekt,** med bas-URI för åtkomst till blob-lagringen för ditt lagringskonto (*pbaccount* i figur 1) tillsammans med **StorageCredentialsAccountAndKey-objektet,** som visas i följande exempel. Exemplet visar sedan att skapa en referens till ett **CloudBlobContainer-objekt** och sedan skapa behållaren *(testvhds*) om den inte redan finns. Skapa en referens till ett **CloudPageBlob-objekt** genom att sedan använda **CloudBlobContainer-objektet** genom att ange sidblobbnamnet (os4.vhd) som ska komma åt. Om du vill skapa sidblobben anropar du [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), som skickar maxstorleken för bloben att skapa. *BlobSize* måste vara en multipel av 512 byte.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Ändra storlek på en sidblob

Om du vill ändra storlek på en sidblob när du har skapat den använder du metoden [Ändra storlek.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) Den begärda storleken bör vara en multipel av 512 byte.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Skriva sidor till en sidblob

Om du vill skriva sidor använder du metoden [CloudPageBlob.WritePages.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)  På så sätt kan du skriva en sekventiell uppsättning sidor upp till 4 MB. Förskjutningen som skrivs till måste börja på en 512-bytegräns (startingOffset % 512 == 0) och sluta på en 512-gräns - 1.  I följande kodexempel visas hur du **anropar WritePages** för en blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Så snart en skrivbegäran för en sekventiell uppsättning sidor lyckas i blob-tjänsten och replikeras för hållbarhet och återhämtning, har skrivningen begåtts och framgång returneras tillbaka till klienten.  

Nedanstående diagram visar 2 separata skrivoperationer:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  En skrivåtgärd som börjar vid förskjutning 0 längd 1024 byte 
2.  En skrivåtgärd som börjar vid offset 4096 av längd 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Läsa sidor från en sidblob

Om du vill läsa sidor använder du metoden [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) för att läsa ett antal byte från sidblobben. På så sätt kan du hämta hela blobben eller bytesintervallet från en förskjutning i blobben. Vid läsning behöver förskjutningen inte starta på en multipel av 512. När du läser byte från en NUL-sida returnerar tjänsten noll byte.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Följande bild visar en Läs-åtgärd med en förskjutning på 256 och en intervallstorlek på 4352. Data som returneras markeras i orange. Nollor returneras för NUL-sidor.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Om du har en glest befolkad blob kanske du bara vill hämta de giltiga sidregionerna för att undvika att betala för utgående av noll byte och för att minska hämtningsfördröjningen.  Om du vill ta reda på vilka sidor som backas upp av data använder du [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Du kan sedan räkna upp de returnerade intervallen och hämta data i varje intervall. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Leasing av en sidblob

Åtgärden Lease Blob upprättar och hanterar ett lås på en blob för skriv- och borttagningsåtgärder. Den här åtgärden är användbar i scenarier där en sidblob används från flera klienter för att säkerställa att endast en klient kan skriva till blobben åt gången. Azure Diskar, till exempel, utnyttjar den här leasingmekanismen för att säkerställa att disken endast hanteras av en enda virtuell dator. Låstiden kan vara 15 till 60 sekunder, eller kan vara oändlig. Se dokumentationen [här](/rest/api/storageservices/lease-blob) för mer information.

Förutom omfattande REST-API:er ger sidblobar även delad åtkomst, hållbarhet och förbättrad säkerhet. Vi kommer att täcka dessa fördelar mer i detalj i nästa punkter. 

### <a name="concurrent-access"></a>Samtidig åtkomst

Sidan blobbar REST API och dess leasingmekanism tillåter program att komma åt sidan blob från flera klienter. Anta till exempel att du måste skapa en distribuerad molntjänst som delar lagringsobjekt med flera användare. Det kan vara ett webbprogram som betjänar en stor samling bilder till flera användare. Ett alternativ för att implementera detta är att använda en virtuell dator med anslutna diskar. Nackdelarna med detta inkluderar,(i) begränsningen att en disk endast kan kopplas till en enda virtuell dator vilket begränsar skalbarhet, flexibilitet och ökande risker. Om det finns ett problem med den virtuella datorn eller tjänsten som körs på den virtuella datorn, sedan på grund av lånet, är avbildningen otillgänglig tills lånet löper ut eller bryts. och (ii) Extra kostnad för att ha en IaaS VM. 

Ett alternativt alternativ är att använda sidblobar direkt via Azure Storage REST API:er. Det här alternativet eliminerar behovet av dyra virtuella IaaS-datorer, erbjuder full flexibilitet för direkt åtkomst från flera klienter, förenklar den klassiska distributionsmodellen genom att eliminera behovet av att koppla från/koppla bort diskar och eliminerar risken för problem på den virtuella datorn. Och det ger samma prestandanivå för slumpmässiga läs-/skrivåtgärder som en disk

### <a name="durability-and-high-availability"></a>Hållbarhet och hög tillgänglighet

Både standard- och premiumlagring är hållbar lagring där sidblobbdata alltid replikeras för att säkerställa hållbarhet och hög tillgänglighet. Mer information om Azure Storage Redundans finns i den här [dokumentationen](../common/storage-redundancy.md). Azure har konsekvent levererat hållbarhet i företagsklass för IaaS-diskar och sidblobar, med en branschledande nollprocent [årlig felfrekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate).

### <a name="seamless-migration-to-azure"></a>Sömlös migrering till Azure

För kunder och utvecklare som är intresserade av att implementera sin egen anpassade säkerhetskopieringslösning erbjuder Azure också inkrementella ögonblicksbilder som bara innehåller deltan. Den här funktionen undviker kostnaden för den ursprungliga fullständiga kopian, vilket avsevärt sänker säkerhetskopieringskostnaden. Tillsammans med möjligheten att effektivt läsa och kopiera differentiella data är detta en annan kraftfull funktion som möjliggör ännu fler innovationer från utvecklare, vilket leder till en förstklassig säkerhetskopierings- och katastrofåterställning (DR) på Azure. Du kan konfigurera din egen säkerhetskopierings- eller DR-lösning för dina virtuella datorer på Azure med hjälp av [Blob Snapshot](/rest/api/storageservices/snapshot-blob) tillsammans med [API:et Hämta sidintervall](/rest/api/storageservices/get-page-ranges) och [API:et för inkrementell kopia,](/rest/api/storageservices/incremental-copy-blob) som du enkelt kan använda för att enkelt kopiera inkrementella data för DR. 

Dessutom har många företag kritiska arbetsbelastningar som redan körs i lokala datacenter. För att migrera arbetsbelastningen till molnet, en av de viktigaste frågorna skulle vara den mängd driftstopp som behövs för att kopiera data, och risken för oförutsedda problem efter övergången. I många fall kan driftstopp vara en showstopper för migrering till molnet. Med hjälp av rest-API:et för sidblobbar åtgärdar Azure det här problemet genom att aktivera molnmigrering med minimala störningar för kritiska arbetsbelastningar. 

Exempel på hur du tar en ögonblicksbild och hur du återställer en sidblob från en ögonblicksbild finns i [installationen av en säkerhetskopieringsprocess med hjälp av den inkrementella ögonblicksbilder.](../../virtual-machines/windows/incremental-snapshots.md)
