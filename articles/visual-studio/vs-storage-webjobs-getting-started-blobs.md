---
title: Kom igång med Blob Storage med Visual Studio (webbjobb-projekt)
description: Hur du kommer igång med Blob Storage i ett webbjobb-projekt efter att ha anslutit till en Azure-lagring med hjälp av Visual Studio Connected Services.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299974"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Kom igång med Azure Blob Storage och anslutna Visual Studio-tjänster (webb jobb projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln C# innehåller kod exempel som visar hur du utlöser en process när en Azure-Blob skapas eller uppdateras. I kod exemplen används [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1. x. När du lägger till ett lagrings konto till ett webbjobb-projekt med hjälp av dialog rutan **Lägg till anslutna tjänster** i Visual Studio, installeras rätt Azure Storage NuGet-paket, lämpliga .net-referenser läggs till i projektet och anslutnings strängar för lagrings kontot uppdateras i filen app. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Så här utlöser du en funktion när en BLOB skapas eller uppdateras
I det här avsnittet visas hur du använder attributet **en** .

 **Obs:** WebJobs-SDK: n söker igenom loggfiler för att se om det är nya eller ändrade blobbar. Den här processen är långsam. en funktion kanske inte utlöses förrän flera minuter eller längre efter att blobben har skapats.  Om ditt program behöver bearbeta blobar omedelbart, är den rekommenderade metoden att skapa ett Queue-meddelande när du skapar blobben och använder attributet **QueueTrigger** i stället för attributet **en** i funktionen som bearbetar blobben .

### <a name="single-placeholder-for-blob-name-with-extension"></a>En plats hållare för BLOB-namn med tillägget
Följande kod exempel kopierar text-blobbar som visas i behållaren *indata* till behållaren *utdata* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Attributet konstruktorn använder en sträng parameter som anger behållarens namn och en plats hållare för BLOB-namnet. I det här exemplet skapar funktionen en blob med namnet *Blob1. txt* i behållaren *utdata* , om en blob med namnet *Blob1. txt* skapas i behållaren för *indata* .

Du kan ange ett namn mönster med plats hållaren för BLOB-namn, som du ser i följande kod exempel:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Den här koden kopierar endast blobbar som har namn som börjar med "original". Till exempel kopieras *original-Blob1. txt* i behållaren för *indata* till *copy-Blob1. txt* i behållaren *utdata* .

Om du behöver ange ett namn mönster för BLOB-namn som har klammerparenteser i namnet, så dubbla klammerparenteser. Om du till exempel vill hitta blobbar i behållaren *avbildningar* som har namn som detta:

        {20140101}-soundfile.mp3

Använd detta för ditt mönster:

        images/{{20140101}}-{name}

I exemplet skulle *namnet* placeholder vara *Soundfile. mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separata plats hållare för BLOB-namn och tillägg
Följande kod exempel ändrar fil namns tillägget när blobbar kopieras som visas i behållaren *indata* till behållaren *utdata* . Koden loggar tillägget för bloben för *indata* och anger utökningen för bloben *output* till *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typer som du kan binda till blobbar
Du kan använda attributet **en** på följande typer:

* **nollängd**
* **TextReader**
* **Skicka**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andra typer avserialiserade av [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Om du vill arbeta direkt med Azure Storage-kontot kan du också lägga till en **CloudStorageAccount** -parameter till Metodsignaturen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Hämta text-BLOB-innehåll efter bindning till sträng
Om text-blobbar förväntas kan **en** tillämpas på en **sträng** parameter. Följande kod exempel binder en text-blob till en **sträng** parameter med namnet **logMessage**. Funktionen använder den parametern för att skriva innehållet i blobben till WebJobs SDK-instrumentpanelen.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Hämta serialiserat BLOB-innehåll med hjälp av ICloudBlobStreamBinder
I följande kod exempel används en klass som implementerar **ICloudBlobStreamBinder** för att göra det möjligt för **en** -attributet att binda en blob till **webavbildnings** typen.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Bindnings koden för **Webavbildningen** finns i en **WebImageBinder** -klass som härleds från **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Så här hanterar du Poison-blobbar
När en **en** -funktion Miss lyckas anropar SDK: n igen, om felet orsakades av ett tillfälligt fel. Om fel orsakas av BLOB-innehållet Miss lyckas funktionen varje gång den försöker bearbeta blobben. Som standard anropar SDK en funktion upp till fem gånger för en specifik blob. Om det femte försöket Miss lyckas lägger SDK till ett meddelande i en kö med namnet *WebJobs-en-Poison*.

Det maximala antalet återförsök kan konfigureras. Samma **MaxDequeueCount** -inställning används för hantering av skadlig blob och meddelande hantering för hantering av skadlig kö.

Queue-meddelandet för Poison-blobbar är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet *{webb namn för webb namn}* . Funktionen. *{Funktions namn}* , till exempel: WebJob1. functions. CopyBlob)
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (en BLOB versions identifierare, till exempel: "0x8D1DC6E70A277EF")

I följande kod exempel har funktionen **CopyBlob** kod som gör att den Miss kan köras varje gång den anropas. När SDK anropar den för maximalt antal återförsök skapas ett meddelande i den skadliga BLOB-kön och meddelandet bearbetas av **LogPoisonBlob** -funktionen.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK deserialiserar automatiskt JSON-meddelandet. Här är klassen **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>BLOB-avsöknings algoritm
WebJobs-SDK: n söker igenom alla behållare som anges av **en** -attribut vid program start. I ett stort lagrings konto kan genomsökningen ta lite tid, så det kan vara en stund innan nya blobbar hittas och **en** -funktioner körs.

För att identifiera nya eller ändrade blobbar efter att programmet har startats, läser SDK: n med jämna mellanrum från Blob Storage-loggarna. BLOB-loggarna buffras och blir bara fysiskt skrivna var tionde minut, så det kan finnas en betydande fördröjning efter att en BLOB har skapats eller uppdaterats innan motsvarande **en** -funktion körs.

Det finns ett undantag för blobbar som du skapar med hjälp av **BLOB** -attributet. När WebJobs SDK skapar en ny BLOB, skickar den den nya blobben omedelbart till alla matchande **en** -funktioner. Om du har en kedja av BLOB-indata och utdata, kan SDK: n bearbeta dem effektivt. Men om du vill ha låg latens för att köra BLOB-bearbetningen för blobbar som skapas eller uppdateras på annat sätt, rekommenderar vi att du använder **QueueTrigger** i stället för **en**.

### <a name="blob-receipts"></a>BLOB-kvitton
WebJobs SDK ser till att ingen **en** -funktion anropas mer än en gång för samma nya eller uppdaterade blob. Det gör detta genom att underhålla *BLOB-kvitton* för att avgöra om en specifik blob-version har bearbetats.

BLOB-kvitton lagras i en behållare med namnet *Azure-WebJobs-hosts* i Azure Storage-kontot som anges av AzureWebJobsStorage-anslutnings strängen. Ett BLOB-kvitto har följande information:

* Funktionen som anropades för blobben (" *{webb namn}* . Funktionen. *{Function Name}* ", till exempel:" WebJob1. functions. CopyBlob ")
* Behållarens namn
* Blob-typ ("BlockBlob" eller "PageBlob")
* BLOB-namnet
* ETag (en BLOB versions identifierare, till exempel: "0x8D1DC6E70A277EF")

Om du vill framtvinga en ombearbetning av en BLOB kan du manuellt ta bort BLOB-kvittot för denna BLOB från behållaren *Azure-WebJobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Närliggande ämnen som omfattas av artikel köerna
Information om hur du hanterar BLOB-bearbetning som utlöses av ett köat meddelande, eller för WebJobs SDK-scenarier som inte är bara för BLOB-bearbetning, finns i [så här använder du Azure Queue Storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Följande avsnitt innehåller information om följande:

* Asynkrona funktioner
* Flera instanser
* Korrekt avstängning
* Använd WebJobs SDK-attribut i bröd texten i en funktion
* Ange anslutnings strängar för SDK i kod.
* Ange värden för WebJobs SDK-konstruktorns parametrar i kod
* Konfigurera **MaxDequeueCount** för hantering av Poison-blob.
* Utlös en funktion manuellt
* Skriv loggar

## <a name="next-steps"></a>Nästa steg
I den här artikeln finns kod exempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-blobbar. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentations resurser](https://go.microsoft.com/fwlink/?linkid=390226).

