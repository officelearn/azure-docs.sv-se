---
title: HTTP-funktioner i Durable Functions-Azure Functions
description: Lär dig mer om de integrerade HTTP-funktionerna i Durable Functions-tillägget för Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935799"
---
# <a name="http-features"></a>HTTP-funktioner

Durable Functions har flera funktioner som gör det enkelt att införliva varaktiga dirigeringar och entiteter i HTTP-arbetsflöden. Den här artikeln innehåller information om några av dessa funktioner.

## <a name="exposing-http-apis"></a>Exponerar HTTP-API: er

Dirigeringar och entiteter kan anropas och hanteras med HTTP-begäranden. Durable Functions-tillägget visar inbyggda HTTP-API: er och innehåller också API: er för att interagera med dirigeringar och entiteter från inlösta funktioner i HTTP.

### <a name="built-in-http-apis"></a>Inbyggda HTTP API: er

Durable Functions-tillägget lägger automatiskt till en uppsättning HTTP-API: er till Azure Functions värden. Med dessa API: er kan du interagera med och hantera dirigeringar och entiteter utan att skriva någon kod.

Följande inbyggda HTTP-API: er stöds.

* [Starta nytt dirigering](durable-functions-http-api.md#start-orchestration)
* [Fråga om Orchestration-instans](durable-functions-http-api.md#get-instance-status)
* [Avsluta Orchestration-instans](durable-functions-http-api.md#terminate-instance)
* [Skicka en extern händelse till ett dirigering](durable-functions-http-api.md#raise-event)
* [Rensa Orchestration-historik](durable-functions-http-api.md#purge-single-instance-history)
* [Skicka en åtgärds händelse till en entitet](durable-functions-http-api.md#signal-entity)
* [Fråga om status för en entitet](durable-functions-http-api.md#query-entity)

I artikeln om [http-API: er](durable-functions-http-api.md) finns en fullständig beskrivning av alla inbyggda http-API: er som exponeras av Durable Functions-tillägget.

### <a name="http-api-url-discovery"></a>HTTP API URL-identifiering

[Dirigerings klientens bindning](durable-functions-bindings.md#orchestration-client) exponerar API: er som kan användas för att generera praktiska nytto laster för HTTP-svar. Det kan till exempel skapa ett svar som innehåller länkar till hanterings-API: er för en angiven Dirigerings instans. Här är ett exempel på en HTTP-utlösare som visar hur du använder det här API: t för en ny Dirigerings instans:

#### <a name="precompiled-c"></a>FörkompileradeC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C#Över

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>function. JSON

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Att starta en Orchestrator-funktion med hjälp av funktionerna för HTTP-utlösare som visas ovan kan göras med valfri HTTP-klient. Följande spiral kommando startar en Orchestrator-funktion med `DoWork`namnet.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Här är ett exempel på ett svar för ett dirigering `abc123` med som dess ID (viss information tas bort för tydlighetens skull):

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

Vart och ett `~Uri` av fälten i föregående exempel motsvarar inbyggda http API: er. Dessa API: er kan användas för att hantera mål Dirigerings instansen.

> [!NOTE]
> Formatet för webhook-URL: erna kan variera beroende på vilken version av Azure Functions-värden som du kör. Exemplet ovan är för Azure Functions 2,0-värden.

En beskrivning av alla inbyggda HTTP API: er finns i referens dokumentationen för [http API](durable-functions-http-api.md) .

### <a name="async-operation-tracking"></a>Asynkron åtgärds spårning

Det HTTP-svar som tidigare nämnts är utformat för att hjälpa till att implementera långvariga HTTP asynkrona API: er med Durable Functions. Det här mönstret kallas ibland för *avsökningens konsument mönster*. Klient/server-flödet fungerar på följande sätt:

1. Klienten utfärdar en HTTP-begäran om att starta en tids krävande process, till exempel en Orchestrator-funktion.
2. Målets http-utlösare returnerar ett http 202 `Location` -svar med `statusQueryGetUri` ett huvud med värdet.
3. Klienten avsöker URL: en i `Location` rubriken. Det fortsätter att se http 202-svar med `Location` ett sidhuvud.
4. När instansen har slutförts (eller Miss lyckas) returnerar slut `Location` punkten i rubriken http 200.

Det här protokollet tillåter samordning av långvariga processer med externa klienter eller tjänster som stöder avsökning av `Location` en http-slutpunkt och följande rubrik. Både klient-och Server implementeringarna av det här mönstret är inbyggda i Durable Functions HTTP-API: er.

> [!NOTE]
> Som standard har alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) stöd för standard mönstret för asynkrona åtgärder. Den här funktionen gör det möjligt att bädda in en långsiktig, varaktig funktion som en del av ett Logic Apps-arbetsflöde. Mer information om Logic Apps stöd för asynkrona HTTP-mönster finns i [dokumentationen för Azure Logic Apps arbets flödes åtgärder och utlösare](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interaktion med Orchestration kan göras från alla funktions typer, inte bara HTTP-utlösta funktioner.

Mer information om hur du hanterar dirigeringar och entiteter med hjälp av klient-API: er finns i artikeln [instans hantering](durable-functions-instance-management.md) .

## <a name="consuming-http-apis"></a>Använda HTTP API: er

Orchestrator-funktioner tillåts inte i/O direkt, enligt beskrivningen i Orchestrator- [funktionens kod begränsningar](durable-functions-code-constraints.md). I stället anropar Orchestrator funktioner vanligt vis [aktivitets funktioner](durable-functions-types-features-overview.md#activity-functions) som utför I/O-åtgärder.

Från och med Durable Functions 2,0 kan dirigeringar använda HTTP-API: er med hjälp av [bindningen för Dirigerings utlösare](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> Möjligheten att anropa HTTP-slutpunkter direkt från Orchestrator-funktioner är inte tillgänglig ännu i Java Script.

Följande exempel kod visar en C# Orchestrator-funktion som gör en utgående http-begäran `CallHttpAsync` med hjälp av .NET-API: et:

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

Med åtgärden "anropa HTTP" kan du utföra följande saker i dina Orchestrator-funktioner:

* Anropa HTTP-API: er direkt från Orchestration-funktioner (med vissa begränsningar som nämns senare).
* Automatiskt stöd för HTTP 202 status avsöknings mönster på klient sidan.
* Använd [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) för att göra auktoriserade http-anrop till andra Azure-slutpunkter.

Möjligheten att använda HTTP-API: er direkt från Orchestrator-funktioner är avsedd som en bekvämlighet för en viss uppsättning vanliga scenarier. Det är möjligt att du implementerar alla dessa funktioner själv med hjälp av aktivitets funktioner. I många fall kan aktivitets funktioner ge dig större flexibilitet.

### <a name="http-202-handling"></a>HTTP 202-hantering

API: t "anropa HTTP" kan automatiskt implementera klient sidan för *avsökningens konsument mönster*. Om ett anropat API returnerar ett http 202-svar `Location` med ett huvud, kommer Orchestrator-funktionen automatiskt `Location` att söka i resursen tills ett svar som inte är 202 kommer tillbaka. Svaret som inte är 202 är svaret som returnerades till Orchestrator-funktions koden.

> [!NOTE]
> Orchestrator-funktioner stöder också *klient mönstret för avsökning*på Server sidan, enligt beskrivningen i [asynkron åtgärds spårning](#async-operation-tracking). Det innebär att dirigeringar i en Function-app enkelt kan koordinera Orchestrator-funktionerna i andra Function-appar. Detta liknar [under Orchestration-](durable-functions-sub-orchestrations.md) konceptet, men med stöd för kommunikation mellan appar. Detta är särskilt användbart för program utveckling i mikroutförande.

### <a name="managed-identities"></a>Hanterade identiteter

Durable Functions har inbyggt stöd för API: er som accepterar Azure AD-token för auktorisering. Det här stödet använder [Azure Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för att hämta dessa tokens.

Följande kod är ett exempel på en .NET Orchestrator-funktion som gör autentiserade anrop för att starta om en virtuell dator med hjälp av Azure Resource Manager [Virtual Machines REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
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

I föregående exempel `tokenSource` är parametern konfigurerad för att hämta Azure AD-token för [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (identifieras av "resurs-URI" `https://management.core.windows.net`). Exemplet förutsätter att den aktuella Function-appen antingen körs lokalt eller har distribuerats som en Azure Functions app med en hanterad identitet. Den lokala identiteten eller den hanterade identiteten antas ha behörighet att hantera virtuella datorer i den angivna resurs `myRG`gruppen.

Vid körning returnerar den konfigurerade token-källan automatiskt en OAuth 2,0-åtkomsttoken och lägger till den som en Bearer- `Authorization` token i rubriken för den utgående begäran. Den här modellen är en förbättring av manuellt tillägg av auktoriseringsarkiv till HTTP-begäranden på grund av följande:

* Uppdatering av token hanteras automatiskt. Du behöver inte bekymra dig om utgångna token.
* Tokens lagras aldrig i det hållbara Orchestration-läget.
* Du behöver inte skriva någon kod för att hantera hämtning av token.

Ett mer komplett exempel finns i [förkompilerade C# "RestartVMs"-exempel](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Hanterade identiteter är inte begränsade till Azure Resource Management. Hanterade identiteter kan användas för att få åtkomst till alla API: er som accepterar Azure AD Bearer-token, inklusive Azure-tjänster från första part eller webb program från tredje part. Webb programmet från tredje part kan även vara en annan Function-app. En lista över Azure-tjänster från första part som stöder autentisering med Azure AD finns i [Azure-tjänster som stöder Azure AD-autentisering](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Begränsningar

Det inbyggda stödet för att anropa HTTP API: er är en bekvämlighets funktion som inte passar för alla scenarier. HTTP-förfrågningar som skickats av Orchestrator-funktioner och deras svar serialiseras och sparas som Kömeddelanden. Detta köat beteende garanterar att HTTP-anrop är [pålitliga och säkra för omuppspelning av dirigering](durable-functions-orchestrations.md#reliability). Detta köer innebär dock också att:

* Varje HTTP-begäran omfattar ytterligare svars tid jämfört med en intern HTTP-klient.
* Meddelanden om stora förfrågningar eller svar som inte får plats i ett Queue-meddelande kan kraftigt försämra Dirigerings prestanda. Den potentiella prestanda försämringen beror på omkostnaderna för att avlasta meddelande nytto laster till Blob Storage.
* Strömnings-, segment-och binära nytto laster stöds inte.
* Möjligheten att anpassa HTTP-klientens beteende är begränsad.

Om någon av dessa begränsningar kan påverka din användnings fråga bör du i stället använda aktivitets funktioner och språkspecifika HTTP-klientcertifikat för att göra utgående HTTP-anrop.

> [!NOTE]
> Om du är en .NET-utvecklare kanske du undrar varför den här funktionen använder `DurableHttpRequest` och `DurableHttpResponse` typer i stället för de inbyggda .net `HttpRequestMessage` och `HttpResponseMessage`. Det här design valet var avsiktligt. Den främsta orsaken är att en anpassad typ hjälper till att se till att användarna inte gör felaktiga antaganden om de funktioner som stöds av den interna HTTP-klienten. Robusta specifika typer gör det också möjligt att förenkla API-utformningen och lätt att se särskilda funktioner, till exempel [hanterad identitets integrering](#managed-identities) och [avsöknings konsument mönster](#http-202-handling).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om varaktiga entiteter](durable-functions-entities.md)