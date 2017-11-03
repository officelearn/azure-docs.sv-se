---
title: Testa Azure Functions | Microsoft Docs
description: "Testa dina Azure funktioner med hjälp av Postman cURL och Node.js."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure funktioner, funktioner, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur och testning"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategier för att testa din kod i Azure Functions

Det här avsnittet visar de olika sätt att testa funktioner, inklusive användning av följande allmänna metoder:

+ HTTP-baserade verktyg som cURL, Postman och även en webbläsare för Webbaserad utlösare
+ Azure Lagringsutforskaren för att testa Azure Storage-baserade utlösare
+ Testa fliken i Azure Functions-portalen
+ Funktion som utlöses av timer
+ Testa programmet eller framework

Dessa tester metoder funktionen en HTTP-utlösare som accepterar indata via en frågesträngsparameter eller begärandetexten. Du skapar den här funktionen i det första avsnittet.

## <a name="create-a-function-for-testing"></a>Skapa en funktion för testning
För merparten av den här kursen använder vi en lite annorlunda version av mallen HttpTrigger JavaScript-funktionen som är tillgängliga när du skapar en funktion. Om du behöver hjälp med att skapa en funktion kan du granska detta [kursen](functions-create-first-azure-function.md). Välj den **HttpTrigger - JavaScript** mallen när du skapar test-funktionen i den [Azure-portalen].

Standardmall för funktionen är i princip en ”hello world”-funktion som ekon tillbaka namnet på begäranparametern brödtext eller fråga sträng `name=<your name>`.  Vi ska uppdatera koden för att även kan du ange namnet och en adress som JSON-innehåll i begärandetexten. Funktionen ekon sedan dessa tillbaka till klienten när det är tillgängligt.   

Uppdatera funktionen med följande kod, som ska användas för testning:

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
Det finns olika verktyg som du kan använda för att utlösa dina funktioner för att testa utanför Azure-portalen. Dessa inkluderar HTTP testning verktyg (både UI-baserade och kommandot rad), verktyg för Azure Storage-åtkomst och även en enkel webbläsare.

### <a name="test-with-a-browser"></a>Testa med en webbläsare
Webbläsaren är ett enkelt sätt att utlösaren funktioner via HTTP. Du kan använda en webbläsare för GET-begäranden som inte kräver en brödtext nyttolast och att använda endast fråga strängparametrar.

Om du vill testa funktionen vi definierade tidigare, kopiera den **funktions-Url** från portalen. Det har följande format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Lägg till den `name` parameter för frågesträngen. Använda ett faktiska namn för den `<Enter a name here>` platshållare.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Klistra in Webbadressen i webbläsaren och du bör få ett svar liknar följande.

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
Rekommenderade verktyg för att testa de flesta av dina funktioner är Postman som integreras med webbläsaren Chrome. Om du vill installera Postman [hämta Postman](https://www.getpostman.com/). Postman ger kontroll över många fler attribut för en HTTP-begäran.

> [!TIP]
> Använda testning verktyg som du är mest bekväm med HTTP. Här följer några alternativ till Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

Testa funktionen med en begärantext i Postman:

1. Starta Postman från den **appar** i det övre vänstra hörnet av Chrome-webbläsarfönstret.
2. Kopiera ditt **funktions-Url**, och klistra in den i Postman. Den omfattar åtkomst kod frågesträngparametern.
3. HTTP-metod för att ändra **efter**.
4. Klicka på **brödtext** > **raw**, och Lägg till en JSON-begärantext som liknar följande:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Klicka på **skicka**.

Följande bild visar testning enkla echo funktionen exempel i den här självstudiekursen.

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
Ofta när du testar programmet, är det inte nödvändigt att söka alla ytterligare kommandoraden för att felsöka programmet. Detta är inte annorlunda med testning funktioner. Observera att cURL är tillgängliga som standard på Linux-baserade system. I Windows, måste du först hämta och installera den [cURL verktyget](https://curl.haxx.se/).

Om du vill testa funktionen som vi definierade tidigare, kopiera den **funktions-URL** från portalen. Det har följande format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Det här är URL: en för att utlösa funktionen. Testa detta med hjälp av kommandot cURL på kommandoraden för att GET (`-G` eller `--get`) begäran mot funktionen:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Det här exemplet kräver en frågesträngsparameter som kan skickas som Data (`-d`) i cURL-kommando:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Kör kommandot och du ser i följande utdata för funktionen på kommandoraden:

![Skärmbild av kommandotolk-utdata](./media/functions-test-a-function/curl-test.png)

I portalen **loggar** fönstret utdata liknar följande loggas i funktionen körs:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testa en blob-utlösare med Lagringsutforskaren
Du kan testa en utlösare blob-funktion med hjälp av [Azure Lagringsutforskaren](http://storageexplorer.com/).

1. I den [Azure-portalen] för funktionen appen, skapa en C#, F # eller JavaScript blob Utlösarfunktion. Ange sökvägen till övervaka till namnet på blob-behållare. Exempel:

        files
2. Klicka på den  **+**  för att välja eller skapa lagringskonto som du vill använda. Klicka sedan på **Skapa**.
3. Skapa en textfil med följande text och spara den:

        A text file for blob trigger function testing.
4. Kör [Azure Lagringsutforskaren](http://storageexplorer.com/), och ansluta till blob-behållaren i storage-konto som övervakas.
5. Klicka på **överför** att ladda upp filen.

    ![Skärmbild av Lagringsutforskaren](./media/functions-test-a-function/azure-storage-explorer-test.png)

Standard-blob utlösaren Funktionskoden rapporter bearbetningen av blob i loggarna:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testa en funktion i funktioner
Azure Functions portal är utformat så att du kan testa HTTP och timer som utlöste funktioner. Du kan också skapa funktioner för att utlösa andra funktioner som du vill testa.

### <a name="test-with-the-functions-portal-run-button"></a>Testa med knappen funktioner portal kör
Portalen innehåller en **kör** knapp som du kan använda för att göra vissa begränsade testning. Du kan ange en begärantext med hjälp av knappen, men du kan inte ange frågeparametrar sträng eller uppdaterar huvuden för begäran.

Testa funktionen http-utlösaren vi skapade tidigare genom att lägga till en JSON-sträng som liknar följande i den **Begärandetext** fältet. Klicka på den **kör** knappen.

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
Vissa funktioner går inte att kunna testa med de verktyg som tidigare nämnts. Anta till exempel att en kö Utlösarfunktion som körs när ett meddelande har släppts i [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Du kan alltid skriva kod för att ta bort ett meddelande i kön och ett exempel på detta i konsolen projekt tillhandahålls nedan. Det finns en annan metod som du kan använda som testar funktioner direkt.  

Du kan använda en timer som utlösare konfigurerats med en kö utdatabindning. Timer utlösaren koden kan sedan skriva testmeddelanden till kön. Det här avsnittet går igenom ett exempel.

Mer detaljerad information om hur du använder bindningar med Azure Functions, finns det [Azure Functions för utvecklare](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Skapa en kö utlösare för testning
Om du vill visa den här metoden vi först skapa en kö Utlösarfunktion som vi vill testa för en kö med namnet `queue-newusers`. Den här funktionen bearbetas namn och adress information släppa i Queue storage för en ny användare.

> [!NOTE]
> Om du använder en annan kö, kontrollera att det namn som du använder överensstämmer med den [namngivning av köer och MetaData](https://msdn.microsoft.com/library/dd179349.aspx) regler. Annars får du ett felmeddelande.
>
>

1. I den [Azure-portalen] appen funktionen klickar du på **nya funktionen** > **QueueTrigger - C#**.
2. Ange könamnet som ska övervakas av funktionen kö:

        queue-newusers
3. Klicka på den  **+**  för att välja eller skapa lagringskonto som du vill använda. Klicka sedan på **Skapa**.
4. Lämna den här portalen webbläsarfönster öppen, så att du kan övervaka loggposter för standard kön Funktionskoden mallen.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Skapa en timer som utlösare för att ta bort ett meddelande i kön
1. Öppna den [Azure-portalen] i ett nytt webbläsarfönster och navigera till appen funktion.
2. Klicka på **nya funktionen** > **TimerTrigger - C#**. Ange ett cron-uttryck för att ange hur ofta timer koden testar kö-funktionen. Klicka sedan på **Skapa**. Om du vill testa att köra med 30 sekunders mellanrum, kan du använda följande [CRON-uttryck](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Klicka på den **integrera** för din nya timer som utlösare.
4. Under **utdata**, klickar du på **+ nya utdata**. Klicka på **kön** och **Välj**.
5. Obs det namn som du använder för den **meddelandet köobjekt**. Du kan använda den i Funktionskoden timer.

        myQueue
6. Ange namnet på kön var meddelandet ska skickas:

        queue-newusers
7. Klicka på den  **+**  för att välja lagringskontot som du tidigare använt med kö utlösaren. Klicka sedan på **Spara**.
8. Klicka på den **utveckla** för din timer som utlösare.
9. Du kan använda följande kod för C# timer-funktionen, så länge som du använde för samma meddelande objektet könamnet visades tidigare. Klicka sedan på **Spara**.

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

C# timer funktionen Kör nu med 30 sekunders mellanrum om du använde exempel cron-uttryck. Loggar för funktionen timer rapportera varje körning:

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
Du kan behöva skapa ett externt program eller framework om du vill testa dina funktioner.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testa en HTTP-Utlösarfunktion med kod: Node.js
Du kan använda en Node.js-app för att köra en HTTP-begäran för att testa funktionen.
Se till att ange:

* Den `host` i alternativen för begäran att funktionen app värden.
* Din funktionsnamnet i den `path`.
* Koden åtkomst (`<your code>`) i den `path`.

Exempel:

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


### <a name="test-a-queue-trigger-function-with-code-c"></a>Testa en kö Utlösarfunktion med koden: C# #
Vi nämnt tidigare kan du testa en utlösare för kön med kod för att ta bort ett meddelande i kön. Följande exempelkod baseras på C#-koden som visas i den [komma igång med Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) kursen. Koden för andra språk är också tillgänglig från den länken.

Om du vill testa den här koden i en konsolapp, måste du:

* [Konfigurera anslutningssträngen för lagring i filen app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Skicka en `name` och `address` som parametrar till appen. Till exempel `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Den här koden accepterar namn och adress för en ny användare som kommandoradsargument under körningen.)

C#-exempelkod:

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

[Azure-portalen]: https://portal.azure.com
