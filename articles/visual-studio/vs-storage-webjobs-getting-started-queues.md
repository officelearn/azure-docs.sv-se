---
title: Komma igång med Queue Storage och Visual Studio Connected Services (webb jobbs projekt) | Microsoft Docs
description: Hur du kommer igång med Azure Queue Storage i ett webbjobb-projekt efter att ha anslutit till ett lagrings konto med hjälp av Visual Studio Connected Services.
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
ms.openlocfilehash: 44206f1826fc25407d9dec3f832b70881091e187
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248963"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Komma igång med Azure Queue Storage och Visual Studio Connected Services (webb jobb projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Queue Storage i ett Visual Studio Azure-webbjobb när du har skapat eller refererat till ett Azure Storage-konto med hjälp av dialog rutan **Lägg till anslutna tjänster** i Visual Studio. När du lägger till ett lagrings konto till ett webb jobbs projekt med hjälp av dialog rutan **Lägg till anslutna tjänster** i Visual Studio, installeras rätt Azure Storage NuGet-paket, lämpliga .net-referenser läggs till i projektet och anslutnings strängar för lagrings kontot uppdateras i filen app. config.  

Den här artikeln C# innehåller kod exempel som visar hur du använder Azure WebJobs SDK version 1. x med Azure Queue Storage-tjänsten.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Mer information finns i [Kom igång med Azure Queue Storage med .net](../storage/queues/storage-dotnet-how-to-use-queues.md) . Mer information om ASP.NET finns i [ASP.net](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Så här utlöser du en funktion när ett köat meddelande tas emot
Om du vill skriva en funktion som WebJobs SDK anropar när ett Queue-meddelande tas emot, använder du attributet **QueueTrigger** . Attributet konstruktorn använder en sträng parameter som anger namnet på den kö som ska avsökas. Om du vill se hur du ställer in könamnet dynamiskt kan du läsa [om hur du ställer in konfigurations alternativ](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>String Queue-meddelanden
I följande exempel innehåller kön ett sträng meddelande, så **QueueTrigger** tillämpas på en sträng parameter med namnet **logMessage** som innehåller innehållet i Queue-meddelandet. Funktionen [skriver ett logg meddelande till instrument panelen](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Förutom **sträng**kan parametern vara en byte-matris, ett **CloudQueueMessage** -objekt eller en Poco som du definierar.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanligt gammalt CLR-objekt](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) köa meddelanden
I följande exempel innehåller Queue-meddelandet JSON för ett **BlobInformation** -objekt som innehåller en **BlobName** -egenskap. SDK deserialiserar automatiskt objektet.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

SDK använder [Newtonsoft. JSON NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json) för att serialisera och deserialisera meddelanden. Om du skapar Kömeddelanden i ett program som inte använder WebJobs SDK kan du skriva kod som följande exempel för att skapa ett POCO som SDK: n kan parsa.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Asynkrona funktioner
Följande asynkrona funktion [skriver en logg till instrument panelen](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Asynkrona funktioner kan ta en token för [uppsägning](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)som visas i följande exempel som kopierar en blob. (En förklaring av **queueTrigger** -plats hållaren finns i [](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) avsnittet blobs.)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Skriver QueueTrigger-attributet fungerar med
Du kan använda **QueueTrigger** med följande typer:

* **sträng**
* En POCO-typ serialiserad som JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Avsöknings algoritm
SDK: n implementerar en slumpmässig algoritm för exponentiella inaktivitet för att minska den inaktiva köns avsökning på lagrings transaktions kostnader.  När ett meddelande hittas väntar SDK två sekunder och söker sedan efter ett annat meddelande. När inget meddelande hittas väntar det att vänta i fyra sekunder innan du försöker igen. Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är en minut som standard. [Maximal vänte tid kan konfigureras](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Flera instanser
Om din webbapp körs på flera instanser körs kontinuerliga webbjobb på varje dator och varje dator väntar på utlösare och försöker köra funktioner. I vissa fall kan detta leda till att vissa funktioner bearbetar samma data två gånger, så att funktioner ska vara idempotenta (skrivna så att de anropar dem upprepade gånger med samma indata som inte genererar dubbla resultat).  

## <a name="parallel-execution"></a>Parallell körning
Om du har flera funktioner som lyssnar på olika köer kommer SDK att anropa dem parallellt när meddelanden tas emot samtidigt.

Samma sak gäller när flera meddelanden tas emot för en enskild kö. Som standard hämtar SDK en batch med 16 köa meddelanden i taget och kör funktionen som bearbetar dem parallellt. [Batchstorleken kan konfigureras](#how-to-set-configuration-options). När talet som bearbetas hamnar ned till hälften av batchstorleken, hämtar SDK en annan batch och börjar bearbeta dessa meddelanden. Därför är det maximala antalet samtidiga meddelanden som bearbetas per funktion en och en halv gånger batchstorleken. Den här gränsen gäller separat för varje funktion som har ett **QueueTrigger** -attribut. Om du inte vill att parallellt ska köras för meddelanden som tas emot i en kö ställer du in batchstorleken på 1.

## <a name="get-queue-or-queue-message-metadata"></a>Hämta metadata för kö-eller Queue meddelande
Du kan hämta följande meddelande egenskaper genom att lägga till parametrar till Metodsignaturen:

* **DateTimeOffset** -expirationTime
* **DateTimeOffset** -insertionTime
* **DateTimeOffset** -nextVisibleTime
* **sträng** queueTrigger (innehåller meddelande text)
* **sträng** -ID
* popReceipt för **sträng**
* **int** -dequeueCount

Om du vill arbeta direkt med Azure Storage-API: et kan du också lägga till en **CloudStorageAccount** -parameter.

I följande exempel skrivs alla dessa metadata till en informations program logg. I exemplet innehåller både logMessage och queueTrigger innehållet i Queue-meddelandet.

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

Här är en exempel logg skriven av exempel koden:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Korrekt avstängning
En funktion som körs i ett kontinuerligt webbjobb kan godkänna en **CancellationToken** -parameter som gör det möjligt för operativ systemet att meddela funktionen när webb jobbet är på väg att avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas plötsligt på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående avslutning av webb jobbet i en funktion.

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

**Obs:** Instrument panelen kanske inte korrekt visar status och utdata för funktioner som har avslut ATS.

Mer information finns i [WebJobs reshutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Så här skapar du ett köat meddelande vid bearbetning av ett Queue-meddelande
Om du vill skriva en funktion som skapar ett nytt Queue meddelande, använder du attributet **Queue** . Som **QueueTrigger**skickar du i könamnet som en sträng, eller så kan du [Ange könamnet dynamiskt](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>String Queue-meddelanden
Följande kod exempel för icke-asynkrona kod skapar ett nytt Queue-meddelande i kön med namnet "outputqueue" med samma innehåll som det Queue meddelande som togs emot i kön med namnet "inputqueue". (För asynkrona funktioner **används\<IAsyncCollector T >** som visas senare i det här avsnittet.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanligt gammalt CLR-objekt](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) köa meddelanden
Om du vill skapa ett Queue-meddelande som innehåller en POCO i stället för en sträng, skickar du POCO-typen som en utdataparameter till konstruktorn för attributet **Queue** .

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

SDK: n serialiserar automatiskt objektet till JSON. Ett Queue-meddelande skapas alltid, även om objektet är null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Skapa flera meddelanden eller i asynkrona funktioner
Om du vill skapa flera meddelanden ska du ange parameter typen för **\<ICollector t >** eller **IAsyncCollector\<t >** , som du ser i följande exempel.

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

Varje Queue-meddelande skapas omedelbart när metoden **Add** anropas.

### <a name="types-that-the-queue-attribute-works-with"></a>Typer som attributet Queue fungerar med
Du kan använda attributet **Queue** i följande parameter typer:

* **out-sträng** (Queue meddelande skapas om parametervärdet är icke-null när funktionen slutar)
* **ut byte []** (fungerar som **sträng**)
* **ut CloudQueueMessage** (fungerar som **sträng**)
* **ut Poco** (en serialiserbar typ skapar ett meddelande med ett null-objekt om parametern är null när funktionen slutar)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (för att skapa meddelanden manuellt med Azure Storage-API: et direkt)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Använd WebJobs SDK-attribut i bröd texten i en funktion
Om du behöver göra en del arbete i din funktion innan du använder ett WebJobs SDK-attribut som **kö**, **BLOB**eller **tabell**kan du använda **IBinder** -gränssnittet.

I följande exempel visas ett meddelande om indatakö och ett nytt meddelande skapas med samma innehåll i en utgående kö. Namnet på utdataporten anges med kod i bröd texten i funktionen.

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

**IBinder** -gränssnittet kan också användas med **Table** -och **BLOB** -attribut.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Läsa och skriva blobbar och tabeller vid bearbetning av ett Queue-meddelande
Med **BLOB** -och **Table** -attributen kan du läsa och skriva blobbar och tabeller. Exemplen i det här avsnittet gäller för blobbar. Kod exempel som visar hur du utlöser processer när blobbar skapas eller uppdateras finns i [använda Azure Blob Storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>String Queue-meddelanden som utlöser BLOB-åtgärder
För ett Queue-meddelande som innehåller en sträng är **queueTrigger** en plats hållare som du kan använda i **BLOB** -attributets **blobPath** -parameter som innehåller meddelandets innehåll.

I följande exempel används **Stream** -objekt för att läsa och skriva blobbar. Queue-meddelandet är namnet på en blob som finns i behållaren textblobs. En kopia av blobben med "-New" som läggs till i namnet skapas i samma behållare.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Konstruktorn **BLOB** -attribut använder en **blobPath** -parameter som anger behållare och blob-namn. Mer information om den här plats hållaren finns i [så här använder du Azure Blob Storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

När attributet dekorerar ett **Stream** -objekt, anger en annan konstruktor att **fileaccess** -läget är läsa, skriva eller läsa/skriva.

I följande exempel används ett **CloudBlockBlob** -objekt för att ta bort en blob. Queue-meddelandet är namnet på blobben.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanligt gammalt CLR-objekt](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) köa meddelanden
För en POCO som är lagrad som JSON i Queue-meddelandet kan du använda plats hållare som namn egenskaper för objektet i attributet **blobPath** för **kön** . Du kan också använda egenskaps namn för kö-metadata som plats hållare. Se [Hämta metadata för kö-eller Queue meddelande](#get-queue-or-queue-message-metadata).

I följande exempel kopieras en blob till en ny BLOB med ett annat fil namns tillägg. Queue-meddelandet är ett **BlobInformation** -objekt som innehåller egenskaperna **BlobName** och **BlobNameWithoutExtension** . Egenskaps namnen används som plats hållare i BLOB-sökvägen för **BLOB** -attributen.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

SDK använder [Newtonsoft. JSON NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json) för att serialisera och deserialisera meddelanden. Om du skapar Kömeddelanden i ett program som inte använder WebJobs SDK kan du skriva kod som följande exempel för att skapa ett POCO som SDK: n kan parsa.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Om du behöver göra lite arbete i din funktion innan du binder en blob till ett objekt, kan du använda attributet i bröd texten i funktionen, som visas i avsnittet [använda WebJobs SDK-attribut i bröd texten i en funktion](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typer du kan använda BLOB-attributet med
**BLOB** -attributet kan användas med följande typer:

* **Strömma** (läsning eller skrivning, anges med parametern FileAccess konstruktor)
* **TextReader**
* **TextWriter**
* **sträng** Läs
* **out-sträng** (Write; skapar bara en BLOB om sträng parametern är icke-null när funktionen returnerar)
* POCO (läsa)
* out-POCO (Write; skapar alltid en BLOB, skapar som null-objekt om POCO-parametern är null när funktionen returnerar)
* **CloudBlobStream** Skriv
* **ICloudBlob** (Läs eller skriv)
* **CloudBlockBlob** (Läs eller skriv)
* **CloudPageBlob** (Läs eller skriv)

## <a name="how-to-handle-poison-messages"></a>Så här hanterar du Poison-meddelanden
Meddelanden vars innehåll gör att en funktion inte kan anropas kallas *Poison-meddelanden*. När funktionen Miss lyckas tas inte Queue-meddelandet bort och slutligen hämtas den igen, vilket gör att cykeln upprepas. SDK: n kan automatiskt avbryta cykeln efter ett begränsat antal iterationer, eller så kan du göra det manuellt.

### <a name="automatic-poison-message-handling"></a>Automatisk hantering av skadligt meddelande
SDK kommer att anropa en funktion upp till fem gånger för att bearbeta ett Queue-meddelande. Om det femte försöket Miss lyckas flyttas meddelandet till en Poison-kö. Du kan se hur du konfigurerar det maximala antalet återförsök i [hur du konfigurerar konfigurations alternativ](#how-to-set-configuration-options).

Den Poison-kön heter *{originalqueuename}* -Poison. Du kan skriva en funktion för att bearbeta meddelanden från den Poison-kön genom att logga dem eller skicka ett meddelande om att det krävs en manuell åtgärd.

I följande exempel fungerar inte **CopyBlob** -funktionen när ett Queue-meddelande innehåller namnet på en blob som inte finns. När detta inträffar flyttas meddelandet från copyblobqueue-kön till copyblobqueue-Poison-kön. **ProcessPoisonMessage** loggar sedan in det skadliga meddelandet.

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

Följande bild visar konsol utdata från dessa funktioner när ett Poison-meddelande bearbetas.

![Konsol resultat för hantering av skadligt meddelande](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manuell hantering av Poison-meddelanden
Du kan få antalet gånger som ett meddelande har hämtats för bearbetning genom att lägga till en **int** -parameter med namnet **dequeueCount** i din funktion. Du kan sedan kontrol lera antalet uppräkningar i funktions koden och utföra din egen hantering av Poison-meddelanden när antalet överskrider ett tröskelvärde, som du ser i följande exempel.

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

## <a name="how-to-set-configuration-options"></a>Ange konfigurations alternativ
Du kan använda **JobHostConfiguration** -typen för att ange följande konfigurations alternativ:

* Ange anslutnings strängar för SDK i kod.
* Konfigurera **QueueTrigger** -inställningar som till exempel maximalt antal avköade.
* Hämta könamn från konfigurationen.

### <a name="set-sdk-connection-strings-in-code"></a>Ange anslutnings strängar för SDK i kod
Genom att ange anslutnings strängar för SDK i kod kan du använda dina egna anslutnings sträng namn i konfigurationsfiler eller miljövariabler, som du ser i följande exempel.

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

### <a name="configure-queuetrigger--settings"></a>Konfigurera QueueTrigger-inställningar
Du kan konfigurera följande inställningar som gäller bearbetningen av köa meddelanden:

* Det maximala antalet köa meddelanden som hämtas samtidigt som körs parallellt (standard är 16).
* Maximalt antal försök innan ett Queue-meddelande skickas till en Poison-kö (Standardvärdet är 5).
* Maximal vänte tid innan avsökningen görs igen när en kö är tom (Standardvärdet är 1 minut).

I följande exempel visas hur du konfigurerar de här inställningarna:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ange värden för WebJobs SDK-konstruktorns parametrar i kod
Ibland vill du ange ett könamn, ett BLOB-namn eller behållare, eller ett tabell namn i kod i stället för att hårdkoda det. Du kanske till exempel vill ange könamnet för **QueueTrigger** i en konfigurations fil eller en miljö variabel.

Det kan du göra genom att skicka ett **NameResolver** -objekt till **JobHostConfiguration** -typen. Du inkluderar särskilda plats hållare omgiven av procent (%) loggar in WebJobs SDK-attribut och **NameResolver** -koden anger de faktiska värden som ska användas i stället för dessa plats hållare.

Anta till exempel att du vill använda en kö med namnet logqueuetest i test miljön och en namngiven logqueueprod i produktion. I stället för ett hårdkodat könamn, vill du ange namnet på en post i **appSettings** -samlingen som skulle ha det faktiska könamnet. Om **appSettings** -nyckeln är logqueue kan din funktion se ut som i följande exempel.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

**NameResolver** -klassen kan sedan hämta könamnet från **appSettings** som visas i följande exempel:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Du skickar **NameResolver** -klassen till **JobHost** -objektet som visas i följande exempel.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Obs:** Kö-, tabell-och blob-namn löses varje gång en funktion anropas, men BLOB container-namn matchas endast när programmet startas. Du kan inte ändra namnet på en BLOB-behållare medan jobbet körs.

## <a name="how-to-trigger-a-function-manually"></a>Så här utlöser du en funktion manuellt
Om du vill utlösa en funktion manuellt använder **** du anrops-eller **CallAsync** -metoden i **JobHost** -objektet och attributet **NoAutomaticTrigger** i funktionen, som du ser i följande exempel.

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

## <a name="how-to-write-logs"></a>Skriva loggar
På instrument panelen visas loggar på två platser: sidan för webb jobbet och sidan för ett visst webb jobbs anrop.

![Sidan loggar på webb sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Sidan loggar i funktions anrop](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Utdata från konsol metoder som du anropar i en funktion eller i **Main ()-** metoden visas på instrument panelens sida för webb jobbet, inte på sidan för ett visst metod anrop. Utdata från objektet TextWriter som du hämtar från en parameter i din metodsignatur visas på instrument panels sidan för ett metod anrop.

Konsolens utdata kan inte länkas till ett visst metod anrop eftersom konsolen är enkel trådad, medan många jobb funktioner kan köras samtidigt. Det är därför som SDK: n tillhandahåller varje funktions anrop med ett eget unikt logg skrivar objekt.

Om du vill skriva [spårnings loggar för program](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)använder du **Console. out** (skapar loggar som marker ATS som information) och **konsol. error** (skapar loggar som marker ATS som fel). Ett alternativ är att använda [trace eller TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), vilket ger utförliga, varnings-och kritiska nivåer utöver information och fel. Spårnings loggar för program visas i loggfiler, Azure-tabeller eller Azure-blobbar, beroende på hur du konfigurerar din Azure-webbapp. När det gäller alla konsol utdata visas de senaste 100-programloggarna också på instrument panelens sida för webb jobbet, inte sidan för ett funktions anrop.

Konsol utdata visas bara på instrument panelen om programmet körs i ett Azure-webbjobb, inte om programmet körs lokalt eller i en annan miljö.

Du kan inaktivera loggning genom att ange anslutnings strängen för instrument panelen till null. Mer information finns i [så här anger du konfigurations alternativ](#how-to-set-configuration-options).

I följande exempel visas flera olika sätt att skriva loggar:

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

I instrument panelen för WebJobs-SDK visas utdata från **** objektet TextWriter när du går till sidan för ett visst funktions anrop och väljer **Växla utdata**:

![Anrops länk](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Sidan loggar i funktions anrop](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

På instrument panelen för WebJobs-SDK visas de senaste 100 linjerna i konsolens utdata när du går till sidan för webb jobbet (inte för funktions anrop) och väljer **Växla utdata**.

![Växla utdata](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

I ett kontinuerligt webbjobb visas program loggar i/data/Jobs/Continuous/ *{webjobname}* /job_log.txt i webb program fil systemet.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

I en Azure-Blob ser program loggarna ut så här: 2014-09-26T21:01:13, information, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, Console. Write-Hello World!, 2014-09-26T21:01:13, fel, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, konsol. error-Hello World!, 2014-09-26T21 : 01:13, information, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, Console. out-Hello World!,

Och i en Azure-tabell visas **konsolen. ut** och **konsolen. fel** loggar ser ut så här:

![Informations logg i tabell](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Fel logg i tabell](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln finns kod exempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure-köer. Mer information om hur du använder Azure WebJobs och WebJobs SDK finns i [Azure WebJobs dokumentations resurser](https://go.microsoft.com/fwlink/?linkid=390226).

