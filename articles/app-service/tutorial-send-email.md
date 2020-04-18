---
title: 'Självstudiekurs: skicka e-post med Logic Apps'
description: Läs om hur du anropar affärsprocesser från apptjänstappen. Skicka e-post, tweets och Facebook-inlägg, lägg till i e-postlistor och mycket mer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 4073b49a134356943bd7da8d54bf574f2e0d5eea
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604855"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Självstudiekurs: Skicka e-post och anropa andra affärsprocesser från App Service

I den här självstudien får du lära dig hur du integrerar appen App Service med dina affärsprocesser. Detta är vanligt för webbappscenarier, till exempel:

- Skicka bekräftelsemeddelande för en transaktion
- Lägga till användare i Facebook-grupp
- Anslut till tredjepartssystem som SAP, SalesForce, etc.
- Exchange-standard-B2B-meddelanden

I den här självstudien skickar du e-postmeddelanden med Gmail från apptjänstappen med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Det finns andra sätt att skicka e-postmeddelanden från en webbapp, till exempel SMTP-konfiguration som tillhandahålls av språkramverket. Logic Apps ger dock mycket mer kraft till apptjänstappen utan att lägga till komplexitet i koden. Logic Apps tillhandahåller ett enkelt konfigurationsgränssnitt för de mest populära affärsintegrationerna, och din app kan anropa dem när som helst med en HTTP-begäran.

## <a name="prerequisite"></a>Krav

Distribuera en app med det språkramverk du väljer till App Service. Information om hur du följer en självstudiekurs för att distribuera en exempelapp finns nedan:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Självstudiekurs: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Självstudiekurs: Skapa en Node.js- och MongoDB-app i Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Självstudiekurs: Skapa en PHP- och MySQL-app i Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Självstudiekurs: Kör en Python-webbapp (Django) med PostgreSQL i Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Skapa en Ruby- och Postgres-app i Azure App Service på Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Skapa logikappen

1. Skapa en tom logikapp i [Azure-portalen](https://portal.azure.com)genom att följa instruktionerna i [Skapa logikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). När du ser **Logic Apps Designer**går du tillbaka till den här självstudien.
1. På välkomstsidan för Logic Apps Designer väljer du **När en HTTP-begäran tas emot** under Start med en vanlig **utlösare**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. Välj **Använd exempelnyttolast i**dialogrutan för **När en HTTP-begäran tas emot**för att generera schema .

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopiera följande exempel JSON till textrutan och välj **Klar**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat genereras nu för de begärandedata du vill ha. I praktiken kan du bara samla in faktiska begärandedata som programkoden genererar och låta Azure generera JSON-schemat åt dig. 
1. Högst upp i Logic Apps Designer väljer du **Spara**. 

    Du kan nu se URL:en för utlösaren för HTTP-begäran. Välj kopieringsikonen om du vill kopiera den för senare användning.

    ![](./media/tutorial-send-email/http-request-url.png)

    Denna HTTP-begäran definition är en utlösare till allt du vill göra i denna logik app, vare sig det Gmail eller något annat. Senare anropar du den här webbadressen i apptjänstappen. Mer information om utlösaren för begäran finns i [HTTP-begäran/svarsreferensen](../connectors/connectors-native-reqres.md).

1. Längst ned i designern klickar du på **Nytt steg**, skriver **Gmail** i sökrutan för åtgärder och letar reda på och väljer **Skicka e-post (V2)**.
    
    > [!TIP]
    > Du kan söka efter andra typer av integrationer, till exempel SendGrid, MailChimp, Office 365 och SalesForce. Mer information finns i [Logic Apps-dokumentationen](https://docs.microsoft.com/azure/logic-apps/).
1. I dialogrutan **Gmail** väljer du **Logga in** och logga in på det Gmail-konto som du vill skicka e-postmeddelandet från.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. När du har loggat in klickar du i textrutan **Till** och dialogrutan dynamiskt innehåll öppnas automatiskt.

1. Bredvid åtgärden **När en HTTP-begäran tas emot** väljer du Visa **mer**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Du bör nu se de tre egenskaperna från dina exempel-JSON-data som du använde tidigare. I det här steget använder du dessa egenskaper från HTTP-begäran för att skapa ett e-postmeddelande.
1. Eftersom du väljer värdet för fältet **Till** väljer du **e-post**. Om du vill kan du växla från dialogrutan dynamiskt innehåll genom att klicka på **Lägg till dynamiskt innehåll**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. I listrutan **Lägg till ny parameter** väljer du **Ämne** och **Brödtext**.

1. Klicka i textrutan **Ämne** och välj på samma sätt **uppgift**. Skriv *skapad*med markören kvar i rutan **Ämne** . 

1. Klicka i **brödtexten**och välj på samma sätt **förfalla**. Flytta markören till vänster om **förfallna** och skriv *Det här arbetsobjektet förfaller på*.

    > [!TIP]
    > Om du vill redigera HTML-innehåll direkt i e-posttexten väljer du **Kodvyn** högst upp i fönstret Logic Apps Designer. Se bara till att du bevarar `@{triggerBody()?['due']}`den dynamiska innehållskoden (till exempel)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Lägg sedan till ett asynkront HTTP-svar på HTTP-utlösaren. Mellan HTTP-utlösaren och Gmail-åtgärden klickar du på **+** tecknet och väljer Lägg till en parallell **gren**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Sök efter **svar**i sökrutan och välj sedan åtgärden **Svar.**

    ![](./media/tutorial-send-email/choose-response-action.png)

    Som standard skickar svarsåtgärden en HTTP 200. Det är bra nog för den här guiden. Mer information finns i [HTTP-begäran/svarsreferensen](../connectors/connectors-native-reqres.md).

1. Välj **Spara** igen högst upp i Logic Apps Designer. 

## <a name="add-http-request-code-to-app"></a>Lägga till HTTP-begärandekod i appen

Kontrollera att du har kopierat URL:en för HTTP-begäran från tidigare. Eftersom den innehåller känslig information är det bäst att du inte placerar den direkt i koden. Med App Service kan du referera till den som en miljövariabel i stället med hjälp av appinställningar. 

Skapa appinställningen med följande kommando i [Cloud Shell](https://shell.azure.com)(ersätt * \<appnamn>, * * \<>för resursgrupp-namn och * * \<logik-app-url->*):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

I koden gör du ett standard-HTTP-inlägg till url:en med valfri HTTP-klientspråk som är tillgängligt för språkramverket, med följande konfiguration:

- Den begärande brödtexten innehåller samma JSON-format som du angav till logikappen:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Begäran innehåller rubriken `Content-Type: application/json`. 
- Om du vill optimera prestanda skickar du begäran asynkront om möjligt.

Klicka på fliken Önskat språk/ramverk nedan för att se ett exempel.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

I ASP.NET kan du skicka HTTP-inlägget med klassen [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Ett exempel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Om du testar den här koden i exempelappen för [självstudiekurs: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)kan du använda den `Todo` för att skicka en e-postbekräftelse i åtgärden [Skapa](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)när objektet har lagts till. Om du vill använda den asynkrona koden ovan konverterar du åtgärden Skapa till asynkron.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

I ASP.NET Core kan du skicka HTTP-inlägget med klassen [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Ett exempel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Denna kod är skriven för enkelheten i demonstration. I praktiken ska du inte `HttpClient` instansiera ett objekt för varje begäran. Följ anvisningarna på [Använd IHttpClientFactory för att implementera fjädrande HTTP-begäranden](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Om du testar den här koden i exempelappen för [självstudiekurs: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)kan du använda den `Todo` för att skicka en e-postbekräftelse i åtgärden [Skapa](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)när objektet har lagts till.

# <a name="nodejs"></a>[Node.js](#tab/node)

I Node.js kan du enkelt skicka HTTP-inlägget med ett npm-paket som [axios](https://www.npmjs.com/package/axios). Ett exempel:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Om du testar den här koden i exempelappen för [självstudiekurs: Skapa en nod.js och MongoDB-app i Azure](app-service-web-tutorial-nodejs-mongodb-app.md)kan du använda den för att skicka en e-postbekräftelse i [skapa-funktionen](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)när [artikeln har sparats](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

I PHP kan du skicka HTTP-inlägget enkelt med [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Ett exempel:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Om du testar den här koden i exempelappen för [självstudiekurs: Skapa en PHP- och MySQL-app i Azure](app-service-web-tutorial-php-mysql.md)kan du använda den för att skicka en e-postbekräftelse i [funktionen Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), strax före retursatsen.

# <a name="python"></a>[Python](#tab/python)

I Python kan du enkelt skicka HTTP-inlägget med [begäranden](https://pypi.org/project/requests/). Ett exempel:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Om du testar den här koden i exempelappen för [självstudiekurs: Kör en Python-webbapp (Django) med PostgreSQL i Azure App Service](containers/tutorial-python-postgresql-app.md)kan du använda den för att skicka en e-postbekräftelse i funktionen [Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), strax före retursatsen.

# <a name="ruby"></a>[Ruby](#tab/ruby)

I Ruby kan du enkelt skicka HTTP-inlägget med [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Ett exempel:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Om du testar den här koden i exempelappen för [Build a Ruby och Postgres-appen i Azure App Service på Linux](containers/tutorial-ruby-postgres-app.md)kan du använda den för att skicka en e-postbekräftelse i [skapa-åtgärden](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [ @task.save när den lyckas](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Fler resurser

[Självstudie: Vara värd för en RESTful-API med CORS i Azure App Service](app-service-web-tutorial-rest-api.md)  
[HTTP-begäran/svarsreferens för Logic Apps](../connectors/connectors-native-reqres.md)  
[Snabbstart: Skapa ditt första arbetsflöde med hjälp av Azure Logic Apps - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)