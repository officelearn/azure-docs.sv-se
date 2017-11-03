---
title: "Kom igång med blob storage och Visual Studio anslutna tjänster (Webbjobb projekt) | Microsoft Docs"
description: "Hur du kommer igång med Blob storage i ett Webbjobb projekt efter anslutning till en Azure storage med hjälp av Visual Studio anslutna tjänster."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 7d683f950e8847a18f38158a8f8727b1274fc711
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Kom igång med Azure Blob storage och Visual Studio anslutna tjänster (Webbjobb projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller C#-kodexempel som visar hur du utlöser en process när ett Azure BLOB-objekt skapas eller uppdateras. Koden exempel användning av [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x. När du lägger till ett lagringskonto till ett Webbjobb-projekt med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan lämplig Azure Storage NuGet-paketet har installerats, lämpliga .NET referenserna har lagts till i projektet, och anslutningssträngar för storage-konto har uppdaterats i filen App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Hur du utlöser en funktion när en blob skapas eller uppdateras
Det här avsnittet visar hur du använder den **BlobTrigger** attribut.

 **Obs:** WebJobs SDK igenom loggfiler kan du titta på för nya eller ändrade BLOB. Den här processen är långsam natur; en funktion kan hämta aktiveras inte förrän flera minuter eller längre efter blob skapas.  Om ditt program måste bearbeta blobbar omedelbart, den rekommenderade metoden är att skapa ett kömeddelande när du skapar blob och använder den **QueueTrigger** attribut i stället för den **BlobTrigger** attributet för den funktion som behandlar blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enskild platshållare för blob-namn med filtillägg
Följande kodexempel kopierar text blobbar som visas i den *inkommande* behållare för att den *utdata* behållare:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Attributkonstruktorn använder en strängparameter som anger behållarens namn och en platshållare för blob-namnet. I det här exemplet, om en blob med namnet *Blob1.txt* skapas i den *inkommande* -behållaren i funktionen skapas en blob med namnet *Blob1.txt* i den *utdata* behållare.

Du kan ange ett mönster med platshållaren för blob-namnet som visas i följande kodexempel:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Den här koden kopieras endast blob som har namn som börjar med ”ursprungliga-”. Till exempel *ursprungliga Blob1.txt* i den *inkommande* behållare kopieras till *kopiera Blob1.txt* i den *utdata* behållare.

Om du behöver ange ett på namnmönster för blob-namn som innehåller klammerparenteser i namnet dubbla klammerparenteser. Om du vill söka efter blobbar i till exempel den *bilder* behållare som har namn så här:

        {20140101}-soundfile.mp3

Använd detta för dina mönster:

        images/{{20140101}}-{name}

I det här exemplet kan den *namn* platshållare värdet *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separat blob namn och filnamnstillägg för platshållare
Följande kodexempel ändras filnamnstillägget när den kopierar blobar som visas i den *inkommande* behållare för att den *utdata* behållare. Koden loggar tillägget för den *inkommande* blob och anger filnamnstillägget för den *utdata* blob till *.txt*.

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
Du kan använda den **BlobTrigger** attribut för följande typer:

* **sträng**
* **TextReader**
* **Dataströmmen**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andra typer som avserialiseras av [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Om du vill arbeta direkt med Azure storage-konto, du kan också lägga till en **CloudStorageAccount** parameter till Metodsignaturen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Hämtar blob textinnehåll av bindning till en sträng
Om texten blobbar förväntas **BlobTrigger** kan tillämpas på en **sträng** parameter. Följande kodexempel Binder en text blobb till en **sträng** parameter med namnet **logMessage**. Parametern använder funktionen för att skriva innehållet i blob till WebJobs-SDK-instrumentpanelen.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Hämta serialiserad blob-innehåll med hjälp av ICloudBlobStreamBinder
Följande kodexempel används en klass som implementerar **ICloudBlobStreamBinder** att aktivera den **BlobTrigger** attribut för att binda en blobb till den **WebImage** typen.

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

## <a name="how-to-handle-poison-blobs"></a>Hur du hanterar skadligt blobbar
När en **BlobTrigger** funktionen misslyckas SDK anropar den igen, om felet orsakades av ett tillfälligt fel. Om felet orsakas av innehållet för blob, misslyckas åtgärden varje gång den försöker bearbeta blob. Som standard anropar SDK en funktion upp till 5 gånger för en given blob. Om femte misslyckas, SDK lägger till ett meddelande till en kö med namnet *webjobs-blobtrigger-poison*.

Det maximala antalet nya försök kan konfigureras. Samma **MaxDequeueCount** inställningen används för hantering av skadligt blob och hantering av skadligt kön meddelandet.

Kömeddelande för skadligt BLOB är en JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet *{webbjobbsnamnet}*. Funktioner. *{Funktionsnamn}*, till exempel: WebJob1.Functions.CopyBlob)
* BlobType (”BlockBlob” eller ”PageBlob”)
* ContainerName
* BlobName
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

I följande kodexempel i **CopyBlob** funktionen har kod som gör att den misslyckas varje gång den anropas. När SDK-anrop den för det maximala antalet nya försök kan skapa ett meddelande i kön skadligt blob och meddelandet bearbetas av den **LogPoisonBlob** funktion.

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

SDK deserializes automatiskt JSON-meddelandet. Här är den **PoisonBlobMessage** klass:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmen för BLOB-avsökning
WebJobs SDK söker igenom alla behållare som anges av **BlobTrigger** attribut vid programstart. I ett stort lagringskonto skanningen kan ta lite tid, så det kan vara en stund innan nya blobbar finns och **BlobTrigger** funktioner utförs.

För att identifiera nya eller ändrade blobbar när programmet startas läser SDK regelbundet från blob storage-loggar. Blob-loggarna är buffrade och bara hämta fysiskt skriva var 10: e minut eller så, så det kan finnas betydande fördröjning när en blob skapas eller uppdateras innan den motsvarande **BlobTrigger** funktionen körs.

Det finns ett undantag för blob som du skapar med hjälp av den **Blob** attribut. När WebJobs SDK skapar en ny blob, passerar de nya blob omedelbart för alla matchande **BlobTrigger** funktioner. Därför om du har en kedja av blob-in- och utdataenheter kan SDK bearbeta dem effektivt. Men om du vill låg latens och kör din blob bearbetningsfunktioner för blob som skapats eller uppdaterats på annat sätt, bör du använda **QueueTrigger** snarare än **BlobTrigger**.

### <a name="blob-receipts"></a>BLOB kvitton
WebJobs SDK säkerställer du att inga **BlobTrigger** funktionen anropas flera gånger för samma nya eller uppdaterade blob. Detta åstadkoms genom att upprätthålla *blob kvitton* för att avgöra om en viss blob-version har bearbetats.

BLOB kvitton lagras i en behållare med namnet *webjobs-azure-värdar* i Azure storage-konto som angetts i anslutningssträngen för AzureWebJobsStorage. En blob-inleverans har följande information:

* Funktionen anropades för blob (”*{webbjobbsnamnet}*. Funktioner. *{Funktionsnamn}*”, till exempel:” WebJob1.Functions.CopyBlob ”)
* Behållarens namn
* Blob-datatyp (”BlockBlob” eller ”PageBlob”)
* Blobbnamnet
* ETag (en blob versions-ID, till exempel: ”0x8D1DC6E70A277EF”)

Om du vill tvinga ombearbetning av en blob kan du manuellt ta bort blob-inleverans för blobben från den *webjobs-azure-värdar* behållare.

## <a name="related-topics-covered-by-the-queues-article"></a>Närliggande ämnen som omfattas av köer artikel
För information om hur du hanterar blob-bearbetning som utlöses av ett kömeddelande eller för WebJobs SDK scenarier som inte är specifika för blob-bearbetning, se [använda Azure queue storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Närliggande ämnen som beskrivs i artikeln inkluderar följande:

* Async-funktion
* Flera instanser
* Korrekt avslutning
* Använda WebJobs-SDK-attribut i brödtexten för en funktion
* Ange anslutningssträngar SDK i koden.
* Ange värden för WebJobs SDK konstruktorparametrarna i koden
* Konfigurera **MaxDequeueCount** för hantering av skadligt blob.
* Utlös en funktion manuellt
* Skriva loggar

## <a name="next-steps"></a>Nästa steg
Den här artikeln har lämnat kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure BLOB. Mer information om hur du använder Azure WebJobs och WebJobs-SDK finns [Azure WebJobs-dokumentation](http://go.microsoft.com/fwlink/?linkid=390226).

