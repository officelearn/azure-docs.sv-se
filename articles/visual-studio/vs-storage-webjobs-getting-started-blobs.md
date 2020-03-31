---
title: Komma igång med blob-lagring med Visual Studio (WebJob-projekt)
description: Komma igång med Blob-lagring i ett WebJob-projekt efter anslutning till ett Azure-lagringsutrymme med hjälp av Visual Studio-anslutna tjänster.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299974"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Komma igång med Azure Blob storage och Visual Studio-anslutna tjänster (WebJob-projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln innehåller C#-kodexempel som visar hur du utlöser en process när en Azure-blob skapas eller uppdateras. Kodexemplen använder [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x. När du lägger till ett lagringskonto i ett WebJob-projekt med hjälp av dialogrutan Lägg **till anslutna tjänster** i Visual Studio installeras lämpligt Azure Storage NuGet-paket, lämpliga .NET-referenser läggs till i projektet och anslutningssträngar för lagringskontot uppdateras i filen App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Så här utlöser du en funktion när en blob skapas eller uppdateras
Det här avsnittet visar hur du använder **attributet BlobTrigger.**

 **Anm.:** WebJobs SDK söker igenom loggfiler för att titta efter nya eller ändrade blobbar. Denna process är i sig långsam; en funktion kanske inte utlöses förrän flera minuter eller längre efter att blobben har skapats.  Om ditt program behöver bearbeta blobbar omedelbart, är den rekommenderade metoden att skapa ett kömeddelande när du skapar blobben och använda **attributet QueueTrigger** i stället för **attributet BlobTrigger** på funktionen som bearbetar blobben.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enskild platshållare för blobnamn med tillägg
I följande kodexempel kopieras textblobar som visas i *indatabehållaren* till *utdatabehållaren:*

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Attributkonstruktorn tar en strängparameter som anger behållarnamnet och en platshållare för blobnamnet. I det här exemplet, om en blob med namnet *Blob1.txt* skapas i *indatabehållaren,* skapar funktionen en blob med namnet *Blob1.txt* i *utdatabehållaren.*

Du kan ange ett namnmönster med platshållaren för blobnamn, som visas i följande kodexempel:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Denna kod kopierar endast blobbar som har namn som börjar med "original-". *Original-Blob1.txt* i *indatabehållaren* kopieras till exempel till *copy-Blob1.txt* i *utdatabehållaren.*

Om du behöver ange ett namnmönster för blobnamn som har klammerparenteser i namnet dubblar du klammerparenteserna. Om du till exempel vill hitta blobbar i *avbildningsbehållaren* som har namn som detta:

        {20140101}-soundfile.mp3

använd detta för ditt mönster:

        images/{{20140101}}-{name}

I exemplet skulle *name* namnplatshållarvärdet vara *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separata blobnamn och tilltilläggsplatshållare
Följande kodexempel ändrar filtillägget när det kopierar blobbar som visas i *indatabehållaren* till *utdatabehållaren.* Koden loggar tillägget för *indatabloben* och anger tillägget för *utdatabloben* till *.txt*.

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
Du kan använda **attributet BlobTrigger** på följande typer:

* **sträng**
* **TextLäsare**
* **Strömma**
* **ICloudBlob (på andra)**
* **CloudBlockBlob**
* **CloudPageBlob (moln)**
* Andra typer deserialized av [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Om du vill arbeta direkt med Azure-lagringskontot kan du också lägga till en **CloudStorageAccount-parameter** i metodsignaturen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Hämta textblob innehåll genom att binda till sträng
Om textblobar förväntas kan **BlobTrigger** användas på en **strängparameter.** Följande kodexempel binder en textblob till en **strängparameter** med namnet **logMessage**. Funktionen använder den parametern för att skriva innehållet i blobben till WebJobs SDK-instrumentpanelen.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Hämta serialiserat blob-innehåll med hjälp av ICloudBlobStreamBinder
I följande kodexempel används en klass som implementerar **ICloudBlobStreamBinder** för att aktivera **attributet BlobTrigger** för att binda en blob till **webimage-typen.**

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

**WebImage-bindningskoden** finns i en **WebImageBinder-klass** som härstammar från **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Hur man hanterar gift blobbar
När en **BlobTrigger-funktion** misslyckas anropar SDK den igen, om felet orsakades av ett tillfälligt fel. Om felet orsakas av innehållet i blobben misslyckas funktionen varje gång den försöker bearbeta bloben. Som standard anropar SDK en funktion upp till 5 gånger för en viss blob. Om det femte försöket misslyckas lägger SDK till ett meddelande i en kö med namnet *webjobs-blobtrigger-poison*.

Det maximala antalet återförsök kan konfigureras. Samma **MaxDequeueCount-inställning** används för hantering av giftblobbhantering och hantering av giftkömeddelande.

Kömeddelandet för giftblobar är ett JSON-objekt som innehåller följande egenskaper:

* FunctionId (i formatet *{WebJob name}*. Funktioner. *{Funktionsnamn}*, till exempel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName (BlobName)
* ETag (en blob-versionsidentifierare, till exempel: "0x8D1DC6E70A277EF")

I följande kodexempel har **funktionen CopyBlob** kod som gör att den misslyckas varje gång den anropas. När SDK har anropat det för det maximala antalet försök skapas ett meddelande i kön för giftblobb och meddelandet bearbetas av funktionen **LogPoisonBlob.**

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

SDK deserialiserar automatiskt JSON-meddelandet. Här är **PoisonBlobMessage** klass:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritm för blob-avsökning
WebJobs SDK söker igenom alla behållare som anges av **BlobTrigger-attribut** vid programstart. I ett stort lagringskonto kan den här genomsökningen ta lite tid, så det kan ta ett tag innan nya blobbar hittas och **BlobTrigger-funktioner** körs.

Om du vill identifiera nya eller ändrade blobbar efter programstart läser SDK regelbundet från blob-lagringsloggarna. Blob-loggarna buffras och skrivs bara fysiskt var 10:e minut eller så, så det kan uppstå betydande fördröjningar när en blob har skapats eller uppdaterats innan motsvarande **BlobTrigger-funktion** körs.

Det finns ett undantag för blobbar som du skapar med attributet **Blob.** När WebJobs SDK skapar en ny blob, skickar den nya blob omedelbart till alla matchande **BlobTrigger-funktioner.** Om du har en kedja med blob-indata och utdata kan SDK därför bearbeta dem effektivt. Men om du vill ha låg latens som kör blob-bearbetningsfunktionerna för blobbar som skapas eller uppdateras på annat sätt rekommenderar vi att du använder **QueueTrigger** i stället för **BlobTrigger**.

### <a name="blob-receipts"></a>Blob-kvitton
WebJobs SDK ser till att ingen **BlobTrigger-funktion anropas** mer än en gång för samma nya eller uppdaterade blob. Detta gör detta genom att underhålla *blob-inleveranser* för att avgöra om en viss blob-version har bearbetats.

Blob-kvitton lagras i en behållare med namnet *azure-webjobs-hosts* i Azure-lagringskontot som anges av AzureWebJobsStorage-anslutningssträngen. Ett blob-kvitto har följande information:

* Funktionen som anropades för blobben ("*{WebJob name}*. Funktioner. *{Funktionsnamn}*", till exempel: "WebJob1.Functions.CopyBlob")
* Behållarnamnet
* Blob-typen ("BlockBlob" eller "PageBlob")
* Blob-namnet
* ETag (en blob-versionsidentifierare, till exempel: "0x8D1DC6E70A277EF")

Om du vill tvinga fram upparbetning av en blob kan du manuellt ta bort blobinleveransen för bloben från behållaren *azure-webjobs-hosts.*

## <a name="related-topics-covered-by-the-queues-article"></a>Relaterade ämnen som omfattas av köer artikeln
Information om hur du hanterar blob-bearbetning som utlöses av ett kömeddelande eller för WebJobs SDK-scenarier som inte är specifika för blob-bearbetning finns i [Så här använder du Azure-kölagring med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Relaterade ämnen som behandlas i den artikeln inkluderar följande:

* Asynkronfunktioner
* Flera instanser
* Graciös avstängning
* Använda WebJobs SDK-attribut i en funktions brödtext
* Ange SDK-anslutningssträngarna i kod.
* Ange värden för WebJobs SDK-konstruktorparametrar i kod
* Konfigurera **MaxDequeueCount** för giftblolobhantering.
* Utlösa en funktion manuellt
* Skriv loggar

## <a name="next-steps"></a>Nästa steg
Den här artikeln har kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-blobbar. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

