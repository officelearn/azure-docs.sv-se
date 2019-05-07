---
title: Översikt över Azure-sidblobar | Microsoft Docs
description: En översikt över Azure page blobs och deras fördelar, däribland användningsfall med exempel på skript.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: c0d5108ee235be74f6188bfc8b590069a4e790b5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192646"
---
# <a name="overview-of-azure-page-blobs"></a>Översikt över Azure-sidblobar

Azure Storage erbjuder tre typer av blob-lagring: Blockblobbar, Tilläggsblobbar och sidblobbar. Blockblobar består av block och är perfekt för att lagra text eller binära filer och för att överföra stora filer effektivt. Lägg till BLOB-objekt är också består av block, men de är optimerade för tilläggsåtgärder, vilket gör dem perfekt för loggningsscenarier. Sidblobar är består av 512 byte-sidor på till 8 TB i total storlek och är utformad för frekventa slumpmässig läsning/skrivning åtgärder. Sidblobar är grunden i Azure IaaS-diskar. Den här artikeln handlar om förklarar funktioner och fördelar med sidblobar.

Sidblobar är en samling 512 byte-sidor, vilket ger möjlighet att läsa/skriva godtyckliga områden i byte. Därför är sidblobar utmärkt för att lagra index-baserade och utspridda datastrukturer som Operativsystemet och datadiskarna för virtuella datorer och databaser. Till exempel använder Azure SQL DB sidblobar som underliggande beständig lagring för dess databaser. Dessutom används sidblobar ofta för filer med Intervallbaserat uppdateringar.  

Viktiga funktioner i Azure-sidblobar är dess REST-gränssnittet, tillförlitlighet gällande det underliggande lagringsutrymmet och funktioner för sömlös migrering till Azure. Dessa funktioner beskrivs närmare i nästa avsnitt. Dessutom stöds för närvarande Azure sidblobar på två typer av lagring: Premium-lagring och Standard-lagring. Premium Storage har utformats speciellt för arbetsbelastningar som kräver konsekvent hög prestanda och låg latens, vilket gör premium-sidblobar perfekt för högpresterande data databaserna.  Standard-lagring är mer kostnadseffektivt sätt latens-okänslig arbetsbelastningar som körs.

## <a name="sample-use-cases"></a>Exemplet användningsfall

Vi ska diskutera några användningsområden för sidblobar som börjar med Azure IaaS-diskar. Azure-sidblobar är basen för den virtuella diskar plattformen för Azure IaaS. Både Azure OS och datadiskar implementeras som virtuella diskar där data sparas varaktigt i Azure Storage-plattformen och sedan levereras till de virtuella datorerna för maximal prestanda. Azure-diskar finns kvar i Hyper-V [VHD-format](https://technet.microsoft.com/library/dd979539.aspx) och lagras som en [sidblob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) i Azure Storage. Förutom att använda virtuella diskar för virtuella Azure IaaS-datorer, aktivera sidblobar också PaaS och DBaaS scenarier, till exempel Azure SQL DB-tjänsten, som för närvarande använder sidblobar för att lagra SQL-data vilket möjliggör snabb slumpmässig Läs-och skrivåtgärder för databasen. Ett annat exempel är om du har en PaaS-tjänst för delade media åtkomst för samarbetsfunktioner video redigeringsprogram sidblobar ge snabb åtkomst till sekventiella skrivåtgärder i mediet. Dessutom kan snabba och effektiva redigera och slå samman samma mediets av flera användare. 

Första parts Microsoft-tjänster som Azure Site Recovery, Azure Backup, samt många tredjepartsutvecklare har implementerat branschledande innovationer med hjälp av sidans blob REST-gränssnittet. Här följer några av de unika scenarier implementerat i Azure: 
* Hantering av program-riktade inkrementell ögonblicksbild: Program kan använda sidan blob-ögonblicksbilder och REST API: er för att spara programmet kontrollpunkter utan att det medför kostsamma duplicering av data. Azure Storage stöder lokala ögonblicksbilder för sidblobbar, som inte kräver kopiera hela blobben. De här offentliga ögonblicksbild API: er också kan få åtkomst till och kopiera deltan mellan ögonblicksbilder.
* Direktmigrering av program och data från lokalt till molnet: Kopiera data på plats och Använd REST API: er för att skriva direkt till en sida för Azure-blob när den lokala virtuella datorn fortsätter att köras. När målet har fått allt, men du kan snabbt växla över till virtuell Azure-dator med hjälp av dessa data. På så sätt kan du migrera dina virtuella datorer och virtuella diskar från lokalt till molnet med minimal avbrottstid eftersom migrering av data sker i bakgrunden medan du fortsätter att använda den virtuella datorn och den stilleståndstiden som behövs för redundans kommer att korta (i minuter).
* [SAS-baserad](../common/storage-dotnet-shared-access-signature-part-1.md) delad åtkomst, vilket möjliggör scenarier som flera läsare och single-skrivare med stöd för samtidighetskontroll.

## <a name="page-blob-features"></a>Funktioner för sidblob

### <a name="rest-api"></a>REST-API
Se följande dokument från att komma igång med [utveckla med hjälp av sidblobar](storage-dotnet-how-to-use-blobs.md). Exempelvis titta på hur du kommer åt sidan blobar med Storage-klientbiblioteket för .NET. 

Följande diagram visar övergripande relationerna mellan konto, behållare och sidblobar.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Skapa en tom sida-blob med en angiven storlek
Om du vill skapa en sidblobb vi först skapa en **CloudBlobClient** objekt med bas-URI för blob-lagring för ditt lagringskonto (*pbaccount* i bild 1) tillsammans med den  **StorageCredentialsAccountAndKey** objekt som visas i följande exempel. Exemplet visar sedan hur du skapar en referens till en **CloudBlobContainer** objekt och sedan skapa behållaren (*testvhds*) om den inte redan finns. Sedan använder den **CloudBlobContainer** objekt, skapar en referens till en **CloudPageBlob** objekt genom att ange sidans blob-namn (os4.vhd) åtkomst till. Om du vill skapa sidans blob anropa [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) skicka den maximala storleken för blobben som ska skapas. Den *blobSize* måste vara en multipel av 512 byte.

```csharp
using Microsoft.WindowsAzure.StorageClient;
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

#### <a name="resizing-a-page-blob"></a>Ändra storlek på en sidblobb
Ändra storlek på en sidblobb när du har skapat genom att använda den [ändra storlek på](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) API. Den begärda storleken ska vara en multipel av 512 byte.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Skriva sidor till en sidblob
Om du vill skriva sidor, använda den [CloudPageBlob.WritePages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_BeginWritePages_System_IO_Stream_System_Int64_System_String_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_System_AsyncCallback_System_Object_) metod.  På så sätt kan du skriva en sekventiell uppsättning sidor upp till 4MBs. Förskjutningen skrivs till måste börja på en 512 byte-gräns (startingOffset % 512 == 0), och på en 512 gräns - 1.  I följande kodexempel visas hur du anropar **WritePages** för en blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

När en skrivbegäran för en sekventiell uppsättning sidor lyckas blob service och replikeras för hållbarhet och återhämtning, skrivningen har genomförts och lyckades returneras tillbaka till klienten.  

Den nedan visas diagram 2 separat skrivåtgärder:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Skrivning med början vid förskjutningen 0 med längden 1 024 byte 
2.  Skrivning med början vid förskjutningen 4096 med längden 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Läsa sidor från en sidblob
Om du vill läsa sidor, använda den [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) metod för att läsa byte från sidans blob. På så sätt kan du hämta fullständig blob eller byte från valfri förskjutning i blob. När du läser, behöver inte förskjutningen starta på en multipel av 512. När du läser byte från en sida för NUL returnerar tjänsten noll byte.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
Följande bild visar en Läs-åtgärd med BlobOffSet av 256 och rangeSize av 4352. Data som returneras är markerat på Orange. Nollor returneras för NUL sidor

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Om du har en sparsamt ifyllda blob kan du bara hämta giltig regioner kan undvika att betala för egressing noll byte och minska svarstider för hämtning.  Använd för att fastställa vilka sidor backas upp av data, [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Du kan sedan räkna upp de returnerade intervall och hämta data i varje intervall. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Leasing en sidblob
Åtgärden Lease Blob upprättar och hanterar ett lås för en blob för skrivning och ta bort. Den här åtgärden är användbar i scenarier där en sidblobb nås från flera klienter för att se till att endast en klient kan skriva till bloben i taget. Azure-diskar, till exempel använder det här leasing mekanism för att se till att disken är endast hanteras av en enskild virtuell dator. Varaktighet för lås kan vara 15 till 60 sekunder eller kan vara oändlig. Finns i dokumentationen [här](/rest/api/storageservices/lease-blob) för mer information.

Utöver omfattande REST API: er erbjuder även Page blobs delad åtkomst, tillförlitlighet och förbättrad säkerhet. Vi täcker dessa förmåner i detalj i nästa stycken. 

### <a name="concurrent-access"></a>Samtidig åtkomst
Sidblobar REST API och dess leasingmekanism ger åtkomst till sidans blob från flera klienter. Anta exempelvis att du behöver för att skapa en distribuerad molntjänst som delar storage-objekt med flera användare. Det kan vara ett webbprogram som ett stort antal avbildningar till flera användare. Ett alternativ för att implementera detta är att använda en virtuell dator med anslutna diskar. Nackdelarna med för den här inkludera (i) den begränsning som en disk kan endast kopplas till en enda virtuell dator så att begränsa skalbarhet, flexibilitet och ökar riskerna. Om det finns ett problem med den virtuella datorn eller tjänsten som körs på den virtuella datorn, sedan blir på grund av lånet, avbildningen inte tillgänglig förrän lånet upphör att gälla eller är bruten; och (ii) ytterligare kostnaden för att ha en IaaS VM. 

Ett alternativ är att använda sidblobar direkt via Azure Storage REST API: er. Det här alternativet eliminerar behovet av dyra virtuella IaaS-datorer, erbjuder fullständig flexibilitet för direkt åtkomst från flera klienter, förenklar den klassiska distributionsmodellen genom att eliminera behovet av att koppla/frånkoppla diskar och eliminerar risken för problem på den virtuella datorn. Och det ger samma nivå av prestanda för slumpmässig läsning/skrivning åtgärder som en disk

### <a name="durability-and-high-availability"></a>Hållbarhet och hög tillgänglighet
Standard- och premium storage är beständig lagring där sidan blob-data replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Mer information om Azure lagringsredundans finns i den här [dokumentation](../common/storage-redundancy.md). Azure har konsekvent levereras tillförlitlighet i företagsklass för IaaS-diskar och sidblobar, med branschledande noll % [årlig Felfrekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate). Det vill säga förlorat aldrig Azure blob-data för en kund-sidan. 

### <a name="seamless-migration-to-azure"></a>Sömlös migrering till azure
Azure erbjuder också inkrementella ögonblicksbilder som bara innehåller deltan för kunder och utvecklare som vill implementera sina egna anpassade lösning för säkerhetskopiering. Den här funktionen förhindrar kostnaden för den första fullständiga kopian, vilket avsevärt minskar kostnaden för säkerhetskopiering. Tillsammans med möjligheten att effektivt läsa och kopiera differentiell data kan är det här en annan kraftfull funktion som gör det möjligt för ännu mer innovationer från utvecklare som leder till en klassens bästa säkerhetskopiering och haveriberedskap (DR) återställningsfunktioner på Azure. Du kan ställa in egna säkerhetskopior eller DR-lösning för dina virtuella datorer på Azure med [Blobögonblicksbilden](/rest/api/storageservices/snapshot-blob) tillsammans med den [Get Page Ranges](/rest/api/storageservices/get-page-ranges) API och [inkrementell kopiering av Blob](/rest/api/storageservices/incremental-copy-blob) API, som du kan Använd för att enkelt kopiera inkrementella data för Haveriberedskap. 

Många företag har dessutom kritiska arbetsbelastningar som körs i lokala datacenter. För att migrera arbetsbelastningen till molnet är en av de viktigaste aspekterna stilleståndstiden som behövs för att kopiera data och risken för oförutsedda problem efter övergången. I många fall vara avbrottstiden en SHOWENS stjärna för migrering till molnet. På sidan blobbar REST API, Azure åtgärdar problemet genom att aktivera migreringen till molnet med minimala störningar för kritiska arbetsbelastningar. 

Exempel på hur du tar en ögonblicksbild och hur du återställer en sidblobb från en ögonblicksbild finns i den [installationsprocessen en säkerhetskopiering med inkrementella ögonblicksbilder](../../virtual-machines/windows/incremental-snapshots.md) artikeln.
