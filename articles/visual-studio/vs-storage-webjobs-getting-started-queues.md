---
title: Komma igång med queue storage- och Visual Studio anslutna tjänster (Webbjobb projekt) | Microsoft Docs
description: Hur du kommer igång med Azure Queue storage i ett Webbjobb projekt efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna tjänster.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 332d682147ba832f631052d8348039f74b46c438
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Komma igång med Azure Queue storage och Visual Studio anslutna tjänster (Webbjobb projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur komma igång med Azure Queue storage i ett projekt i Visual Studio Azure Webjobs när du har skapat eller refererar till ett Azure storage-konto med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan. När du lägger till ett lagringskonto till ett Webbjobb-projekt med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan lämplig Azure Storage NuGet-paket som är installerade, lämpliga .NET referenserna har lagts till i projektet, och anslutningssträngar för storage-konto har uppdaterats i filen App.config.  

Den här artikeln innehåller C#-kodexempel som visar hur du använder Azure WebJobs SDK version 1.x med tjänsten Azure Queue storage.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Se [Kom igång med Azure Queue Storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) för mer information. Läs mer om ASP.NET [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Hur du utlöser en funktion när ett kömeddelande tas emot
Om du vill skriva en funktion som WebJobs SDK anropar när ett kömeddelande tas emot, använder den **QueueTrigger** attribut. Attributkonstruktorn använder en strängparameter som anger namnet på kön avsöker. Om du vill se hur du ställer in könamnet dynamiskt kolla [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Sträng Kömeddelanden
I följande exempel kön innehåller ett strängmeddelande, så **QueueTrigger** tillämpas på en som strängparameter med namnet **logMessage** som innehåller innehållet i kön meddelandet. Funktionen [skriver ett loggmeddelande till instrumentpanelen](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Förutom **sträng**, parametern kan vara en bytematris en **CloudQueueMessage** objekt eller en POCO som du definierar.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
I följande exempel innehåller kömeddelandet JSON för en **BlobInformation** objekt som innehåller en **BlobName** egenskapen. SDK deserializes automatiskt objektet.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

SDK använder den [Newtonsoft.Json NuGet-paketet](http://www.nuget.org/packages/Newtonsoft.Json) serialisera och deserialisera meddelanden. Om du skapar meddelanden i kö i ett program som inte använder WebJobs SDK kan du skriva kod som i följande exempel för att skapa ett kömeddelande för POCO som SDK kan parsa.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Async-funktion
Följande async-funktion [skriver en logg till instrumentpanelen](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Asynkrona funktioner kan ta en [annullering token](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)som visas i följande exempel som kopierar en blob. (En förklaring av den **queueTrigger** platshållare, finns det [Blobbar](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) avsnitt.)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>Attributet QueueTrigger fungerar med typer
Du kan använda **QueueTrigger** med följande typer:

* **Sträng**
* En POCO-typen som serialiseras som JSON
* **byte]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Avsökningen algoritm
SDK implementerar en exponentiell tillbaka av algoritmen för att minska effekten av inaktiv-kön avsökning på transaktion lagringskostnader.  När ett meddelande hittas SDK väntar två sekunder och söker efter en annan message; När det finns inget meddelande väntar på fyra sekunder innan du försöker igen. Väntetiden fortsätter att öka tills väntetiden, där standardinställningen är en minut efter efterföljande misslyckade försök att få ett meddelande i kön. [Väntetiden kan konfigureras](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Flera instanser
Om ditt webbprogram som körs på flera instanser, en kontinuerliga Webbjobb som körs på varje dator. varje dator ska vänta tills utlösare och försök att köra funktioner I vissa scenarier som detta kan leda till vissa funktioner som bearbetar samma data två gånger så funktioner ska vara idempotent (skrivs så att anropas flera gånger med samma inflödesdata inte ger dubbla resultat).  

## <a name="parallel-execution"></a>Parallell körning
Om du har flera funktioner som lyssnar på olika köer anropar SDK dem parallellt när meddelanden tas emot samtidigt.

Samma sak gäller när flera meddelanden tas emot för en enskild kö. Som standard SDK hämtar en batch med 16 Kömeddelanden i taget och kör den funktion som behandlar dem parallellt. [Batchstorleken konfigureras](#how-to-set-configuration-options). När antalet bearbetas hämtar till hälften av batchstorleken, hämtar en annan batch SDK och påbörjar bearbetningen av dessa meddelanden. Därför är det maximala antalet samtidiga meddelanden som bearbetas per funktion en och en halv gånger batchstorlek. Den här begränsningen gäller separat för varje funktion som har en **QueueTrigger** attribut. Om du inte vill parallell körning för meddelanden som tas emot i en kö, Ange batchstorleken 1.

## <a name="get-queue-or-queue-message-metadata"></a>Hämta kön eller kön meddelandet metadata
Du kan få följande meddelandeegenskaper genom att lägga till parametrar Metodsignaturen:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **strängen** queueTrigger (innehåller meddelandetext)
* **strängen** id
* **strängen** popReceipt
* **int** dequeueCount

Om du vill arbeta direkt med Azure storage API, du kan också lägga till en **CloudStorageAccount** parameter.

I följande exempel skriver alla dessa metadata till en INFO programloggen. Både logMessage och queueTrigger innehåller innehållet i kön meddelandet i det här exemplet.

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

## <a name="graceful-shutdown"></a>Korrekt avslutning
En funktion som körs i ett kontinuerligt Webbjobb kan acceptera en **CancellationToken** parameter som gör operativsystemet för att meddela funktionen när Webbjobbet håller på att avslutas. Du kan använda det här meddelandet för att kontrollera att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du kontrollerar för nära förestående Webbjobb avslutning i en funktion.

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

**Obs:** instrumentpanelen kanske inte korrekt visar status och utdata för funktioner som har stängts av.

Mer information finns i [WebJobs korrekt avslutning](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Så här skapar du ett kömeddelande under bearbetning av ett meddelande i kön
Om du vill skriva en funktion som skapar ett nytt meddelande i kön, använder den **kön** attribut. Som **QueueTrigger**kan du skicka in könamnet som en sträng eller [ange könamnet dynamiskt](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Sträng Kömeddelanden
Följande kodexempel icke asynkron skapar ett nytt meddelande i kön i kön med namnet ”outputqueue” med samma innehåll som kön meddelandet som togs emot i kön med namnet ”inputqueue”. (För asynkrona funktioner använder **IAsyncCollector<T>**  enligt senare i det här avsnittet.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
Skicka POCO-typ för att skapa ett meddelande i kön som innehåller en POCO i stället för en sträng som en utdataparameter till den **kön** Attributkonstruktorn.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

SDK Serialiserar automatiskt objektet till JSON. Ett kömeddelande skapas alltid, även om objektet är null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Skapa flera meddelanden eller i async-funktioner
Om du vill skapa flera meddelanden gör parametertypen för utgående kö **ICollector<T>**  eller **IAsyncCollector<T>** som visas i följande exempel.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Varje meddelande i kön skapas omedelbart när den **Lägg till** -metoden anropas.

### <a name="types-that-the-queue-attribute-works-with"></a>Typer som attributet kön fungerar med
Du kan använda den **kön** attributet på följande parameter:

* **i strängen** (skapar kömeddelande om parametervärdet är icke-null när funktionen avslutas)
* **ut byte []** (fungerar som **sträng**)
* **ut CloudQueueMessage** (fungerar som **sträng**)
* **ut POCO** (en serialiserbar typ. skapar ett meddelande med ett null-objekt om parametern är null när funktionen avslutas)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (för att skapa meddelanden manuellt med hjälp av Azure Storage-API direkt)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Använda WebJobs-SDK-attribut i brödtexten för en funktion
Om du behöver göra några resurser i din funktion innan du använder ett WebJobs-SDK-attribut som **kön**, **Blob**, eller **tabell**, du kan använda den **IBinder**gränssnitt.

I följande exempel tar ett inkommande kö-meddelande och skapar ett nytt meddelande med samma innehåll i en utgående kö. Könamnet utdata anges av koden i själva funktionen.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

Den **IBinder** gränssnitt kan även användas med den **tabell** och **Blob** attribut.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Hur kan läsa och skriva BLOB och tabeller under bearbetning av ett meddelande i kön
Den **Blob** och **tabell** attribut kan du läsa och skriva BLOB och tabeller. Exemplen i det här avsnittet gäller för blobbar. Kodexempel som visar hur du utlöser processer när blobbar har skapats eller uppdaterats, se [använda Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Sträng Kömeddelanden utlösa blob-åtgärder
För ett meddelande i kön som innehåller en sträng, **queueTrigger** är en platshållare som du kan använda i den **Blob** attributets **blobPath** parametrar som innehåller innehållet i den meddelande.

I följande exempel används **dataströmmen** objekt att läsa och skriva BLOB. Kömeddelandet är namnet på en blob som finns i behållaren textblobs. En kopia av blobben med ”-nya” läggs till namnet skapas i samma behållare.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Den **Blob** attributet konstruktorn tar en **blobPath** parameter som anger namnet på behållaren och blob. Mer information om den här platshållaren finns [använda Azure blob storage med WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

När attributet decorates en **dataströmmen** objekt, en annan konstruktorparametern anger det **FileAccess** läge som Läs-, Skriv- eller läsning och skrivning.

I följande exempel används en **CloudBlockBlob** objekt att ta bort en blob. Kömeddelandet är namnet på blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(vanlig gamla CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) meddelanden i kö
För en POCO lagras som JSON i kön meddelandet, kan du använda platshållare som namn egenskaperna för objektet i den **kön** attributets **blobPath** parameter. Du kan också använda kön metadata egenskapsnamn som platshållare. Se [hämta kön eller kön meddelandet metadata](#get-queue-or-queue-message-metadata).

I följande exempel kopierar en blobb till en ny blob med ett annat tillägg. Kön meddelandet är ett **BlobInformation** -objekt som innehåller **BlobName** och **BlobNameWithoutExtension** egenskaper. Egenskapsnamnen används som platshållare i blobbsökvägen för den **Blob** attribut.

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

SDK använder den [Newtonsoft.Json NuGet-paketet](http://www.nuget.org/packages/Newtonsoft.Json) serialisera och deserialisera meddelanden. Om du skapar meddelanden i kö i ett program som inte använder WebJobs SDK kan du skriva kod som i följande exempel för att skapa ett kömeddelande för POCO som SDK kan parsa.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Om du behöver göra vissa arbetet i din funktion innan du binder en blobb till ett objekt du kan använda attributet i brödtexten i funktion, som visas i [använder WebJobs-SDK-attribut i brödtexten för en funktion](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Du kan använda Blob-attribut med
Den **Blob** attributet kan användas med följande typer:

* **Dataströmmen** (läsa eller skriva som anges med hjälp av parametern FileAccess konstruktor)
* **TextReader**
* **TextWriter**
* **strängen** (läsa)
* **i strängen** (skriva; skapar en blob endast om strängparametern är icke-null när returnerar funktionen)
* POCO (läsa)
* ut POCO (skriva; alltid skapar en blob, skapar som null-objekt om POCO-parametern är null när returnerar funktionen)
* **CloudBlobStream** (Skriv)
* **ICloudBlob** (läsa eller skriva)
* **CloudBlockBlob** (läsa eller skriva)
* **CloudPageBlob** (läsa eller skriva)

## <a name="how-to-handle-poison-messages"></a>Hur du hanterar förgiftade meddelanden
Meddelanden vars innehåll gör en funktionen misslyckas kallas *förgiftade meddelanden*. När funktionen misslyckas, tas inte bort kömeddelandet och slutligen hämtas igen och orsakar cykeln ska upprepas. SDK kan avbryta cykeln efter ett begränsat antal upprepningar eller så kan du göra det manuellt.

### <a name="automatic-poison-message-handling"></a>Hantering av automatisk skadligt meddelande
SDK: N ska anropa en funktion upp till 5 gånger för att bearbeta ett meddelande i kön. Om den femte försök misslyckas, flyttas meddelandet till en skadligt kö. Du kan se hur du konfigurerar det maximala antalet försök i [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

Skadligt kön heter *{originalqueuename}*-skadligt. Du kan skriva en funktion för att bearbeta meddelanden från skadligt kön av loggning av dem eller skicka ett meddelande till den manuella åtgärder krävs.

I följande exempel på **CopyBlob** funktionen kommer att misslyckas när ett kömeddelande innehåller namnet på en blob som inte finns. När det händer kan flyttas meddelandet från kön copyblobqueue till copyblobqueue poison kön. Den **ProcessPoisonMessage** loggar skadligt meddelande.

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

Följande bild visar konsolens utdata från de här funktionerna när ett skadligt meddelande bearbetas.

![Konsolens utdata för hantering av skadligt meddelande](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Hantering av manuell skadligt meddelande
Du kan hämta antalet gånger som ett meddelande har plockats för bearbetning genom att lägga till en **int** parameter med namnet **dequeueCount** till funktionen. Du kan kontrollera antalet dequeue i Funktionskoden och utföra egna skadligt meddelandehantering när antalet överskrider ett tröskelvärde som visas i följande exempel.

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

## <a name="how-to-set-configuration-options"></a>Hur du ställer in konfigurationsalternativ
Du kan använda den **JobHostConfiguration** typ för att ange följande konfigurationsalternativ:

* Ange anslutningssträngar SDK i koden.
* Konfigurera **QueueTrigger** inställningar, till exempel maximalt antal har status Created.
* Hämta könamn från konfigurationen.

### <a name="set-sdk-connection-strings-in-code"></a>Ange SDK anslutningssträngar i kod
Ställa in anslutningssträngar SDK i koden kan du använda din egen anslutning sträng namn i konfigurationsfiler eller miljövariabler som visas i följande exempel.

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

### <a name="configure-queuetrigger--settings"></a>Konfigurera inställningar för QueueTrigger
Du kan konfigurera följande inställningar som gäller för meddelandebehandling kö:

* Det maximala antalet Kömeddelanden som fångas upp samtidigt kan köras parallellt (standard är 16).
* Det maximala antalet försök innan ett kömeddelande skickas till ett skadligt kö (standardvärdet är 5).
* Maximal väntetid innan avsökning igen när en kö är tomt (standard är 1 minut).

I följande exempel visas hur du konfigurerar dessa inställningar:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ange värden för WebJobs SDK konstruktorparametrarna i koden
Du vill ibland ange en ny kö, ett blob-namn eller en behållare eller en tabell i stället för att hårdkoda ge den namnet. Du kan till exempel vill ange namnet på kön för **QueueTrigger** i en konfiguration av fil- eller miljö variabel.

Du kan göra det genom att passera i en **NameResolver** objekt till den **JobHostConfiguration** typen. Du inkludera särskilda platshållare omges av procenttecken (%) i WebJobs SDK attributet konstruktorn parametrar och **NameResolver** koden anger de faktiska värdena som ska användas i stället för dessa platshållare.

Anta att du vill använda en kö med namnet logqueuetest i testmiljön och en namngiven logqueueprod i produktion. I stället för en hårdkodad kö du vill ange namnet på en post i den **appSettings** samling som skulle ha faktiska könamnet. Om den **appSettings** nyckeln är logqueue, din funktion kan se ut som följande exempel.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Din **NameResolver** klassen kan sedan hämta könamnet från **appSettings** som visas i följande exempel:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Du skickar den **NameResolver** klassen i att den **JobHost** objekt som visas i följande exempel.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Obs:** kön tabell och blobbnamnen är löst varje gång som en funktion kallas men blob-behållaren namnmatchning bara när programmet startas. Du kan inte ändra namnet för blob-behållare medan jobbet körs.

## <a name="how-to-trigger-a-function-manually"></a>Hur du utlöser en funktion manuellt
Utlös en funktion manuellt genom att använda den **anropa** eller **CallAsync** -metoden i den **JobHost** objekt och **NoAutomaticTrigger** attribut i funktionen som visas i följande exempel.

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

## <a name="how-to-write-logs"></a>Hur du skriver loggar
Instrumentpanelen visar loggar på två platser: sidan för Webbjobbet, och på sidan för ett särskilt Webbjobb-anrop.

![Loggar Webbjobb på sidan](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Loggar i funktionen anrops-sida](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Utdata från konsolen metoder som anropas i en funktion eller i den **Main()** metoden visas på sidan instrumentpanelen för Webbjobbet, inte på sidan för en viss metodanropet. Utdata från TextWriter-objekt som du får från en parameter i Metodsignaturen visas på sidan instrumentpanelen för ett metodanrop.

Konsolens utdata kan inte länkas till en viss metodanropet eftersom konsolen är enkeltrådad, men många jobbfunktioner kan köras samtidigt. Det är därför SDK innehåller varje funktionsanrop med sin egen unika loggen skrivarobjekt.

Att skriva [spårning programloggarna](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), använda **Console.Out** (skapar loggar som är märkta som INFO) och **Console.Error** (skapar loggar som är märkta som fel). Ett alternativ är att använda [spårningen eller TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), som innehåller utförlig, varning, och kritiska nivåer utöver Info och fel. Spårningsloggar för programmet visas i web app loggfilerna Azure-tabeller eller Azure BLOB-objekt beroende på hur du konfigurerar din Azure-webbapp. Som gäller för alla konsolens utdata, visas senaste 100 programloggarna även på sidan instrumentpanelen för Webbjobb, inte sidan för ett funktionsanrop.

Konsolens utdata visas i instrumentpanelen bara om programmet körs i en Azure-Webbjobb inte om programmet körs lokalt eller i en annan miljö.

Du kan inaktivera loggning genom att ange anslutningssträngen instrumentpanelen till null. Mer information finns i [hur du ställer in konfigurationsalternativ](#how-to-set-configuration-options).

I följande exempel visar flera olika sätt att skriva loggar:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

I WebJobs SDK instrumentpanelen för utdata från den **TextWriter** objekt ser ut när du gå till sidan för en viss fungera anrop och välj **växla utdata**:

![Anrops-länk](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Loggar i funktionen anrops-sida](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

I instrumentpanelen för WebJobs SDK senaste 100 raderna i konsolen utdata visar in när du gå till sidan för Webbjobb (inte för funktionsanrop) och välj **växla utdata**.

![Växla utdata](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

I ett kontinuerligt Webbjobb programloggarna visas i/data/jobb/kontinuerlig/*{webjobname}*/job_log.txt i filsystemet web app.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

I ett Azure blob-program loggar ser ut så här: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13, fel, contosoadsnew, 491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Och i en Azure-tabellen i **Console.Out** och **Console.Error** loggar ut så här:

![Loggen för information i tabellen](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Felloggen i tabellen](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln har lämnat kodexempel som visar hur du hanterar vanliga scenarier för att arbeta med Azure köer. Mer information om hur du använder Azure WebJobs och WebJobs-SDK finns [Azure WebJobs-dokumentation](http://go.microsoft.com/fwlink/?linkid=390226).

