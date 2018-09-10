---
title: Arbeta med proxyservrar i Azure Functions | Microsoft Docs
description: Översikt över hur du använder Azure Functions Proxies
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 2aa8036149f4056f2d197f0712b86104f5cf2215
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095053"
---
# <a name="work-with-azure-functions-proxies"></a>Arbeta med Azure Functions Proxies

Den här artikeln beskriver hur du konfigurerar och arbetar med Azure Functions-proxyservrar. Med den här funktionen kan du ange slutpunkter på funktionsappen som implementeras av en annan resurs. Du kan använda dessa proxyservrar för att dela ett stort API i flera funktionsappar (som i en mikrotjänstarkitektur), samtidigt som en enda API-yta för klienter.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardfunktioner fakturering gäller proxy körningar. Mer information finns i [priser för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Skapa en proxy

Det här avsnittet visar hur du skapar en proxy i Functions-portalen.

1. Öppna den [Azure Portal], och gå sedan till din funktionsapp.
2. I den vänstra rutan väljer **nya proxyserverkonfigurationen**.
3. Ange ett namn för proxyservern.
4. Konfigurera den slutpunkt som exponeras i den här funktionsappen genom att ange den **flödesmallen** och **HTTP-metoder**. Dessa parametrar fungerar enligt reglerna för [HTTP-utlösare].
5. Ange den **backend-URL** till en annan slutpunkt. Den här slutpunkten kan vara en funktion i en annan funktionsapp eller det bero på att alla andra API: er. Värdet behöver inte vara statiska och kan referera till [programinställningar] och [parametrar från den ursprungliga klientbegäran].
6. Klicka på **Skapa**.

Proxyservern finns nu som en ny slutpunkt på din funktionsapp. Från en klientperspektiv motsvarar den en HttpTrigger i Azure Functions. Du kan testa din nya proxyserverkonfigurationen genom att kopiera URL: en för Proxy och testa det med dina favoritverktyg HTTP-klienten.

## <a name="modify-requests-responses"></a>Ändra begäranden och svar

Du kan ändra begäranden till och -svar från backend-server med Azure Functions Proxies. Dessa omvandlingar kan använda variabler som definieras i [använda variabler].

### <a name="modify-backend-request"></a>Ändra backend-begäran

Som standard initieras backend-begäran som en kopia av den ursprungliga begäran. Förutom att URL: en för backend-server kan du ändra till HTTP-metoden, rubriker och frågesträngsparametrarna. Ändrade värden kan referera till [programinställningar] och [parametrar från den ursprungliga klientbegäran].

Backend-begäranden kan ändras i portalen av expading den *begär åsidosättning* delen av sidan proxy. 

### <a name="modify-response"></a>Ändra svaret

Som standard initieras klienten svaret som en kopia av backend-svaret. Du kan ändra Svarets statuskod, orsaksfras, rubriker och brödtext. Ändrade värden kan referera till [programinställningar], [parametrar från den ursprungliga klientbegäran], och [parametrar från backend-svaret].

Backend-begäranden kan ändras i portalen av expading den *Åsidosätt svar* delen av sidan proxy. 

## <a name="using-variables"></a>Använda variabler

Konfigurationen för en proxyserver behöver inte vara statisk. Du kan ange ett villkor för det för att använda variabler i ursprungliga klientbegäran, backend-svar eller programinställningar.

### <a name="reference-localhost"></a>Referens för lokala funktioner
Du kan använda `localhost` att referera till en funktion i samma funktionsapp direkt, utan en proxy tur och RETUR-begäran.

`"backendurl": "https://localhost/api/httptriggerC#1"` sedan hänvisar till en lokal HTTP-utlöst funktion på vägen `/api/httptriggerC#1`

 
>[!Note]  
>Om din funktion använder *funktion, admin eller sys* åtkomstnivåer, måste du ange koden och clientId, enligt den ursprungliga funktions-URL. I det här fallet ser referensen ut som: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Parametrarna som referens

Du kan använda parametrarna som indata till backend-URL: en egenskap eller som en del av ändra begäranden och svar. Vissa parametrar kan vara kopplat från flödesmallen som anges i den grundläggande proxykonfigurationen och andra kan komma från egenskaperna för den inkommande begäranden.

#### <a name="route-template-parameters"></a>Dirigera mallparametrar
Parametrar som används i flödesmallen är tillgängliga för att referera till efter namn. Parameternamnen står inom klammerparenteser ({}).

Exempel: om en proxy har en flödesmall som `/pets/{petId}`, backend-URL: en kan innehålla värdet för `{petId}`, som i `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Om flödesmallen avslutas i ett jokertecken, till exempel `/api/{*restOfPath}`, värdet `{restOfPath}` är en strängrepresentation av återstående sökvägssegment från den inkommande begäranden.

#### <a name="additional-request-parameters"></a>Om ytterligare begäranparametrar
Följande värden kan användas i konfigurationsvärden förutom mallparametrar väg:

* **{request.method}** : HTTP-metod som används på den ursprungliga begäran.
* **{request.headers. \<HeaderName\>}**: en rubrik som kan läsas från den ursprungliga begäran. Ersätt *\<HeaderName\>* med namnet på rubriken som du vill läsa. Om sidhuvudet inte finns med på begäran, kommer värdet vara en tom sträng.
* **{request.querystring. \<ParameterName\>}**: en frågesträngsparameter som kan läsas från den ursprungliga begäran. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill läsa. Om parametern inte finns med på begäran, kommer värdet vara en tom sträng.

### <a name="response-parameters"></a>Referens för backend-svarsparametrar

Svarsparametrar kan användas som en del av ändra svaret till klienten. Följande värden kan användas i konfigurationsvärden:

* **{backend.response.statusCode}** : HTTP-statuskoden som returneras av backend-svaret.
* **{backend.response.statusReason}** : HTTP-orsaksfras som returneras av backend-svaret.
* **{backend.response.headers. \<HeaderName\>}**: en rubrik som kan läsas från backend-svaret. Ersätt *\<HeaderName\>* med namnet på rubriken som du vill läsa. Om rubriken inte ingår i svaret, kommer värdet vara en tom sträng.

### <a name="use-appsettings"></a>Referens för programinställningar

Du kan också referera [programinställningar som definierats för funktionsappen](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) om inställningsnamn med procenttecken (%).

Till exempel en backend-URL: en för *https://%ORDER_PROCESSING_HOST%/api/orders* skulle ha ”% ORDER_PROCESSING_HOST %” ersätts med värdet för inställningen ORDER_PROCESSING_HOST.

> [!TIP] 
> Använd programinställningar för backend-värdar när du har flera distributioner eller testmiljöer. På så sätt kan du kan se till att du alltid pratar med rätt serverdelen för den miljön.

## <a name="debugProxies"></a>Felsöka proxyservrar

Genom att lägga till flaggan `"debug":true` till alla proxy i din `proxies.json` aktiverar felsökningsloggning. Loggar lagras i `D:\home\LogFiles\Application\Proxies\DetailedTrace` och är åtkomlig via avancerade verktyg (kudu). Alla HTTP-svar som innehåller också en `Proxy-Trace-Location` huvud med en URL till loggfilen.

Du kan felsöka en proxy från klientsidan genom att lägga till en `Proxy-Trace-Enabled` rubrik inställd `true`. Detta kommer också logga en spårning på filsystemet och returnera trace-URL: en som en rubrik i svaret.

### <a name="block-proxy-traces"></a>Blockera proxy spårningar

Av säkerhetsskäl kan du inte vill att alla anropar din tjänst för att generera en spårning. De kommer inte att kunna få åtkomst till trace innehållet utan att dina inloggningsuppgifter, men genererar spårningen förbrukar resurser och visar att du använder Funktionsproxy.

Inaktivera spårningar helt och hållet genom att lägga till `"debug":false` till alla viss proxy i din `proxies.json`.

## <a name="advanced-configuration"></a>Avancerad konfiguration

Proxyservrar som du konfigurerar lagras i en *proxies.json* filen som finns i roten av en funktionen app-katalogen. Du kan manuellt redigera filen och distribuera den som en del av din app när du använder någon av den [distributionsmetoder](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) som Functions stöder. 

> [!TIP] 
> Om du inte har angett något av metoder för distribution, du kan också arbeta med den *proxies.json* filen i portalen. Gå till din funktionsapp väljer **plattformsfunktioner**, och välj sedan **App Service Editor**. Då kan du visa hela filen strukturen för din funktionsapp och göra ändringar.

*Proxies.JSON* definieras av ett objekt för proxyservrar som består av namngivna proxyservrar och deras definitioner. Du kan också om redigeringsprogram stöder det kan du referera till en [JSON-schema](http://json.schemastore.org/proxies) för kodifyllning. En exempelfil kan se ut så här:

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

Varje proxy har ett eget namn som *proxy1* i föregående exempel. Objekt för rättighetsdefinition av motsvarande proxy definieras av följande egenskaper:

* **matchCondition**: krävs – ett objekt som definierar de förfrågningar som utlösa körningen av den här proxyn. Den innehåller två egenskaper som delas med [HTTP-utlösare]:
    * _metoder_: en matris med HTTP-metoder som proxyn svarar. Om det inte anges svarar proxyn på alla HTTP-metoder på vägen.
    * _väg_: krävs – definierar flödesmallen styra som begär URL: er proxyn svarar på. Till skillnad från i HTTP-utlösare finns inget standardvärde.
* **backendUri**: URL: en för backend-resursen som begäran ska vara via proxy. Det här värdet kan referera till programinställningar och parametrar från den ursprungliga klientbegäran. Om den här egenskapen inte finns, svarar Azure Functions med en HTTP 200 OK.
* **requestOverrides**: ett objekt som definierar transformationer på backend-begäran. Se [Definiera ett requestOverrides-objekt].
* **responseOverrides**: ett objekt som definierar transformationer för klient-svaret. Se [Definiera ett responseOverrides-objekt].

> [!NOTE] 
> Den *väg* -egenskapen i Azure Functions-proxyservrar inte att behandla den *routePrefix* egenskapen för värdkonfigurationen Funktionsapp. Om du vill lägga till ett prefix som `/api`, det måste finnas med i den *väg* egenskapen.

### <a name="disableProxies"></a>Inaktivera enskilda proxyservrar

Du kan inaktivera enskilda proxyservrar genom att lägga till `"disabled": true` till proxyn i den `proxies.json` filen. Detta innebär att alla begäranden som uppfyller matchCondidtion att returnera 404.
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

* **backend.Request.Method**: HTTP-metod som används för att anropa backend-server.
* **backend.Request.QueryString. \<ParameterName\>**: en frågesträngsparameter som kan ställas in för anrop till serverdelen. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill använda. Om den tomma strängen anges, ingår inte parametern på backend-begäran.
* **backend.Request.headers. \<HeaderName\>**: en rubrik som kan ställas in för anrop till serverdelen. Ersätt *\<HeaderName\>* med namnet på rubriken som du vill ange. Om du anger den tomma strängen kan ingår inte rubriken på backend-begäran.

Värden kan referera till programinställningar och parametrar från den ursprungliga klientbegäran.

En exempelkonfiguration kan se ut så här:

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

Objektet requestOverrides definierar ändringar som görs i ett svar som skickas tillbaka till klienten. Objektet definieras av följande egenskaper:

* **response.statusCode**: HTTP-statuskod som ska returneras till klienten.
* **response.statusReason**: HTTP orsaksfras ska returneras till klienten.
* **Response.body**: strängrepresentation av texten som ska returneras till klienten.
* **Response.headers. \<HeaderName\>**: en rubrik som kan ställas in för svar till klienten. Ersätt *\<HeaderName\>* med namnet på rubriken som du vill ange. Om du anger den tomma strängen kan ingår inte rubriken på ett svar.

Värden kan referera till programinställningar, parametrar från den ursprungliga klientbegäran och parametrar från backend-svaret.

En exempelkonfiguration kan se ut så här:

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
> I det här exemplet svarstexten anges direkt, så ingen `backendUri` egenskapen krävs. Exemplet visar hur du kan använda Azure Functions-proxyservrar för simulerade API: er.

[Azure Portal]: https://portal.azure.com
[HTTP-utlösare]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definiera ett requestOverrides-objekt]: #requestOverrides
[Definiera ett responseOverrides-objekt]: #responseOverrides
[programinställningar]: #use-appsettings
[Använda variabler]: #using-variables
[parametrar från den ursprungliga klientbegäran]: #request-parameters
[parametrar från backend-svaret]: #response-parameters
