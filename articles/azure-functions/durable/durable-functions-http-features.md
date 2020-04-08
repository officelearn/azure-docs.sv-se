---
title: HTTP-funktioner i varaktiga funktioner - Azure-funktioner
description: Lär dig mer om de integrerade HTTP-funktionerna i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802391"
---
# <a name="http-features"></a>HTTP-funktioner

Durable Functions har flera funktioner som gör det enkelt att införliva varaktiga orkestreringar och entiteter i HTTP-arbetsflöden. Den här artikeln går in på detaljer om några av dessa funktioner.

## <a name="exposing-http-apis"></a>Exponera HTTP-API:er

Orkestreringar och entiteter kan anropas och hanteras med HJÄLP AV HTTP-begäranden. Tillägget Varaktiga funktioner exponerar inbyggda HTTP-API:er. Det ger också API:er för interaktion med orkestreringar och entiteter från http-utlösta funktioner.

### <a name="built-in-http-apis"></a>Inbyggda HTTP-API:er

Tillägget Varaktiga funktioner lägger automatiskt till en uppsättning HTTP-API:er i Azure Functions-värden. Med dessa API:er kan du interagera med och hantera orkestreringar och entiteter utan att skriva någon kod.

Följande inbyggda HTTP-API:er stöds.

* [Starta ny orkestrering](durable-functions-http-api.md#start-orchestration)
* [Frågeorkestreringsinstans](durable-functions-http-api.md#get-instance-status)
* [Avsluta orkestreringsinstans](durable-functions-http-api.md#terminate-instance)
* [Skicka en extern händelse till en orkestrering](durable-functions-http-api.md#raise-event)
* [Rensa orkestreringshistorik](durable-functions-http-api.md#purge-single-instance-history)
* [Skicka en operationshändelse till en entitet](durable-functions-http-api.md#signal-entity)
* [Hämta tillståndet för en entitet](durable-functions-http-api.md#get-entity)
* [Fråga listan över entiteter](durable-functions-http-api.md#list-entities)

Se [http-API:erna](durable-functions-http-api.md) för en fullständig beskrivning av alla inbyggda HTTP-API:er som exponeras av tillägget Varaktiga funktioner.

### <a name="http-api-url-discovery"></a>Identifiering av HTTP API-URL

[Orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) exponerar API:er som kan generera praktiska HTTP-svarsnyttolaster. Det kan till exempel skapa ett svar som innehåller länkar till hanterings-API:er för en viss orkestreringsinstans. I följande exempel visas en HTTP-trigger-funktion som visar hur du använder det här API:et för en ny orkestreringsinstans:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**funktion.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Starta en orchestrator-funktion med hjälp av http-trigger-funktioner som visas tidigare kan göras med valfri HTTP-klient. Följande cURL-kommando startar en `DoWork`orchestrator-funktion med namnet :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Nästa är ett exempel svar för `abc123` en orkestrering som har som sitt ID. Vissa detaljer har tagits bort för tydlighetens skull.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

I föregående exempel motsvarar vart och `Uri` ett av fälten som slutar i ett inbyggt HTTP-API. Du kan använda dessa API:er för att hantera målorkestreringsinstansen.

> [!NOTE]
> Formatet på webhook-url:erna beror på vilken version av Azure Functions-värden du kör. Det föregående exemplet är för Azure Functions 2.0-värden.

En beskrivning av alla inbyggda HTTP-API:er finns i [HTTP API-referensen](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Async-åtgärdsspårning

HTTP-svaret som nämndes tidigare är utformat för att implementera långvariga HTTP-asynkron API:er med varaktiga funktioner. Detta mönster kallas ibland *för röstningskonsumentmönstret*. Klient-serverflödet fungerar på följande sätt:

1. Klienten utfärdar en HTTP-begäran om att starta en tidskrävande process som en orchestrator-funktion.
1. Mål-HTTP-utlösaren returnerar ett HTTP 202-svar med ett platshuvud som har värdet "statusQueryGetUri".
1. Klienten avsekringsadressen i platshuvudet. Klienten fortsätter att se HTTP 202-svar med ett platshuvud.
1. När instansen är klar eller misslyckas returnerar slutpunkten i platshuvudet HTTP 200.

Det här protokollet möjliggör samordning av tidskrävande processer med externa klienter eller tjänster som kan avsöka en HTTP-slutpunkt och följa platshuvudet. Både klient- och serverimplementeringarna för det här mönstret är inbyggda i HTTP-API:erna för varaktiga funktioner.

> [!NOTE]
> Som standard stöder alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) standardasynkronåtgärdsmönster. Den här funktionen gör det möjligt att bädda in en varaktig funktion som körs på länge som en del av ett Logic Apps-arbetsflöde. Du hittar mer information om Logic Apps-stöd för asynkrona HTTP-mönster i [Azure Logic Apps arbetsflödesåtgärder och utlösardokumentation](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interaktioner med orkestreringar kan göras från vilken funktionstyp som helst, inte bara HTTP-utlösta funktioner.

Mer information om hur du hanterar orkestreringar och entiteter med hjälp av klient-API:er finns i [artikel om instanshantering](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Använda HTTP-API:er

Som beskrivs i [orchestrator funktionskodbegränsningar](durable-functions-code-constraints.md), orchestrator funktioner kan inte göra I / O direkt. I stället anropar de vanligtvis [aktivitetsfunktioner](durable-functions-types-features-overview.md#activity-functions) som utför I/O-åtgärder.

Från och med Varaktiga funktioner 2.0 kan orkestreringar använda HTTP-API:er med hjälp av [orkesterreriggsbindningen](durable-functions-bindings.md#orchestration-trigger).

Följande exempelkod visar en orchestrator-funktion som gör en utgående HTTP-begäran:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Genom att använda åtgärden "anropa HTTP" kan du utföra följande åtgärder i orchestrator-funktionerna:

* Anropa HTTP-API:er direkt från orchestration-funktioner, med vissa begränsningar som nämns senare.
* Stöd automatiskt för HTTP 202-statusavsökningsmönster på klientsidan.
* Använd [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) för att ringa auktoriserade HTTP-anrop till andra Azure-slutpunkter.

Möjligheten att använda HTTP-API:er direkt från orchestrator-funktioner är avsedd som en bekvämlighet för en viss uppsättning vanliga scenarier. Du kan implementera alla dessa funktioner själv med hjälp av aktivitetsfunktioner. I många fall kan aktivitetsfunktioner ge dig större flexibilitet.

### <a name="http-202-handling"></a>HTTP 202-hantering

API:et "anropa HTTP" kan automatiskt implementera klientsidan av avsökningskonsumentmönstret. Om ett anropat API returnerar ett HTTP 202-svar med ett platshuvud avsökningar automatiskt platsresursen tills ett annat svar än 202 får ett annat svar än 202. Det här svaret är svaret som returneras till orchestrator-funktionskoden.

> [!NOTE]
> Orchestrator-funktioner stöder också konsumentmönstret på serversidan, enligt beskrivningen i [spårning av Async-åtgärder](#async-operation-tracking). Det här stödet innebär att orkestreringar i en funktionsapp enkelt kan samordna orchestrator-funktionerna i andra funktionsappar. Detta liknar [underorkestreringskonceptet,](durable-functions-sub-orchestrations.md) men med stöd för kommunikation över flera appar. Det här stödet är särskilt användbart för apputveckling av mikrotjänststil.

### <a name="managed-identities"></a>Hanterade identiteter

Varaktiga funktioner stöder inbyggt anrop till API:er som accepterar Azure Active Directory-token (Azure AD) för auktorisering. Det här stödet använder [Azure-hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för att hämta dessa token.

Följande kod är ett exempel på en .NET orchestrator-funktion. Funktionen gör autentiserade anrop för att starta om en virtuell dator med hjälp av Azure Resource Manager [virtuella datorer REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

I föregående exempel `tokenSource` är parametern konfigurerad för att hämta Azure AD-token för [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Token identifieras av resursen URI `https://management.core.windows.net`. Exemplet förutsätter att den aktuella funktionsappen antingen körs lokalt eller har distribuerats som en funktionsapp med en hanterad identitet. Den lokala identiteten eller den hanterade identiteten antas ha behörighet `myRG`att hantera virtuella datorer i den angivna resursgruppen .

Vid körning returnerar den konfigurerade tokenkällan automatiskt en OAuth 2.0-åtkomsttoken. Källan lägger sedan till token som innehavartoken i auktoriseringshuvudet för den utgående begäran. Den här modellen är en förbättring jämfört med att manuellt lägga till auktoriseringshuvuden i HTTP-begäranden av följande skäl:

* Tokenuppdatering hanteras automatiskt. Du behöver inte oroa dig för utgångna token.
* Token lagras aldrig i det varaktiga orkestreringstillståndet.
* Du behöver inte skriva någon kod för att hantera tokenförvärv.

Du hittar ett mer komplett exempel i [exemplet för förkompilerade C# RestartVMs](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

Hanterade identiteter är inte begränsade till Azure-resurshantering. Du kan använda hanterade identiteter för att komma åt alla API-program som accepterar Azure AD-innehavartoken, inklusive Azure-tjänster från Microsoft och webbappar från partner. En partners webbapp kan till och med vara en annan funktionsapp. En lista över Azure-tjänster från Microsoft som stöder autentisering med Azure AD finns i [Azure-tjänster som stöder Azure AD-autentisering](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Begränsningar

Det inbyggda stödet för att anropa HTTP-API:er är en bekvämlighetsfunktion. Det är inte lämpligt för alla scenarier.

HTTP-begäranden som skickas av orchestrator-funktioner och deras svar serialiseras och beständiga som kömeddelanden. Det här köbeteendet säkerställer att HTTP-anrop är [tillförlitliga och säkra för orchestration replay](durable-functions-orchestrations.md#reliability). Köbeteendet har dock också begränsningar:

* Varje HTTP-begäran innebär ytterligare svarstid jämfört med en inbyggd HTTP-klient.
* Stora begärande- eller svarsmeddelanden som inte får plats i ett kömeddelande kan avsevärt försämra orchestration-prestanda. Omkostnader för avlastning meddelande nyttolaster till blob lagring kan orsaka potentiell prestandaförsämring.
* Streaming, segmenterade och binära nyttolaster stöds inte.
* Möjligheten att anpassa HTTP-klientens beteende är begränsad.

Om någon av dessa begränsningar kan påverka ditt användningsfall bör du i stället använda aktivitetsfunktioner och språkspecifika HTTP-klientbibliotek för att ringa utgående HTTP-anrop.

> [!NOTE]
> Om du är EN .NET-utvecklare kanske du undrar varför den här funktionen använder typerna **DurableHttpRequest** och **DurableHttpResponse** i stället för de inbyggda typerna .NET **HttpRequestMessage** och **HttpResponseMessage.**
>
> Detta designval är avsiktligt. Den främsta orsaken är att anpassade typer hjälper till att säkerställa att användarna inte gör felaktiga antaganden om de beteenden som stöds för den interna HTTP-klienten. Typer som är specifika för varaktiga funktioner gör det också möjligt att förenkla API-design. De kan också lättare göra tillgängliga specialfunktioner som [hanterad identitetsintegration](#managed-identities) och [röstningskonsumentmönstret.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Utökningsbarhet (.net endast)

Det går att anpassa beteendet för orkestreringens interna HTTP-klient med hjälp av [Azure Functions .NET-beroendeinjektion](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Den här förmågan kan vara användbar för att göra små beteendeförändringar. Det kan också vara användbart för enhetstestning av HTTP-klienten genom att injicera utkastobjekt.

I följande exempel visas hur du använder beroendeinjektion för att inaktivera TLS/SSL-certifikatvalidering för orchestrator-funktioner som anropar externa HTTP-slutpunkter.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om varaktiga entiteter](durable-functions-entities.md)
