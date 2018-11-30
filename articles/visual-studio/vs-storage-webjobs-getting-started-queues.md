---
title: Komma igång med queue storage och Visual Studio-anslutna tjänster (WebJob-projekt) | Microsoft Docs
description: Hur du kommer igång med Azure Queue storage i ett WebJob-projekt när du har anslutit till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 899792be583f3b2e2a16e42472fcdf87bf751893
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635500"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (WebJob-projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskrivs hur komma igång med Azure Queue storage i ett projekt i Visual Studio Azure-Webbjobb när du har skapat eller refererar till ett Azure storage-konto med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan. När du lägger till ett lagringskonto till ett WebJob-projekt med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan lämplig Azure Storage NuGet-paket installeras, lämplig .NET-referenser läggs till i projektet, och anslutningssträngar för storage-konto har uppdaterats i filen App.config.  

Den här artikeln innehåller C#-kodexempel som visar hur du använder Azure WebJobs SDK-version 1.x med Azure Queue storage-tjänsten.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Se [Kom igång med Azure Queue Storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) för mer information. Läs mer om ASP.NET [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Hur du utlöser en funktion när ett kömeddelande tas emot
Om du vill skriva en funktion som WebJobs SDK anropar när ett kömeddelande tas emot, använda den **QueueTrigger** attribut. Attributkonstruktorn använder en strängparameter som anger namnet på kön att söka. Om du vill se hur du ställer in namnet på kön dynamiskt, Kolla in [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Sträng-Kömeddelanden
I följande exempel kön innehåller meddelandet sträng så **QueueTrigger** tillämpas på en strängparameter med namnet **logMessage** som innehåller innehållet i kömeddelandet. Funktionen [skriver ett loggmeddelande till instrumentpanelen](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Förutom **sträng**, parametern kan vara en bytematris en **CloudQueueMessage** objekt eller en POCO som du definierar.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
I följande exempel innehåller kömeddelandet JSON för en **BlobInformation** objekt som innehåller en **BlobName** egenskapen. SDK: N deserializes automatiskt objektet.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

SDK: N använder den [Newtonsoft.Json NuGet-paketet](http://www.nuget.org/packages/Newtonsoft.Json) att serialisera och deserialisera meddelanden. Om du skapar Kömeddelanden i ett program som inte använder WebJobs-SDK kan du skriva kod som i följande exempel för att skapa en POCO-kömeddelande som SDK: N kan parsa.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Async-funktioner
Följande async-funktion [skriver en logg till instrumentpanelen](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Async-funktioner kan ta en [annullering token](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), enligt följande exempel som kopierar en blob. (En förklaring av den **queueTrigger** platshållaren finns i den [Blobar](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) avsnittet.)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typer av attributet QueueTrigger fungerar med
Du kan använda **QueueTrigger** med följande typer:

* **sträng**
* En POCO-typ serialiserad som JSON
* **byte]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Avsökningen algoritmen
SDK implementerar en exponentiell backoff-algoritmen för att minska effekten av idle-kön avsökning lagringskostnader för transaktionen.  När ett meddelande hittas SDK väntar två sekunder och söker efter en annan message; När det finns inget meddelande väntar ungefär fyra sekunder innan du försöker igen. Väntetiden fortsätter att öka tills den når maximal väntetid, där standardinställningen är en minut efter flera misslyckade försök att hämta ett kömeddelande. [Maximal väntetid konfigureras](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Flera instanser
Om din webbapp körs på flera instanser, kontinuerliga WebJobs körs på varje dator och varje dator ska vänta tills utlösare och försök att köra functions. I vissa scenarier som detta kan leda till vissa funktioner som bearbetar samma data två gånger, så funktioner ska vara idempotenta (skriven så att anropa dem flera gånger med samma inflödesdata inte ge duplicerade resultat).  

## <a name="parallel-execution"></a>Parallell körning
Om du har flera funktioner som lyssnar på olika köer, ska SDK: N anropa dem parallellt när meddelanden tas emot samtidigt.

Samma sak gäller när flera meddelanden tas emot för en enskild kö. Som standard, SDK: N hämtar en batch med 16 Kömeddelanden i taget och kör den funktion som bearbetar dem parallellt. [Batchstorleken konfigureras](#how-to-set-configuration-options). När antalet bearbetas hämtar till hälften av batchstorleken, hämtar en annan batch SDK och börjar behandla meddelandena. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är därför en och en halv gång batchstorlek. Den här begränsningen gäller separat för varje funktion som har en **QueueTrigger** attribut. Om du inte vill parallell körning för meddelanden som tas emot i en kö, Ange batchstorleken 1.

## <a name="get-queue-or-queue-message-metadata"></a>Hämta kön eller kön meddelande metadata
Du kan få följande meddelandeegenskaper genom att lägga till parametrar till Metodsignaturen:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **sträng** queueTrigger (innehåller textmeddelande)
* **sträng** id
* **sträng** popReceipt
* **int** dequeueCount

Om du vill arbeta direkt med API: et för Azure storage, du kan också lägga till en **CloudStorageAccount** parametern.

I följande exempel skriver alla dessa metadata till en INFO programloggen. I det här exemplet innehålla både logMessage och queueTrigger du innehållet i kömeddelandet.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Här är en exempellogg som skrivits av exempelkoden:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Avslutning
En funktion som körs i ett kontinuerligt Webbjobb kan acceptera en **CancellationToken** parametern där operativsystemet för att meddela funktionen när Webbjobbet ska avslutas. Du kan använda det här meddelandet för att kontrollera att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du kontrollerar om nära förestående WebJob avslutning i en funktion.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Obs:** instrumentpanelen kanske inte korrekt visar status och utdata för funktioner som har stängts av.

Mer information finns i [WebJobs avslutning](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Så här skapar du ett kömeddelande vid bearbetning av ett kömeddelande
Om du vill skriva en funktion som skapar ett nytt meddelande i kön, använder den **kö** attribut. Som **QueueTrigger**kan du skicka in namnet på kön som en sträng eller [ange namnet på kön dynamiskt](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Sträng-Kömeddelanden
Följande kodexempel för icke-async skapar ett nytt meddelande i kön i kön med namnet ”outputqueue” med samma innehåll som tas emot i kön med namnet ”inputqueue” kömeddelandet. (Vid asynkron functions använder **IAsyncCollector<T>**  som du ser senare i det här avsnittet.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
Om du vill skapa ett kömeddelande som innehåller en POCO i stället för en sträng, skicka POCO-typ som en output-parameter till den **kö** attributkonstruktör.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

SDK: N Serialiserar automatiskt objekt till JSON. Ett kömeddelande skapas alltid, även om objektet är null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Skapa flera meddelanden eller i async-funktioner
Om du vill skapa flera meddelanden, göra parametertypen för den utgående kön **ICollector<T>**  eller **IAsyncCollector<T>**, enligt följande exempel.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Varje meddelande i kön skapas direkt när den **Lägg till** metoden anropas.

### <a name="types-that-the-queue-attribute-works-with"></a>Typer som attributet kö som fungerar med
Du kan använda den **kö** attributet på följande parametertyper:

* **ut sträng** (skapar kömeddelande om parametervärdet är inte är null när funktionen avslutas)
* **ut byte []** (fungerar som **sträng**)
* **ut CloudQueueMessage** (fungerar som **sträng**)
* **ut POCO** (en serialiserbar typ. skapar ett meddelande med ett null-objekt om parametern är null när funktionen avslutas)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (för att skapa meddelanden manuellt med hjälp av Azure Storage API direkt)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Använd WebJobs-SDK-attribut i brödtexten i en funktion
Om du vill göra en del arbete i din funktion innan du använder ett WebJobs-SDK-attribut som **kö**, **Blob**, eller **tabell**, du kan använda den **IBinder**gränssnitt.

I följande exempel tar ett inkommande kö-meddelande och skapar ett nytt meddelande med samma innehåll i en utgående kö. Könamn utdata har angetts av kod i brödtexten till funktionen.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Den **IBinder** gränssnitt kan också användas med den **tabell** och **Blob** attribut.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Läsa och skriva blobbar och tabeller vid bearbetning av ett kömeddelande
Den **Blob** och **tabell** attribut kan du läsa och skriva blobbar och tabeller. Exemplen i det här avsnittet gäller för blobar. Kodexempel som visar hur du utlöser processer när BLOB-objekt skapas eller uppdateras, se [hur du använder Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Sträng Kömeddelanden som utlöser blobåtgärder
Ett meddelande i kön som innehåller en sträng **queueTrigger** är en platshållare som du kan använda i den **Blob** attributets **blobPath** parameter som innehåller innehållet i den meddelande.

I följande exempel används **Stream** objekt att läsa och skriva BLOB-objekt. Kömeddelandet är namnet på en blob finns i behållaren textblobs. En kopia av blobben med ”-nya” sist i namnet skapas i samma behållare.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Den **Blob** attributet konstruktorn tar en **blobPath** parameter som anger namnet på behållare och blobnamn. Läs mer om den här platshållaren [hur du använder Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

När attributet decorates en **Stream** objekt, en annan konstruktorparametern anger den **FileAccess** läge som läsa, skriva eller Läs/Skriv.

I följande exempel används en **CloudBlockBlob** objekt att ta bort en blob. Kömeddelandet är namnet på blobben.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
För en POCO lagras som JSON i kömeddelandet, kan du använda platshållare som namnge egenskaperna för objektet i den **kö** attributets **blobPath** parametern. Du kan också använda könamn metadata-egenskap som platshållare. Se [hämta kön eller kön meddelande metadata](#get-queue-or-queue-message-metadata).

I följande exempel kopierar en blob till en ny blob med ett annat tillägg. Kömeddelandet är en **BlobInformation** objekt som innehåller **BlobName** och **BlobNameWithoutExtension** egenskaper. Egenskapsnamnen används som platshållare i blob-sökvägen för den **Blob** attribut.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

SDK: N använder den [Newtonsoft.Json NuGet-paketet](http://www.nuget.org/packages/Newtonsoft.Json) att serialisera och deserialisera meddelanden. Om du skapar Kömeddelanden i ett program som inte använder WebJobs-SDK kan du skriva kod som i följande exempel för att skapa en POCO-kömeddelande som SDK: N kan parsa.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Om du vill göra en del arbete i din funktion innan du binder en blob till ett objekt kan du kan använda attributet i brödtexten till funktionen, som visas i [använder WebJobs-SDK-attribut i brödtexten i en funktion](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Du kan använda Blob-attributet med
Den **Blob** attributet kan användas med följande typer:

* **Stream** (läsa eller skriva, anges med hjälp av konstruktorparametern FileAccess)
* **TextReader**
* **TextWriter**
* **sträng** (läsa)
* **ut sträng** (skriva; skapar en blob endast om strängparametern är icke-null när returnerar funktionen)
* POCO (läsa)
* ut POCO (skriva; alltid skapar en blob, skapar som null-objekt om POCO-parametern är null när funktionen returnerar)
* **CloudBlobStream** (skriva)
* **ICloudBlob** (läsa eller skriva)
* **CloudBlockBlob** (läsa eller skriva)
* **CloudPageBlob** (läsa eller skriva)

## <a name="how-to-handle-poison-messages"></a>Hur du hanterar skadliga meddelanden
Meddelanden vars innehåll orsakar en funktion misslyckas anropas *skadliga meddelanden*. När funktionen misslyckas, tas inte bort kömeddelandet och slutligen hämtas igen, orsakar cykeln ska upprepas. SDK: N kan automatiskt avbryta cykeln när du har ett begränsat antal iterationer eller du kan göra det manuellt.

### <a name="automatic-poison-message-handling"></a>Hantering av automatisk skadligt meddelande
SDK: N ska anropa en funktion upp till 5 gånger för att bearbeta ett kömeddelande. Om den femte försök misslyckas, flyttas meddelandet till en skadliga kö. Du kan se hur du konfigurerar det maximala antalet återförsök i [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

Skadliga kön heter *{originalqueuename}*-skadliga. Du kan skriva en funktion du bearbetar meddelanden från skadliga kön av loggning av dem eller skicka ett meddelande till den manuella åtgärder krävs.

I följande exempel på **CopyBlob** funktionen kommer att misslyckas när ett kömeddelande innehåller namnet på en blob som inte finns. När det sker så flyttas meddelandet från kön copyblobqueue till copyblobqueue poison kön. Den **ProcessPoisonMessage** loggar skadliga meddelandet.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Följande bild visar konsolens utdata från dessa funktioner när ett skadligt meddelande bearbetas.

![Konsolens utdata för hantering av skadligt meddelande](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Hantering av manuell skadligt meddelande
Du kan hämta hur många gånger meddelandet har hämtats för bearbetning genom att lägga till en **int** parameter med namnet **dequeueCount** till din funktion. Du kan kontrollera antalet ta i Funktionskoden och utföra egna skadliga meddelandehantering när antalet överskrider ett tröskelvärde som du ser i följande exempel.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Hur du ställer in konfigurationsalternativ
Du kan använda den **JobHostConfiguration** Skriv för att ange följande konfigurationsalternativ:

* Ange anslutningssträngar för SDK i koden.
* Konfigurera **QueueTrigger** inställningar, till exempel högsta antal som borttagningar.
* Hämta könamn från konfigurationen.

### <a name="set-sdk-connection-strings-in-code"></a>Ange anslutningssträngar för SDK i koden
Ange anslutningssträngar för SDK i koden kan du använda din egen anslutning sträng namn i konfigurationsfiler eller miljövariabler, som visas i följande exempel.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Konfigurera inställningar för QueueTrigger
Du kan konfigurera följande inställningar som gäller för meddelandehantering kö:

* Det maximala antalet Kömeddelanden som plockas upp samtidigt som ska köras parallellt (standard är 16).
* Det maximala antalet återförsök innan ett kömeddelande skickas till en skadliga kö (standardvärdet är 5).
* Maximal väntetid före avsökning igen när en kö är tom (standardvärdet är 1 minut).

I följande exempel visas hur du konfigurerar dessa inställningar:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ange värden för WebJobs SDK konstruktor parametrar i kod
Vill ibland du ange en kö, ett blobnamn eller behållare eller en tabell i kod i stället för hårdkoda ge den namnet. Du kanske exempelvis vill ange namnet på kön för **QueueTrigger** i en konfiguration av fil- eller miljö variabel.

Du kan göra det genom att skicka in en **NameResolver** objekt till den **JobHostConfiguration** typen. Du inkluderar särskild platshållare som omges av procenttecken (%) i WebJobs SDK attributet konstruktor parametrar och din **NameResolver** kod anger de faktiska värdena som ska användas i stället för platshållarna.

Anta exempelvis att du vill använda en kö med namnet logqueuetest i testmiljön och en namngiven logqueueprod i produktion. I stället för ett hårdkodat könamn som du vill ange namnet på en post i den **appSettings** samling som skulle ha faktiska könamnet. Om den **appSettings** nyckeln är logqueue, din funktion bör se ut som i följande exempel.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Din **NameResolver** klassen kan sedan hämta könamn från **appSettings** som visas i följande exempel:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Du skickar den **NameResolver** klassen i till den **JobHost** objekt som visas i följande exempel.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Obs:** kö, tabell och blobnamn har åtgärdats varje gång som en funktion, men blob-behållarnamn löses bara när programmet startas. Du kan inte ändra namnet när jobbet körs.

## <a name="how-to-trigger-a-function-manually"></a>Hur du utlöser en funktion manuellt
Utlös en funktion manuellt genom att använda den **anropa** eller **CallAsync** metoden på den **JobHost** objekt och **NoAutomaticTrigger** attribut i funktionen, som visas i följande exempel.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Hur du skriver loggar
Instrumentpanelen visar loggar på två platser: sidan för Webbjobbet, och på sidan för ett särskilt WebJob-anrop.

![Loggar i Webbjobbet sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Loggar i funktionen anrops-sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Utdata från konsolen metoder som anropas i en funktion eller i den **Main()** metoden visas på sidan instrumentpanel för Webbjobbet, inte på sidan för en viss metodanropet. Utdata från det TextWriter-objekt som du får från en parameter i Metodsignaturen visas i vyn instrumentpanel för ett metodanrop.

Konsolens utdata kan inte länkas till en viss metodanropet eftersom konsolen är enkla trådar, även om många jobbfunktioner kanske körs på samma gång. Det är därför SDK innehåller varje funktionsanrop med en egen unik log-Skrivarobjektet.

Att skriva [programloggarna för spårning av](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), använda **Console.Out** (skapar loggar som är märkta som information) och **Console.Error** (skapar loggar som är märkta som fel). Ett alternativ är att använda [spårningen eller TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), som innehåller utförlig, varning, och kritiska nivåer utöver information och fel. Spårningsloggar för program visas i web app loggfiler, Azure-tabeller, eller Azure BLOB-objekt beroende på hur du konfigurerar Azure-webbappen. Som gäller för alla konsolens utdata, visas de senaste 100 programloggarna också i instrumentpanelsidan för Webbjobbet, inte sidan för ett funktionsanrop.

Konsolens utdata visas i instrumentpanelen bara om programmet körs i ett Azure WebJob inte om programmet körs lokalt eller i någon annan miljö.

Du kan inaktivera loggning genom att ange strängen som instrumentpanelen till null. Mer information finns i [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

I följande exempel visar flera olika sätt att skriva loggar:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

WebJobs SDK instrumentpanelen för utdata från den **TextWriter** objekt visar upp när du gå till sidan för en viss fungera anrop och välj **växla utdata**:

![Anrops-länk](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Loggar i funktionen anrops-sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

WebJobs SDK instrumentpanelen de senaste 100 rader i konsolen utdata visa upp när du gå till sidan för Webbjobbet (inte för funktionsanrop) och väljer **växla utdata**.

![Visa/Dölj utdata](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

I ett kontinuerligt Webbjobb programloggar som visas i/data/jobb/kontinuerlig/*{webjobname}*/job_log.txt i filsystemet för web app.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

I en Azure blob program loggar ser ut så här: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13, fel, contosoadsnew, 491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Och i en Azure-tabell i **Console.Out** och **Console.Error** loggar ut så här:

![Info log i tabellen](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Felloggen i tabellen](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln har tillhandahållit exempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-köer. Mer information om hur du använder Azure WebJobs och WebJobs-SDK finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

