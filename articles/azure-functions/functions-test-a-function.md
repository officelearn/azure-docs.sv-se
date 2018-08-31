---
title: Testa Azure Functions | Microsoft Docs
description: Testa din Azure functions med hjälp av Postman, cURL och Node.js.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur testning
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a57d5f8d857a8cfcdc81e86650466aec740f41e3
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286817"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategier för att testa din kod i Azure Functions

Det här avsnittet visar olika sätt att testa functions, inklusive med hjälp av följande allmänna metoder:

+ HTTP-baserade verktyg som cURL, Postman och även en webbläsare för Webbaserad utlösare
+ Azure Storage Explorer att testa Azure Storage-baserade utlösare
+ Testa fliken i Azure Functions-portalen
+ Timerutlöst funktion
+ Testa programmet eller framework

Alla dessa metoder för testning använder en HTTP-Utlösarfunktion som godkänner indata via en frågesträngparameter eller begärandetexten. Du skapar den här funktionen med Azure-portalen i det första avsnittet.

## <a name="create-a-simple-function-for-testing-using-the-azure-portal"></a>Skapa en enkel funktion för att testa med Azure portal
För merparten av den här självstudien använder vi en något justerad version av mallen för HttpTrigger JavaScript-funktion som är tillgänglig när du skapar en funktion. Om du behöver hjälp med att skapa en funktion kan du granska det [självstudien](functions-create-first-azure-function.md). Välj den **HttpTrigger - JavaScript** mallen när du skapar testfunktion i den [Azure Portal].

Standardmall för funktionen är i grunden en ”hello world”-funktion som ekar tillbaka namnet på parametern begäran eller sträng `name=<your name>`.  Vi kommer att uppdatera koden för att också vara möjligt att ange namnet och en adress som JSON-innehåll i begärandetexten. Funktionen ekar sedan dessa tillbaka till klienten när det är tillgängligt.   

Uppdatera funktionen med följande kod, som vi använder för att testa:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Testa en funktion med verktyg
Det finns olika verktyg som du kan använda för att utlösa dina funktioner för att testa utanför Azure-portalen. Dessa inkluderar HTTP-testverktyg (både UI-baserad och kommandot rad), Azure Storage få tillgång till verktyg, och även en enkel webbläsare.

### <a name="test-with-a-browser"></a>Testa med en webbläsare
Webbläsaren är ett enkelt sätt att utlösarfunktionerna via HTTP. Du kan använda en webbläsare för GET-begäranden som inte kräver en brödtext-nyttolasten och att använda endast fråga sträng som parametrar.

Om du vill testa funktionen som vi definierade tidigare, kopiera den **Funktionswebbadress** från portalen. Den har följande format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Lägg till den `name` parameter i frågesträngen. Använda ett faktiska namn för den `<Enter a name here>` platshållare.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Klistra in URL: en i webbläsaren och du bör få svaret liknar följande.

![Skärmbild av Chrome webbläsarflik med test-svar](./media/functions-test-a-function/browser-test.png)

Det här exemplet är webbläsaren Chrome som omsluter den returnerade strängen i XML. Andra webbläsare bara strängvärde.

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testa med Postman
Det rekommenderade verktyget för att testa de flesta av dina funktioner är Postman som integreras i webbläsaren Chrome. Postman Se [hämta Postman](https://www.getpostman.com/). Postman ger kontroll över många fler attribut för en HTTP-begäran.

> [!TIP]
> Använd HTTP testning verktyg som du är mest bekväm med. Här följer några alternativ till Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

Så här testar funktionen med en begärandetext i Postman:

1. Starta Postman från den **appar** i det övre vänstra hörnet av en Chrome-webbläsarfönstret.
2. Kopiera din **Funktionswebbadress**, och klistra in den i Postman. Den innehåller kod för dataåtkomst för frågesträngparametern.
3. Ändra HTTP-metoden till **POST**.
4. Klicka på **brödtext** > **raw**, och Lägg till en JSON-begärandetext som liknar följande:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Klicka på **skicka**.

Följande bild visar testa enkel echo funktionen exemplet i den här självstudien.

![Skärmbild av Postman användargränssnitt](./media/functions-test-a-function/postman-test.png)

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Testa med cURL från kommandoraden
Ofta när du testar programvara, är det inte nödvändigt att se något mer än vad som kommandoraden för att få hjälp med felsökning av ditt program. Detta skiljer sig inte med testa functions. Observera att cURL tillgängligt som standard på Linux-baserade system. På Windows, måste du först hämta och installera den [cURL verktyget](https://curl.haxx.se/).

Om du vill testa funktionen som vi definierade tidigare, kopiera den **Funktionswebbadress** från portalen. Den har följande format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Det här är URL: en för att utlösa din funktion. Testa detta genom att använda cURL-kommando på kommandoraden för att göra en hämtning (`-G` eller `--get`)-begäran mot funktionen:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Det här exemplet kräver en frågesträngsparameter som kan skickas som Data (`-d`) i cURL-kommando:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Kör kommandot och du se följande utdata för funktionen från kommandoraden:

![Skärmbild av kommandotolk utdata](./media/functions-test-a-function/curl-test.png)

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testa en blob-utlösare med Storage Explorer
Du kan testa en funktion för blob-utlösare med hjälp av [Azure Storage Explorer](http://storageexplorer.com/).

1. I den [Azure Portal] för din funktionsapp, skapa en Utlösarfunktion för C#, F # eller JavaScript blob. Ange sökvägen till att övervaka till namnet på blob-behållare. Exempel:

        files
2. Klicka på den **+** för att välja eller skapa storage-konto som du vill använda. Klicka sedan på **Skapa**.
3. Skapa en textfil med följande text och spara den:

        A text file for blob trigger function testing.
4. Kör [Azure Storage Explorer](http://storageexplorer.com/), och Anslut till blob-behållare i lagringskontot som övervakas.
5. Klicka på **överför** att ladda upp filen.

    ![Skärmbild av Storage Explorer](./media/functions-test-a-function/azure-storage-explorer-test.png)

Standard blob-utlösare Funktionskoden rapporterar bearbetningen av blob i loggarna:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testa en funktion i functions
Azure Functions portal har utformats så att du kan testa HTTP och timerutlöst funktion. Du kan också skapa funktioner för att utlösa andra funktioner som du vill testa.

### <a name="test-with-the-functions-portal-run-button"></a>Testa med Functions-portalen kör-knappen
Portalen ger en **kör** knapp som du kan använda för att göra några begränsad testning. Du kan ange en brödtext i begäran med hjälp av knappen, men du kan inte ange parametrar för frågesträngen eller uppdatera begärandehuvuden.

Testa HTTP-Utlösarfunktion som vi skapade tidigare genom att lägga till en JSON-sträng som liknar följande i den **Begärandetext** fält. Klicka sedan på den **kör** knappen.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testa med en timer som utlösare
Vissa funktioner går inte att testa på lämpligt sätt med de verktyg som tidigare nämnts. Anta exempelvis att en kö-utlösare-funktion som körs när ett meddelande som släpps till [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Du kan alltid skriva kod för att ta bort ett meddelande till din kö och ett exempel på detta i ett konsollprojekt finns senare i den här artikeln. Det finns dock en annan metod som du kan använda som testar funktioner direkt.  

Du kan använda en timer som utlösare konfigurerats med en kö-utdatabindning. Timer utlösaren koden kan sedan skriva testmeddelanden till kön. Det här avsnittet går igenom ett exempel.

Mer detaljerad information om hur du använder bindningar med Azure Functions finns i den [Azure Functions för utvecklare](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Skapa en kö-utlösare för testning
Om du vill demonstrera den här metoden kan vi först skapa en Utlösarfunktion för kö som vi vill testa för en kö med namnet `queue-newusers`. Den här funktionen bearbetas namn och adress information som släpps till Queue storage för en ny användare.

> [!NOTE]
> Om du använder en annan könamn, kontrollera att det namn som du använder överensstämmer med den [namngivning av köer och MetaData](https://msdn.microsoft.com/library/dd179349.aspx) regler. Annars får du ett felmeddelande.
>
>

1. I den [Azure Portal] för din funktionsapp klickar du på **ny funktion** > **QueueTrigger - C#**.
2. Ange namnet på kön som ska övervakas av funktionen kö:

        queue-newusers
3. Klicka på den **+** för att välja eller skapa storage-konto som du vill använda. Klicka sedan på **Skapa**.
4. Lämna den här portalen webbläsarfönstret öppen, så att du kan övervaka vilka loggposter för standard kö Funktionskoden mall.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Skapa en timer som utlösare för att ta bort ett meddelande i kön
1. Öppna den [Azure Portal] i ett nytt webbläsarfönster och navigera till din funktionsapp.
2. Klicka på **ny funktion** > **TimerTrigger – C#**. Ange ett cron-uttryck för att ange hur ofta timer koden testar din kö-funktion. Klicka sedan på **Skapa**. Om du vill att testet ska köras med 30 sekunders mellanrum, kan du använda följande [CRON-uttryck](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Klicka på den **integrera** flik för din nya timer som utlösare.
4. Under **utdata**, klickar du på **+ nya utdata**. Klicka sedan på **kö** och **Välj**.
5. Obs det namn som du använder för den **kö meddelandeobjekt**. Du använder du i Funktionskoden timer.

        myQueue
6. Ange namnet på kön där meddelandet skickas:

        queue-newusers
7. Klicka på den **+** knappen för att välja det lagringskonto som du använde tidigare med kö-utlösare. Klicka sedan på **Spara**.
8. Klicka på den **utveckla** flik för din timer som utlösare.
9. Du kan använda följande kod för C# timer-funktion, så länge du använde samma kö meddelande objektnamnet visades tidigare. Klicka sedan på **Spara**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

Nu kör timer-funktion C# med 30 sekunders mellanrum om du använde exemplet cron-uttryck. Loggar för timer-funktion rapportera varje körning:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Du kan se varje meddelande bearbetas i webbläsarfönstret för funktionen kö:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Testa en funktion med kod
Du kan behöva skapa ett externt program eller framework för att testa dina funktioner.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testa en HTTP-Utlösarfunktion med kod: Node.js
Du kan använda en Node.js-app för att köra en HTTP-begäran för att testa din funktion.
Se till att ange:

* Den `host` i alternativen för begäran till din funktion app-värd.
* Din funktionsnamnet i den `path`.
* Kod för dataåtkomst (`<your code>`) i den `path`.

Kodexempel:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Resultat:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Testa en kö utlösningsfunktion med kod: C# #
Vi nämnde tidigare kan du testa en kö-utlösare med kod för att ta bort ett meddelande i kön. Följande exempelkod baseras på C#-koden som visas i den [komma igång med Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) självstudien. Kod för övriga språk är också tillgängligt från länken.

Om du vill testa den här koden i en konsolapp, måste du:

* [Konfigurera anslutningssträngen för lagring i filen app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Skicka en `name` och `address` som parametrar till appen. Till exempel `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Den här koden accepterar namn och adress för en ny användare som kommandoradsargument under körning.)

Exemplet C#-kod:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

Du kan se varje meddelande bearbetas i webbläsarfönstret för funktionen kö:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure Portal]: https://portal.azure.com
