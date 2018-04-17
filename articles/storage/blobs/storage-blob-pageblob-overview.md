---
title: Unika funktioner i Azure sidblobbar | Microsoft Docs
description: En översikt över Azure sidblobar och deras fördelar, inklusive användningsfall med exempel på skript.
services: storage
author: anasouma
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: wielriac
ms.openlocfilehash: 5d1ad1555cb1e01e363456af5c50ecd090ce7147
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="unique-features-of-azure-page-blobs"></a>Unika funktioner i Azure sidblobbar

Azure Storage erbjuder tre typer av blob-lagring: Blockblobbar, Tilläggsblobbar och sidblobbar. Blockblobbar består av block och lämpar sig för att lagra text eller binära filer och för att överföra stora filer effektivt. Lägg till BLOB också består av block, men de är optimerade för tilläggsåtgärder, vilket gör dem idealiska för loggningsscenarier. Sidblobbar består av 512 byte sidor till 8 TB total storlek och är avsedd för frekventa slumpmässig läsning/skrivning åtgärder. Sidblobbar är grunden för Azure IaaS-diskar. Den här artikeln fokuserar på funktioner och fördelar med sidblobar.

Sidblobbar är en samling 512 byte-sidorna, vilket ger möjlighet att godtyckliga intervall i byte för läsning och skrivning. Sidblobbar är därför utmärkt för att lagra indexbaserade och utspridda datastrukturer som Operativsystemet och datadiskarna för virtuella datorer och databaser. Till exempel använder Azure SQL DB sidblobbar som den underliggande beständiga lagringen för dess databaser. Dessutom används också ofta sidblobbar för filer med intervallet-baserade uppdateringar.  

Viktiga funktioner i Azure sidblobbar är dess REST-gränssnittet, hållbarhet av det underliggande lagringsutrymmet och funktioner för sömlös migrering till Azure. Dessa funktioner beskrivs närmare i nästa avsnitt. Dessutom Azure sidblobbar stöds för närvarande på två typer av lagring: Premium-lagring och standardlagring. Premium-lagring har utformats speciellt för arbetsbelastningar som kräver konsekvent hög prestanda och låg latens gör premium sidblobbar perfekt för hög performant data databaserna.  Standardlagring är mer kostnadseffektivt för latens-okänslig arbetsbelastningar som körs.

> [!WARNING]
> Sidblobbar i Premium-lagring är avsedd för användning som virtuella hårddiskar endast. Microsoft rekommenderar inte lagra andra typer av data i sidblobbar i Premium Storage eftersom kostnaden kan vara betydligt större. Använd blockblobbar för att lagra data som inte är i en virtuell Hårddisk.

## <a name="sample-use-cases"></a>Exempel användningsområden

Diskuterar vi några användningsområden för sidblobbar som börjar med Azure IaaS-diskar. Azure sidblobbar är basen för den virtuella diskar plattformen för Azure IaaS. Både Azure-OS- och datadiskar implementeras som virtuella diskar där data varaktigt kvar i Azure Storage-plattform och sedan levereras till de virtuella datorerna för högsta prestanda. Azure-diskar som finns kvar i Hyper-V [VHD-format](https://technet.microsoft.com/library/dd979539.aspx) och lagras som en [sidblob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) i Azure Storage. Förutom att använda virtuella diskar för Azure IaaS-VM aktivera sidblobbar även PaaS och DBaaS scenarier, till exempel Azure SQL DB-tjänsten, som används för närvarande sidblobbar för att lagra SQL-data att aktivera snabb slumpmässig Läs-och skrivåtgärder för databasen. Ett annat exempel är om du har en PaaS-tjänst för delade media åtkomst för samarbetsfunktioner video redigeringsprogram sidblobbar Aktivera snabb åtkomst till slumpmässiga platser i mediet. Det gör också snabba och effektiva redigering och sammanslagning av samma media av flera användare. 

Första part Microsoft-tjänster som Azure Site Recovery, Azure Backup, samt många andra utvecklare har implementerat branschledande innovationer sidan blob REST-gränssnittet. Här följer några unika scenarier implementerat i Azure: 
* Hantering av program-riktade inkrementell ögonblicksbild: program kan dra nytta av sidan blob ögonblicksbilder och REST API: er för att spara kontrollpunkterna programmet utan att det medför kostsamma duplicering av data. Azure Storage stöder lokala ögonblicksbilder för sidblobbar som behöver kopiera hela blobben. Dessa offentliga ögonblicksbild API: er även aktivera åtkomst till och kopiera går mellan ögonblicksbilder.
* Direktmigrering av program och data från lokalt till molnet: kopiera data lokalt och använda REST API: er för att skriva direkt till en Azure-sidblob medan den lokala virtuella datorn fortsätter att köras. När målet har fångats, kan du snabbt redundans till virtuella Azure-datorn med hjälp av dessa data. På så sätt kan du kan migrera dina virtuella datorer och virtuella diskar från lokalt till molnet med minimal avbrottstid eftersom migrering av data sker i bakgrunden medan du fortsätter att använda den virtuella datorn och avbrottstiden som behövs för växling vid fel är kort (i minuter).
* [SAS-baserad](../common/storage-dotnet-shared-access-signature-part-1.md) delad åtkomst, vilket möjliggör scenarier som flera läsare och single-skrivare med stöd för samtidighetskontroll.

## <a name="page-blob-features"></a>Funktioner för sidblob

### <a name="rest-api"></a>REST-API
Se följande dokument från att komma igång med [utveckling med hjälp av sidblobar](storage-dotnet-how-to-use-blobs.md). Titta på hur du kommer åt sidblobbar med Storage-klientbibliotek för .NET som ett exempel. 

Följande diagram visar de övergripande relationerna mellan kontot, behållare och sidblobbar.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Skapa en tom sida blob med en angiven storlek
Om du vill skapa en sidblob först skapar vi en **CloudBlobClient** objekt med bas-URI för åtkomst till blobblagring för ditt lagringskonto (*pbaccount* i figur 1) tillsammans med den  **StorageCredentialsAccountAndKey** objekt som visas i följande exempel. Exemplet visar hur du skapar en referens till en **CloudBlobContainer** objekt och sedan skapa behållaren (*testvhds*) om den inte redan finns. Med hjälp av den **CloudBlobContainer** objekt, skapa en referens till en **CloudPageBlob** objekt genom att ange blob sidnamn (os4.vhd) till åtkomst. Om du vill skapa sidblob anropa [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) skicka i maxstorlek att skapa blob. Den *blobSize* måste vara delbar med 512 byte.

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

#### <a name="resizing-a-page-blob"></a>Ändra storlek på en sidblob
Ändra storlek på en sidblob när den har skapats genom att använda den [ändra storlek på](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) API. Den begärda storleken måste vara delbar med 512 byte.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Skriva sidor till en sidblob
Om du vill skriva sidor, Använd den [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx) metod.  På så sätt kan du skriva en sekventiell uppsättning sidor upp till 4MBs. Förskjutningen skrivs till måste börja på en 512 byte-gräns (startingOffset % 512 == 0), och slutet på en 512 gräns - 1.  Följande kodexempel visar hur du anropar **WritePages** för en blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

När en write-förfrågan för en sekventiell uppsättning sidor lyckas blob-tjänsten och replikeras för hållbarhet och återhämtning, skrivning har genomförts och lyckas returneras till klienten.  

Den nedan visas diagram 2 separat skrivåtgärder:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  En skrivåtgärd som börjar vid offset 0 med längden 1 024 byte 
2.  En skrivåtgärd som börjar vid offset 4096 längden 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Läsa sidor från en sidblob
Om du vill läsa sidor, Använd den [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) metod för att läsa byte från sidan-blob. På så sätt kan du hämta fullständig blob eller byte från alla förskjutning i blob. Vid läsning, behöver förskjutningen inte starta på delbar med 512. När du läser byte från en sida för NUL returnerar tjänsten noll byte.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
Följande bild visar en Läsåtgärd med BlobOffSet 256 och rangeSize av 4352. Data som returnerades är markerad i Orange. Nollor returneras för NUL sidor

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Om du har en glesbefolkade blob kanske du vill hämta endast giltig regioner för att undvika betalar för egressing noll byte och minska svarstiden för hämtning.  Använd för att fastställa vilka sidor som backas upp av data [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Du kan räkna upp returnerade intervall och hämta data i varje område. 
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

#### <a name="leasing-a-page-blob"></a>En sidblob-uthyrning
Åtgärden lån Blob upprättar och hanterar ett lås på en blob för skrivning och delete-åtgärder. Den här åtgärden är användbart i scenarier där en sidblob nås från flera klienter för att säkerställa att endast en klient kan skriva till blob i taget. Azure-diskar, till exempel utnyttjar detta leasing mekanism för att se till att disken är endast hanteras av en enda virtuell dator. Lås varaktighet kan vara 15 till 60 sekunder eller kan vara oändlig. Finns i dokumentationen för [här](/rest/api/storageservices/lease-blob) för mer information.

> Använd följande länk för att få [kodexempel](/resources/samples/?service=storage&term=blob&sort=0 ) för många Programscenarier för andra. 

Förutom omfattande REST API: er dessutom sidblobbar delad åtkomst, hållbarhet och förbättrad säkerhet. Vi tar upp dessa fördelar i detalj i nästa punkter. 

### <a name="concurrent-access"></a>Samtidig åtkomst
Sidblobbar REST-API och dess leasingmekanism ger åtkomst till sidblob från flera klienter. Anta exempelvis att du behöver för att skapa en distribuerad molnbaserad tjänst som delar lagringsobjekt med flera användare. Det kan vara ett webbprogram som betjänar en stor mängd bilder till flera användare. Ett alternativ för att implementera detta är att använda en virtuell dator med anslutna diskar. Downsides för den här inkludera (i) begränsningen en disk kan endast kopplas till en enskild virtuell dator vilket begränsar skalbarhet, flexibilitet och öka risker. Om det finns ett problem med den virtuella datorn eller tjänsten som körs på den virtuella datorn, sedan blir på grund av lånet, avbildningen inte tillgänglig förrän lånet upphör att gälla eller är brutna; och (ii) extra kostnader för att en IaaS VM. 

Ett alternativ är att använda sidblobar direkt via Azure Storage REST API: er. Det här alternativet eliminerar behovet av kostsamt IaaS-VM, ger fullständig flexibilitet direktåtkomst från flera klienter, förenklar den klassiska distributionsmodellen genom att eliminera behovet av att koppla/frånkoppla diskar och eliminerar risken för problem på den virtuella datorn. Och ger samma nivå av prestanda för slumpmässig läsning/skrivning åtgärder som en disk

### <a name="durability-and-high-availability"></a>Hållbarhet och hög tillgänglighet
Både Standard och premium-lagring är beständig lagring där sidan blob-data replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Mer information om Azure Storage redundans finns [dokumentationen](../common/storage-redundancy.md). Azure har konsekvent levereras företagsklass hållbarhet för IaaS-diskar och sidblobbar, med ett branschledande noll % [medel Felintervall](https://en.wikipedia.org/wiki/Annualized_failure_rate). Det vill säga har Azure aldrig förlorat kundens sida blob-data. 

### <a name="seamless-migration-to-azure"></a>Sömlös migrering till azure
Azure erbjuder också inkrementella ögonblicksbilder som endast innehåller går för kunder och utvecklare som vill för att implementera sina egna anpassade lösning för säkerhetskopiering. Den här funktionen förhindrar kostnaden för den första fullständiga kopian, vilket avsevärt minskar kostnaden för säkerhetskopiering. Tillsammans med möjligheten att effektivt Läs- och kopiera differentiella data är detta en kraftfull funktion som gör att ytterligare innovationer från utvecklare, vilket leder till en bäst i klassen säkerhetskopiering och katastrofåterställning (DR) återställningsfunktioner på Azure. Du kan ställa in din egen säkerhetskopiering eller DR-lösning för din virtuella dator på Azure med hjälp av [Blob ögonblicksbild](/rest/api/storageservices/snapshot-blob) tillsammans med den [Get sidintervall](/rest/api/storageservices/get-page-ranges) API och [inkrementell kopiera Blob](/rest/api/storageservices/incremental-copy-blob) API, som du kan Använd för att enkelt kopiera de stegvisa data för Katastrofåterställning. 

Många företag har dessutom viktiga arbetsbelastningar som körs i lokalt datacenter. För att migrera arbetsbelastningen till molnet är ett av de viktigaste aspekterna stilleståndstiden som behövs för att kopiera data och risken för oförutsedda problem efter övergången. I många fall vara avbrottstiden en SHOWENS stjärna för migrering till molnet. På sidan blobbar REST-API, Azure löser problemet genom att aktivera molnmigrering med minimala störningar för kritiska arbetsbelastningar. 

Exempel på hur du kan ta en ögonblicksbild och hur du återställer en sidblob från en ögonblicksbild, referera till den [installationsprocessen en säkerhetskopiering med inkrementell ögonblicksbilder](../../virtual-machines/windows/incremental-snapshots.md) artikel.
