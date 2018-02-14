---
title: Arbeta med proxyservrar i Azure Functions | Microsoft Docs
description: "Översikt över hur du använder Azure Functions proxyservrar"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 75b568c12fd58d5599b6878dedb6c2266b6cb649
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="work-with-azure-functions-proxies"></a>Arbeta med Azure Functions proxyservrar

Den här artikeln beskriver hur du konfigurerar och arbetar med Azure Functions proxyservrar. Med den här funktionen kan du ange slutpunkter på appen funktion som implementeras av en annan resurs. Du kan använda dessa proxyservrar för att dela en stor API i flera funktionen appar (som en mikrotjänster arkitektur) och ändå kunna erbjuda en enda API-yta för klienter.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardfunktioner fakturering gäller proxy körningar. Mer information finns i [priser för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Skapa en proxy

Det här avsnittet visar hur du skapar en proxy i Functions-portalen.

1. Öppna den [Azure-portalen], och gå sedan till din funktionsapp.
2. I den vänstra rutan, Välj **ny proxy**.
3. Ange ett namn för proxyservern.
4. Konfigurera den slutpunkt som är exponerad på den här funktionen appen genom att ange den **flödesmallen** och **HTTP-metoderna**. Dessa parametrar fungerar enligt reglerna för [http-utlösare].
5. Ange den **backend-URL** till en annan slutpunkt. Den här slutpunkten kan vara en funktion i en annan funktionsapp eller kan det bero på andra API: er. Värdet behöver inte vara statiska och kan referera till [programinställningar] och [parametrar från den ursprungliga klientbegäran].
6. Klicka på **Skapa**.

Proxyservern finns nu som en ny slutpunkt i appen funktion. Ur klienten motsvarar den en HttpTrigger i Azure Functions. Du kan testa den nya proxyserverkonfigurationen genom att kopiera URL: en för Proxy och testa med din favorit HTTP-klient.

## <a name="modify-requests-responses"></a>Ändra begäranden och -svar

Med Azure Functions-proxyservrar, kan du ändra till och -svar från serverdelen. Dessa omvandlingar kan använda variabler som definieras i [använda variabler].

### <a name="modify-backend-request"></a>Ändra backend-begäran

Som standard initieras backend-begäran som en kopia av den ursprungliga begäranden. Utöver att ställa in backend-URL: en kan du ändra till HTTP-metoden, rubriker och fråga string-parametrar. Ändrade värden kan referera [programinställningar] och [parametrar från den ursprungliga klientbegäran].

Backend-begäranden kan ändras i portalen av expading den *begära åsidosättning* avsnitt i den här sidan proxy. 

### <a name="modify-response"></a>Ändra svaret

Som standard initieras klienten svaret som en kopia av backend-svaret. Du kan ändra svaret statuskod, orsaksfrasen, rubriker och brödtext. Ändrade värden kan referera [programinställningar], [parametrar från den ursprungliga klientbegäran], och [parametrar från backend-svaret].

Backend-begäranden kan ändras i portalen av expading den *svar åsidosättning* avsnitt i den här sidan proxy. 

## <a name="using-variables"></a>Använda variabler

Konfigurationen för en proxy behöver inte vara statisk. Du kan villkor och använda variabler från den ursprungliga klientbegäran, backend-svar eller programinställningar.

### <a name="reference-localhost"></a>Referens för lokala funktioner
Du kan använda `localhost` att referera till en funktion i appen med samma funktion direkt, utan en proxy tur och RETUR-begäran.

`"backendurl": "https://localhost/api/httptriggerC#1"` kommer att referera till en lokal HTTP utlöses funktion på vägen `/api/httptriggerC#1`

 
>[!Note]  
>Om din funktion använder *funktion, admin eller sys* åtkomstnivåer, behöver du ange koden och clientId, enligt den ursprungliga URL: en för funktionen. I det här fallet ser referensen ut som: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Parametrarna som referens

Du kan använda parametrarna som indata till backend-URL: en egenskap eller som en del av att ändra begäranden och -svar. Vissa parametrar kan bindas från flödesmallen som anges i grundläggande proxykonfigurationen och andra kan komma från egenskaperna för den inkommande begäranden.

#### <a name="route-template-parameters"></a>Vidarebefordra mallparametrar
Parametrar som används i flödesmallen kan refereras till av namn. Parameternamn omges av klammerparenteser ({}).

Om exempelvis en proxy som har en flödesmallen `/pets/{petId}`, URL: en för backend-kan innehålla värdet för `{petId}`, som i `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Om flödesmallen avslutas med ett jokertecken, till exempel `/api/{*restOfPath}`, värdet `{restOfPath}` är en strängrepresentation av återstående sökvägssegment från den inkommande begäranden.

#### <a name="additional-request-parameters"></a>Parametrar för ytterligare begäran
Följande värden kan användas i konfigurationsvärden förutom mallparametrar väg:

* **{request.method}** : HTTP-metod som används på den ursprungliga begäranden.
* **{request.headers. \<HeaderName\>}**: ett huvud som kan läsas från den ursprungliga begäranden. Ersätt  *\<HeaderName\>*  med namnet på det huvud som du vill läsa. Om rubriken inte finns på begäran, ska värdet vara en tom sträng.
* **{request.querystring. \<ParameterName\>}**: en frågesträngsparameter som kan läsas från den ursprungliga begäranden. Ersätt  *\<ParameterName\>*  med namnet på den parameter som du vill läsa. Om parametern inte finns på begäran, ska värdet vara en tom sträng.

### <a name="response-parameters"></a>Referens för backend-svarsparametrar

Svarsparametrar kan användas som en del av ändra svar till klienten. Följande värden kan användas i konfigurationsvärden:

* **{backend.response.statusCode}** : HTTP-statuskod som returneras av backend-svaret.
* **{backend.response.statusReason}** : HTTP-orsaksfrasen som returneras av backend-svaret.
* **{backend.response.headers. \<HeaderName\>}**: ett huvud som kan läsas från backend-svaret. Ersätt  *\<HeaderName\>*  med namnet på det huvud som du vill läsa. Om rubriken inte ingår i svaret, ska värdet vara en tom sträng.

### <a name="use-appsettings"></a>Referens för programinställningar

Du kan också referera [programinställningar som definierats för funktion appen](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) genom att omge inställningsnamn med procenttecken (%).

Till exempel backend-URL: en *https://%ORDER_PROCESSING_HOST%/api/orders* skulle ha ”% ORDER_PROCESSING_HOST %” ersätts med värdet för inställningen ORDER_PROCESSING_HOST.

> [!TIP] 
> Använd programinställningar för backend-värdar när du har flera distributioner eller testmiljöer. På så sätt kan kan du se till att du alltid talar till rätt serverdel för den miljön.

## <a name="debugProxies"></a>Felsöka proxyservrar

Genom att lägga till flaggan `"debug":true` till en proxyserver i ditt `proxy.json` ska du Aktivera felsökningsloggning. Loggfilerna lagras i `D:\home\LogFiles\Application\Proxies\DetailedTrace` och kan nås via de avancerade verktyg (kudu). HTTP-svar innehåller också en `Proxy-Trace-Location` huvud med en URL för att få åtkomst till loggfilen.

Du kan felsöka en proxy från klientsidan genom att lägga till en `Proxy-Trace-Enabled` huvudet inställt på `true`. Detta kommer även logga en spårning i filsystemet och returnera trace-URL som rubrik i svaret.

### <a name="block-proxy-traces"></a>Blockera proxy spårningar

Av säkerhetsskäl kan du inte vill att alla anropar din tjänst om du vill skapa en spårning. De kommer inte att komma åt trace innehållet utan att dina inloggningsuppgifter, men genererar spårningen förbrukar resurser och visar att du använder funktionen proxyservrar.

Inaktivera spårningar helt och hållet genom att lägga till `"debug":false` till någon särskild proxyserver i ditt `proxy.json`.

## <a name="advanced-configuration"></a>Avancerad konfiguration

Proxyservrar som du konfigurerar lagras i en *proxies.json* fil som finns i roten av en funktion programkatalogen. Du kan manuellt redigera den här filen och distribuera det som en del av din app när du använder någon av de [distributionsmetoder](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) som stöder funktioner. 

> [!TIP] 
> Om du inte har angett något av metoder för distribution, du kan också fungera med den *proxies.json* filen i portalen. Gå till funktionen appen, Välj **plattformsfunktioner**, och välj sedan **App Service Editor**. På så sätt, kan du visa hela filen strukturen för din funktionsapp och göra ändringar.

*Proxies.JSON* definieras av ett proxyservrar-objekt som består av namngivna proxyservrar och deras definitioner. Du kan också om redigeringsprogram stöder detta, kan du referera en [JSON-schema](http://json.schemastore.org/proxies) för kod slutförande. En exempelfil kan se ut ungefär så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Varje proxy har ett eget namn som *proxy1* i föregående exempel. Objektet för definition av motsvarande proxy definieras av följande egenskaper:

* **matchCondition**: krävs – ett objekt som definierar de begäranden som utlösa körning av denna proxy. Den innehåller två egenskaper som delas med [http-utlösare]:
    * _metoder_: en matris med proxyservern svarar på HTTP-metoderna. Om den inte är angiven svarar proxyn alla HTTP-metoder på vägen.
    * _väg_: krävs--definierar flödesmallen styra som begära webbadresserna din proxyserver svarar på. Till skillnad från i HTTP-utlösare har inget standardvärde.
* **backendUri**: URL: en för backend-resursen som begäran ska vara via proxy. Det här värdet kan referera till programinställningar och parametrar från den ursprungliga klientbegäran. Om den här egenskapen inte är inkluderad svarar Azure Functions med en HTTP-200 OK.
* **requestOverrides**: ett objekt som definierar omformningar på backend-begäran. Se [definiera ett requestOverrides objekt].
* **responseOverrides**: ett objekt som definierar omvandlingar för klient-svaret. Se [definiera ett responseOverrides objekt].

> [!NOTE] 
> Den *väg* egenskap i Azure Functions proxyservrar inte att behandla den *routePrefix* -egenskapen för Värdkonfiguration Funktionsapp. Om du vill ha ett prefix som `/api`, det måste ingå i den *väg* egenskapen.

### <a name="disableProxies"></a>Inaktivera enskilda proxyservrar

Du kan inaktivera enskilda proxyservrar genom att lägga till `"disabled": true` till proxy i den `proxies.json` filen. Detta innebär att alla förfrågningar som uppfyller matchCondidtion att returnera 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "www.example.com"
        }
    }
}
```

### <a name="requestOverrides"></a>Definiera ett requestOverrides-objekt

Objektet requestOverrides definierar ändringar som gjorts på begäran när resursen backend-anropas. Objektet definieras av följande egenskaper:

* **backend.Request.Method**: HTTP-metod som används för att anropa backend-.
* **backend.Request.QueryString. \<ParameterName\>**: en frågesträngsparameter som kan anges för anropet till serverdelen. Ersätt  *\<ParameterName\>*  med namnet på den parameter som du vill ange. Om en tom sträng ingår inte parametern på backend-begäran.
* **backend.Request.headers. \<HeaderName\>**: ett huvud som kan anges för anropet till serverdelen. Ersätt  *\<HeaderName\>*  med namnet på det huvud som du vill ange. Om du anger en tom sträng ingår inte rubriken på backend-begäran.

Värden kan referera till programinställningar och parametrar från den ursprungliga klientbegäran.

En exempelkonfiguration kan se ut ungefär så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definiera ett responseOverrides-objekt

Objektet requestOverrides definierar ändringar som görs i svaret som skickas tillbaka till klienten. Objektet definieras av följande egenskaper:

* **response.statusCode**: HTTP-statuskod som ska returneras till klienten.
* **response.statusReason**: HTTP orsaksfrasen ska returneras till klienten.
* **Response.body**: strängrepresentation av text som ska returneras till klienten.
* **Response.headers. \<HeaderName\>**: ett huvud som kan anges för svar till klienten. Ersätt  *\<HeaderName\>*  med namnet på det huvud som du vill ange. Om du anger en tom sträng ingår inte huvudet i svaret.

Värden kan referera till programinställningar, parametrar från den ursprungliga klientbegäran och parametrar från backend-svaret.

En exempelkonfiguration kan se ut ungefär så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> I det här exemplet svarstexten anges direkt, så ingen `backendUri` egenskapen krävs. Exemplet visar hur du kan använda Azure Functions proxyservrar för mocking API: er.

[Azure-portalen]: https://portal.azure.com
[http-utlösare]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[definiera ett requestOverrides objekt]: #requestOverrides
[definiera ett responseOverrides objekt]: #responseOverrides
[programinställningar]: #use-appsettings
[använda variabler]: #using-variables
[parametrar från den ursprungliga klientbegäran]: #request-parameters
[parametrar från backend-svaret]: #response-parameters
