---
title: Kom igång med blob storage och Visual Studio-anslutna tjänster (WebJob-projekt) | Microsoft Docs
description: Hur du kommer igång med Blob storage i ett WebJob-projekt när du har anslutit till Azure storage med hjälp av Visual Studio-anslutna tjänster.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5a7c16e6ac565d1660fee02cb7df178344b195e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122932"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Kom igång med Azure Blob storage och Visual Studio-anslutna tjänster (WebJob-projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller C#-kodexempel som visar hur du utlöser en process när en Azure-blob skapas eller uppdateras. Använd kodexempel i [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x. När du lägger till ett lagringskonto till ett WebJob-projekt med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan lämplig Azure Storage NuGet-paketet har installerats, lämplig .NET-referenser läggs till i projektet, och anslutningssträngar för storage-konto har uppdaterats i filen App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Hur du utlöser en funktion när en blob skapas eller uppdateras
Det här avsnittet visar hur du använder den **BlobTrigger** attribut.

 **Obs!** WebJobs SDK igenom loggfiler för att se upp för nya eller ändrade blobar. Den här processen är sin natur långsamma; en funktion kan hämta aktiveras inte förrän flera minuter eller längre när bloben har skapats.  Om programmet behöver för att bearbeta blobar omedelbart, den rekommenderade metoden är att skapa ett kömeddelande när du skapar bloben och använder den **QueueTrigger** attribut i stället för den **BlobTrigger** den funktion som bearbetar blob-attributet.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enkel platshållare för blobnamn med filtillägg
Följande kodexempel kopierar text BLOB-objekt som visas i den *inkommande* behållaren till den *utdata* behållare:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Attributkonstruktorn använder en strängparameter som anger behållarens namn och en platshållare för blob-namnet. I det här exemplet, om en blob med namnet *Blob1.txt* skapas i den *inkommande* behållare, funktionen skapar en blob med namnet *Blob1.txt* i den *utdata* behållare.

Du kan ange ett namnmönster med platshållaren för blob-namnet som visas i följande kodexempel:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Den här koden kopierar endast blobar som har namn som börjar med ”ursprungliga-”. Till exempel *ursprungliga Blob1.txt* i den *inkommande* behållare har kopierats till *kopia Blob1.txt* i den *utdata* behållare.

Om du vill ange ett namnmönster för blobnamn som innehåller klammerparenteser i namnet dubbelklicka av klammerparenteser. Exempel: Om du vill hitta blobbar i den *avbildningar* behållare som har namn så här:

        {20140101}-soundfile.mp3

Använd detta för mönstret:

        images/{{20140101}}-{name}

I det här exemplet på *namn* platshållarvärdet skulle vara *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separat blob namn och filnamnstillägg för platshållare
Följande kodexempel ändras filnamnstillägget när den kopierar blobar som visas i den *inkommande* behållaren till den *utdata* behållare. Koden loggar utökning av den *inkommande* blob och ställer in tillägg till den *utdata* -blob till *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Typer av som du kan binda till BLOB
Du kan använda den **BlobTrigger** attribut för följande typer:

* **sträng**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andra typer av avserialiseras av [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Om du vill arbeta direkt med Azure storage-konto, du kan också lägga till en **CloudStorageAccount** parameter till Metodsignaturen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Hämta blob textinnehåll av bindning till en sträng
Om texten blobar förväntas, **BlobTrigger** kan tillämpas på en **sträng** parametern. Följande kodexempel Binder en text-blob till en **sträng** parameter med namnet **logMessage**. Funktionen använder parametern för att skriva innehållet i blobben till WebJobs-SDK-instrumentpanelen.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Hämta serialiserade blobbinnehåll med hjälp av ICloudBlobStreamBinder
Följande kodexempel används en klass som implementerar **ICloudBlobStreamBinder** att aktivera den **BlobTrigger** attribut för att binda en blob till den **WebImage** typen.

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

Den **WebImage** bindning kod har angetts i en **WebImageBinder** klass som härleds från **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Hur du hanterar skadliga blobar
När en **BlobTrigger** funktionen misslyckas, SDK: N anropar den igen, om felet orsakades av ett tillfälligt fel. Om felet orsakas av innehållet i blobben, misslyckas åtgärden varje gång den försöker bearbeta blob. Som standard anropar SDK en funktion upp till 5 gånger för en viss blob. Om femte misslyckas, SDK: N lägger till ett meddelande till en kö med namnet *webjobs-blobtrigger-poison*.

Det maximala antalet återförsök kan konfigureras. Samma **MaxDequeueCount** inställningen används för hantering av skadliga blob och skadliga kö meddelandehantering.

Kömeddelande för skadliga BLOB är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet *{webbjobbsnamnet}*. Funktioner. *{Funktionsnamn}*, till exempel: WebJob1.Functions.CopyBlob)
* BlobType (”BlockBlob” eller ”PageBlob”)
* ContainerName
* BlobName
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

I följande kodexempel i **CopyBlob** funktionen har kod som gör att den misslyckas varje gång den anropas. När SDK anropar den för det maximala antalet återförsök, ett meddelande har skapats för skadliga blob-kön och meddelandet bearbetas av den **LogPoisonBlob** funktion.

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

SDK: N deserializes automatiskt JSON-meddelande. Här är den **PoisonBlobMessage** klass:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmen för BLOB-avsökning
WebJobs SDK söker igenom alla behållare som anges av **BlobTrigger** attribut vid programstart. I ett stort lagringskonto skanningen kan ta lite tid, så det kan vara ett tag innan nya blobbar finns och **BlobTrigger** funktioner körs.

För att identifiera nya eller ändrade blobbar efter programstart, läser SDK: N regelbundet från blob storage-loggar. Blobbloggar är buffrade och endast fysiskt skrivs var tionde minut eller så, så det kan finnas betydande fördröjning när en blob skapas eller uppdateras innan motsvarande **BlobTrigger** funktionen körs.

Det finns ett undantag för blobbar som du skapar med hjälp av den **Blob** attribut. När WebJobs SDK skapar en ny blob, överförs den nya bloben direkt till någon matchande **BlobTrigger** funktioner. Därför om du har en kedja av blob-in- och utdata, kan SDK: N bearbeta dem effektivt. Men om du vill med låg latens och kör din blob bearbetningsfunktioner för blobar som har skapats eller uppdaterats på annat sätt, bör du använda **QueueTrigger** snarare än **BlobTrigger**.

### <a name="blob-receipts"></a>BLOB kvitton
WebJobs SDK kontrollerar du att ingen **BlobTrigger** funktionen anropas flera gånger för samma nya eller uppdaterade blob. Detta sker genom att *blob kvitton* för att avgöra om en viss blob-version har bearbetats.

BLOB kvitton lagras i en behållare med namnet *webjobs-azure-värdar* i Azure storage-kontot som angetts av anslutningssträngen AzureWebJobsStorage. En blob-inleverans har följande information:

* Den funktion som anropades för blob (”*{webbjobbsnamnet}*. Funktioner. *{Funktionsnamn}*”, till exempel: "WebJob1.Functions.CopyBlob")
* Behållarens namn
* Blobtyp (”BlockBlob” eller ”PageBlob”)
* Blobnamnet
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

Om du vill tvinga ombearbetning av en blob kan du manuellt ta bort blob-kvitto på den blobben från den *webjobs-azure-värdar* behållare.

## <a name="related-topics-covered-by-the-queues-article"></a>Närliggande ämnen som omfattas av köer-artikel
Mer information om hur du hantera blob-bearbetning som utlöses av ett kömeddelande eller för WebJobs SDK scenarier som inte är specifika för blob-bearbetning, finns i [hur du använder Azure-kölagring med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Närliggande ämnen som beskrivs i den här artikeln är följande:

* Async-funktioner
* Flera instanser
* Avslutning
* Använd WebJobs-SDK-attribut i brödtexten i en funktion
* Ange anslutningssträngar för SDK i koden.
* Ange värden för WebJobs SDK konstruktor parametrar i kod
* Konfigurera **MaxDequeueCount** för hantering av skadliga blob.
* Utlös en funktion manuellt
* Sparar loggar

## <a name="next-steps"></a>Nästa steg
Den här artikeln har tillhandahållit exempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-blobar. Mer information om hur du använder Azure WebJobs och WebJobs-SDK finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

