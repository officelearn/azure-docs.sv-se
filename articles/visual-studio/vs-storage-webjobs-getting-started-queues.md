---
title: Komma igång med kölagring med Visual Studio (WebJob-projekt)
description: Komma igång med Azure Queue storage i ett WebJob-projekt efter anslutning till ett lagringskonto med hjälp av Anslutna Visual Studio-tjänster.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298744"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (WebJob Projects)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du kommer igång med Azure-kölagring i ett Visual Studio Azure WebJob-projekt när du har skapat eller refererat till ett Azure-lagringskonto med hjälp av dialogrutan **Lägg till anslutna tjänster i** Visual Studio. När du lägger till ett lagringskonto i ett WebJob-projekt med hjälp av dialogrutan Lägg **till anslutna tjänster** i Visual Studio installeras lämpliga Azure Storage NuGet-paket, lämpliga .NET-referenser läggs till i projektet och anslutningssträngar för lagringskontot uppdateras i filen App.config.  

Den här artikeln innehåller C#-kodexempel som visar hur du använder Azure WebJobs SDK version 1.x med Azure Queue storage-tjänsten.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Mer information [finns i Komma igång med Azure Queue Storage med .NET.](../storage/queues/storage-dotnet-how-to-use-queues.md) Mer information om ASP.NET finns [i ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Så här utlöser du en funktion när ett kömeddelande tas emot
Om du vill skriva en funktion som WebJobs SDK anropar när ett kömeddelande tas emot använder du **attributet QueueTrigger.** Attributkonstruktorn tar en strängparameter som anger namnet på kön som ska avsökas. Om du vill se hur du ställer in könamnet dynamiskt läser du [Så här anger du konfigurationsalternativ](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Meddelanden om strängkö
I följande exempel innehåller kön ett strängmeddelande, så **QueueTrigger** tillämpas på en strängparameter med namnet **logMessage** som innehåller innehållet i kömeddelandet. Funktionen [skriver ett loggmeddelande till instrumentpanelen](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Förutom **sträng**kan parametern vara en bytematris, ett **CloudQueueMessage-objekt** eller en POCO som du definierar.

### <a name="poco-plain-old-clr-object-queue-messages"></a>Kömeddelanden [i POCO-köer (Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
I följande exempel innehåller kömeddelandet JSON för ett **BlobInformation-objekt** som innehåller en **BlobName-egenskap.** SDK deserialiserar automatiskt objektet.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

SDK använder [Newtonsoft.Json NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json) för att serialisera och deserialisera meddelanden. Om du skapar kömeddelanden i ett program som inte använder WebJobs SDK kan du skriva kod som följande exempel för att skapa ett POCO-kömeddelande som SDK kan tolka.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Asynkronfunktioner
Följande async-funktion [skriver en logg till instrumentpanelen](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Async-funktioner kan ta en [annulleringstoken](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), som visas i följande exempel som kopierar en blob. (En förklaring av platshållaren för **köTrigger** finns i avsnittet [Blobbar.)](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Skriver attributet QueueTrigger fungerar med
Du kan använda **QueueTrigger** med följande typer:

* **sträng**
* En POCO-typ serialiserad som JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Avsökningsalgoritm
SDK implementerar en slumpmässig exponentiell back-off-algoritm för att minska effekten av inaktiv kö avsökning på lagringstransaktionskostnader.  När ett meddelande hittas väntar SDK två sekunder och söker sedan efter ett annat meddelande. när inget meddelande hittas väntar det ungefär fyra sekunder innan du försöker igen. Efter efterföljande misslyckade försök att få ett kömeddelande fortsätter väntetiden att öka tills den når den maximala väntetiden, vilket är standard till en minut. [Den maximala väntetiden kan konfigureras.](#how-to-set-configuration-options)

## <a name="multiple-instances"></a>Flera instanser
Om webbappen körs på flera instanser körs en kontinuerlig WebJobs på varje dator och varje dator väntar på utlösare och försöker köra funktioner. I vissa fall kan detta leda till att vissa funktioner bearbetar samma data två gånger, så funktionerna bör vara idempotenta (skrivs så att anropa dem upprepade gånger med samma indata inte ger dubbla resultat).  

## <a name="parallel-execution"></a>Parallell körning
Om du har flera funktioner som lyssnar i olika köer anropar SDK dem parallellt när meddelanden tas emot samtidigt.

Detsamma gäller när flera meddelanden tas emot för en enda kö. Som standard får SDK en batch med 16 kömeddelanden åt gången och kör funktionen som bearbetar dem parallellt. [Batchstorleken kan konfigureras.](#how-to-set-configuration-options) När numret som bearbetas kommer ner till hälften av batchstorleken får SDK en annan batch och börjar bearbeta dessa meddelanden. Därför är det maximala antalet samtidiga meddelanden som bearbetas per funktion en och en halv gånger batchstorleken. Den här gränsen gäller separat för varje funktion som har ett **QueueTrigger-attribut.** Om du inte vill ha parallell körning för meddelanden som tas emot i en kö anger du batchstorleken till 1.

## <a name="get-queue-or-queue-message-metadata"></a>Hämta metadata för kö- eller kömeddelande
Du kan hämta följande meddelandeegenskaper genom att lägga till parametrar i metodsignaturen:

* **DateTimeOffset** förfallerTid
* **Infogning av DateTimeOffsetTime**
* **DateTimeOffset** nästaVisibleTime
* **string** strängköTrigger (innehåller meddelandetext)
* **sträng-ID**
* **sträng** popReceipt
* **int** dequeueCount

Om du vill arbeta direkt med Azure storage API kan du också lägga till en **CloudStorageAccount-parameter.**

I följande exempel skrivs alla dessa metadata till en INFO-programlogg. I exemplet innehåller både logMessage och queueTrigger innehållet i kömeddelandet.

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

Här är en exempellogg skriven av exempelkoden:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Graciös avstängning
En funktion som körs i en kontinuerlig WebJob kan acceptera en **CancellationToken-parameter** som gör det möjligt för operativsystemet att meddela funktionen när WebJob är på väg att avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående WebJob-avslutning i en funktion.

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

**Anm.:** Instrumentpanelen kanske inte visar status och utdata korrekt för funktioner som har stängts av.

Mer information finns i [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Så här skapar du ett kömeddelande när du bearbetar ett kömeddelande
Om du vill skriva en funktion som skapar ett nytt kömeddelande använder du **attributet Kö.** Precis som **QueueTrigger**skickar du i könamnet som en sträng eller så kan du [ange könamnet dynamiskt](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Meddelanden om strängkö
Med följande icke-asynkrona kodexempel skapas ett nytt kömeddelande i kön med namnet "utdataqueue" med samma innehåll som kömeddelandet som tas emot i kön med namnet "inputqueue". (För asynkronfunktioner använder **IAsyncCollector\<T>** som visas senare i det här avsnittet.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Kömeddelanden [i POCO-köer (Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Om du vill skapa ett kömeddelande som innehåller en POCO i stället för en sträng skickar du POCO-typen som utdataparameter till attributet Constructor för **kö.**

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

SDK serialiserar automatiskt objektet till JSON. Ett kömeddelande skapas alltid, även om objektet är null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Skapa flera meddelanden eller i asynkronfunktioner
Om du vill skapa flera meddelanden gör du parametertypen för utdatakön **ICollector\<T>** eller **\<IAsyncCollector T>**, som visas i följande exempel.

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

Varje kömeddelande skapas omedelbart när metoden **Lägg till** anropas.

### <a name="types-that-the-queue-attribute-works-with"></a>Typer som attributet Queue fungerar med
Du kan använda **attributet Queue** på följande parametertyper:

* **ut sträng** (skapar kömeddelande om parametervärdet inte är null när funktionen slutar)
* **ut byte[]** (fungerar som **sträng**)
* **ut CloudQueueMessage** (fungerar som **sträng)**
* **ut POCO** (en serialiserbar typ, skapar ett meddelande med ett null-objekt om parametern är null när funktionen slutar)
* **IKollektor**
* **IAsyncCollector**
* **CloudQueue** (för att skapa meddelanden manuellt med azure storage-API:et direkt)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Använda WebJobs SDK-attribut i en funktions brödtext
Om du behöver göra en del arbete i din funktion innan du använder ett WebJobs SDK-attribut som **Kö,** **Blob**eller **Tabell**kan du använda **IBinder-gränssnittet.**

I följande exempel visas ett indatakömeddelande och ett nytt meddelande skapas med samma innehåll i en utdatakö. Utdatakönamnet anges efter kod i funktionens brödtext.

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

**IBinder-gränssnittet** kan också användas med attributen **Tabell** och **Blob.**

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Så här läser och skriver du blobbar och tabeller när du bearbetar ett kömeddelande
Med attributen **Blob** och **Tabell** kan du läsa och skriva blobbar och tabeller. Exemplen i det här avsnittet gäller blobbar. Kodexempel som visar hur du utlöser processer när blobbar skapas eller uppdateras finns i [Så här använder du Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Strängkömeddelanden som utlöser blob-åtgärder
För ett kömeddelande som innehåller en sträng är **queueTrigger** en platshållare som du kan använda i **Blob-attributets** **blobPath-parameter** som innehåller innehållet i meddelandet.

I följande exempel används **Strömma** objekt för att läsa och skriva blobbar. Kömeddelandet är namnet på en blob som finns i textblobs-behållaren. En kopia av blob med "-ny" bifogad till namnet skapas i samma behållare.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Attributkonstruktorn **Blob** tar en **blobPath-parameter** som anger behållaren och blob-namnet. Mer information om den här platshållaren finns i [Så här använder du Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

När attributet dekorerar ett **Stream-objekt** anger en annan konstruktorparameter **fileaccess-läget** som läst, skriv- eller läs-/skriv-läge.

I följande exempel används ett **CloudBlockBlob-objekt** för att ta bort en blob. Kömeddelandet är namnet på blobben.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>Kömeddelanden [i POCO-köer (Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
För en POCO som lagras som JSON i kömeddelandet kan du använda **Queue** platshållare som namnger egenskaper för objektet i köattributets **blobPath-parameter.** Du kan också använda egenskapsnamn för kömetadata som platshållare. Se [Hämta metadata för kö- eller kömeddelande](#get-queue-or-queue-message-metadata).

I följande exempel kopieras en blob till en ny blob med ett annat tillägg. Kömeddelandet är ett **BlobInformation-objekt** som innehåller **egenskaper BlobName** och **BlobNameWithoutExtension.** Egenskapsnamnen används som platshållare i blob-sökvägen för **Blob-attributen.**

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

SDK använder [Newtonsoft.Json NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json) för att serialisera och deserialisera meddelanden. Om du skapar kömeddelanden i ett program som inte använder WebJobs SDK kan du skriva kod som följande exempel för att skapa ett POCO-kömeddelande som SDK kan tolka.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Om du behöver göra en del arbete i din funktion innan du binder en blob till ett objekt kan du använda attributet i funktionens brödtext, som visas i [Använda WebJobs SDK-attribut i brödtexten i en funktion](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typer som du kan använda attributet Blob med
**Blob-attributet** kan användas med följande typer:

* **Ström** (läsa eller skriva, angiven med parametern FileAccess constructor)
* **TextLäsare**
* **TextWriter**
* **sträng** (läs)
* **utsträngen** (skriv; skapar en blob endast om strängparametern inte är null när funktionen returneras)
* POCO (läs)
* ut POCO (skriva; skapar alltid en blob, skapar som null-objekt om POCO-parametern är null när funktionen returnerar)
* **CloudBlobStream** (skrivning)
* **ICloudBlob** (läs eller skriv)
* **CloudBlockBlob** (läs eller skriv)
* **CloudPageBlob** (läs eller skriv)

## <a name="how-to-handle-poison-messages"></a>Hur man hanterar giftmeddelanden
Meddelanden vars innehåll gör att en funktion misslyckas kallas *giftmeddelanden*. När funktionen misslyckas tas inte kömeddelandet bort och hämtas så småningom igen, vilket gör att cykeln upprepas. SDK kan automatiskt avbryta cykeln efter ett begränsat antal iterationer, eller så kan du göra det manuellt.

### <a name="automatic-poison-message-handling"></a>Automatisk hantering av giftmeddelande
SDK anropar en funktion upp till 5 gånger för att bearbeta ett kömeddelande. Om det femte försöket misslyckas flyttas meddelandet till en giftkö. Du kan se hur du konfigurerar det maximala antalet försök i [Så här anger du konfigurationsalternativ](#how-to-set-configuration-options).

Giftkön heter *{originalqueuename}*-poison. Du kan skriva en funktion för att bearbeta meddelanden från giftkön genom att logga dem eller skicka ett meddelande om att manuell uppmärksamhet behövs.

I följande exempel misslyckas **funktionen CopyBlob** när ett kömeddelande innehåller namnet på en blob som inte finns. När det händer flyttas meddelandet från copyblobqueue-kön till copyblobqueue-poison-kön. **ProcessPoisonMessage** loggar sedan giftmeddelandet.

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

Följande bild visar konsolutdata från dessa funktioner när ett giftmeddelande bearbetas.

![Konsolutgång för hantering av giftmeddelanden](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manuell hantering av giftmeddelanden
Du kan få hur många gånger ett meddelande har hämtats för bearbetning genom att lägga till en **int** parameter med namnet **dequeueCount** till din funktion. Du kan sedan kontrollera antalet avkön i funktionskoden och utföra din egen hantering av giftmeddelande när antalet överskrider ett tröskelvärde, som visas i följande exempel.

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

## <a name="how-to-set-configuration-options"></a>Så här anger du konfigurationsalternativ
Du kan använda typen **JobHostConfiguration** för att ange följande konfigurationsalternativ:

* Ange SDK-anslutningssträngarna i kod.
* Konfigurera **QueueTrigger-inställningar,** till exempel maximalt antal avkön.
* Hämta könamn från konfigurationen.

### <a name="set-sdk-connection-strings-in-code"></a>Ange SDK-anslutningssträngar i kod
Genom att ange SDK-anslutningssträngarna i kod kan du använda dina egna anslutningssträngnamn i konfigurationsfiler eller miljövariabler, som visas i följande exempel.

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
Du kan konfigurera följande inställningar som gäller för bearbetningen av kömeddelande:

* Det maximala antalet kömeddelanden som hämtas samtidigt för att köras parallellt (standard är 16).
* Det maximala antalet försök innan ett kömeddelande skickas till en giftkö (standard är 5).
* Den maximala väntetiden innan avsökning igen när en kö är tom (standard är 1 minut).

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ange värden för WebJobs SDK-konstruktorparametrar i kod
Ibland vill du ange ett könamn, ett blobnamn eller en behållare eller ett tabellnamn i kod i stället för att hårdkoda det. Du kanske till exempel vill ange könamnet för **QueueTrigger** i en konfigurationsfil eller miljövariabel.

Du kan göra det genom att skicka in ett **NameResolver-objekt** till typen **JobHostConfiguration.** Du inkluderar speciella platshållare omgivna av procent (%) tecken i WebJobs SDK-attributkonstruktorparametrar och din **NameResolver-kod** anger de faktiska värden som ska användas i stället för dessa platshållare.

Anta till exempel att du vill använda en kö med namnet logqueuetest i testmiljön och en med namnet logqueueprod i produktionen. I stället för ett hårdkodat könamn vill du ange namnet på en post i **appSettings-samlingen** som skulle ha det faktiska könamnet. Om **appSettings-tangenten** är logqueue kan din funktion se ut som följande exempel.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Klassen **NameResolver** kan sedan hämta könamnet från **appSettings** som visas i följande exempel:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Du skickar klassen **NameResolver** till **JobHost-objektet** enligt följande exempel.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Anm.:** Kö-, tabell- och blob-namn matchas varje gång en funktion anropas, men blob-behållarnamn matchas endast när programmet startar. Du kan inte ändra blob-behållarnamnet medan jobbet körs.

## <a name="how-to-trigger-a-function-manually"></a>Så här utlöser du en funktion manuellt
Om du vill utlösa en funktion manuellt använder du metoden **Call** or **CallAsync** på **JobHost-objektet** och **noautomaticTrigger-attributet** på funktionen, som visas i följande exempel.

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

## <a name="how-to-write-logs"></a>Hur man skriver loggar
Instrumentpanelen visar loggar på två ställen: sidan för WebJob och sidan för en viss WebJob-anrop.

![Loggar in på WebJob-sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Loggar in funktionstallelsesida](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Utdata från konsolmetoder som du anropar i en funktion eller metoden **Main()** visas på instrumentpanelssidan för WebJob, inte på sidan för en viss metodanrop. Utdata från TextWriter-objektet som du får från en parameter i metodsignaturen visas på instrumentpanelssidan för en metodinkallelse.

Konsolutdata kan inte länkas till en viss metodanrop eftersom konsolen är enkeltrådad, medan många jobbfunktioner kan köras samtidigt. Det är därför SDK ger varje funktionsyrop med sitt eget unika loggskrivareobjekt.

Om du vill skriva [programspårningsloggar](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)använder du **Console.Out** (skapar loggar markerade som INFO) och **Console.Error** (skapar loggar markerade som FEL). Ett alternativ är att använda [Trace eller TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), som ger utförliga, varning och kritiska nivåer utöver info och fel. Programspårningsloggar visas i webbapploggfilerna, Azure-tabellerna eller Azure-blobbar beroende på hur du konfigurerar din Azure-webbapp. Som gäller för alla Console-utdata visas även de senaste 100 programloggarna på instrumentpanelssidan för WebJob, inte sidan för en funktionsanrop.

Konsolutdata visas bara i instrumentpanelen om programmet körs i ett Azure WebJob-resultat, inte om programmet körs lokalt eller i någon annan miljö.

Du kan inaktivera loggning genom att ange instrumentpanelsanslutningssträngen till null. Mer information finns i [Så här anger du konfigurationsalternativ](#how-to-set-configuration-options).

I följande exempel visas flera sätt att skriva loggar:

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

I WebJobs SDK Dashboard visas utdata från **TextWriter-objektet** när du går till sidan för en viss funktionsutkallelse och väljer **Växla utdata:**

![Länk till anrop](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Loggar in funktionstallelsesida](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

I WebJobs SDK Dashboard visas de senaste 100 raderna konsolutdata när du går till sidan för WebJob (inte för funktionsinkallelsen) och väljer **Växla utdata**.

![Växla utdata](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

I ett kontinuerligt WebJob visas programloggar i /data/jobs/continuous/*{webjobname}*/job_log.txt i webbappfilsystemet.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

I en Azure-blob ser programloggarna ut så här: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Fel,contosoadsnew,491e54, 635473620738373502,0,17404,19,Console.Error - Hej världen!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Och i en Azure-tabell ser **Console.Out** och **Console.Error-loggarna** ut så här:

![Inlogga i info](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Fel i tabellen Logga in](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln har kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-köer. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentationsresurser](https://go.microsoft.com/fwlink/?linkid=390226).

