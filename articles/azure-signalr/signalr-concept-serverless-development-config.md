---
title: Utveckla & konfigurera Azure Functions-appen - Azure SignalR
description: Information om hur du utvecklar och konfigurerar serverlösa realtidsprogram med Azure Functions och Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523178"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions-utveckling och -konfiguration med Azure SignalR Service

Azure Functions-program kan utnyttja [Azure SignalR-tjänstbindningar](../azure-functions/functions-bindings-signalr-service.md) för att lägga till funktioner i realtid. Klientprogram använder klient-SDK:er som är tillgängliga på flera språk för att ansluta till Azure SignalR-tjänsten och ta emot meddelanden i realtid.

I den här artikeln beskrivs begreppen för att utveckla och konfigurera en Azure Function-app som är integrerad med SignalR-tjänsten.

## <a name="signalr-service-configuration"></a>Konfiguration av SignalR-tjänsten

Azure SignalR-tjänsten kan konfigureras i olika lägen. När tjänsten används med Azure Functions måste den konfigureras i *serverlöst* läge.

Leta reda på sidan *Inställningar för* din SignalR-tjänstresurs i Azure-portalen. Ställ in *serviceläget* på *Serverlöst*.

![Läget för signalr-tjänsten](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-utveckling

Ett serverlöst realtidsprogram som skapats med Azure Functions och Azure SignalR-tjänsten kräver vanligtvis två Azure Functions:

* En ”negotiate-funktion” som klienten anropar för att hämta en giltig SignalR Service-åtkomsttoken och en tjänstslutpunkt-URL
* En eller flera funktioner som skickar meddelanden eller hanterar gruppmedlemskap

### <a name="negotiate-function"></a>förhandla om funktion

Ett klientprogram kräver en giltig åtkomsttoken för att ansluta till Azure SignalR Service. En åtkomsttoken kan vara anonym eller autentiseras till ett visst användar-ID. Serverlösa SignalR-tjänstprogram kräver en HTTP-slutpunkt med namnet "förhandla" för att hämta en token och annan anslutningsinformation, till exempel slutpunktsadressen för SignalR-tjänsten.

Använd en HTTP-utlöst Azure-funktion och *SignalRConnectionInfo-indatabindningen* för att generera anslutningsinformationsobjektet. Funktionen måste ha en HTTP-väg som slutar i `/negotiate`.

Mer information om hur du skapar funktionen förhandla finns i [ *signalRConnectionInfo-indatabindningsreferensen* ](../azure-functions/functions-bindings-signalr-service-input.md).

Mer information om hur du skapar en autentrad token finns [i Använda apptjänstautentisering](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Skicka meddelanden och hantera gruppmedlemskap

Använd *SignalR-utdatabindningen* för att skicka meddelanden till klienter som är anslutna till Azure SignalR Service. Du kan sända meddelanden till alla klienter eller skicka dem till en delmängd klienter som autentiseras med ett visst användar-ID eller har lagts till i en viss grupp.

Användare kan läggas till i en eller flera grupper. Du kan också använda *SignalR-utdatabindningen* för att lägga till eller ta bort användare i/från grupper.

Mer information finns i [signalr-utdatabindningsreferensen *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>SignalR-hubbar

SignalR har ett koncept av "nav". Varje klientanslutning och varje meddelande som skickas från Azure Functions begränsas till en specifik hubb. Du kan använda hubbar som ett sätt att dela upp anslutningar och meddelanden i logiska namnområden.

## <a name="client-development"></a>Utveckling av kunder

SignalR-klientprogram kan utnyttja SignalR-klienten SDK på ett av flera språk för att enkelt ansluta till och ta emot meddelanden från Azure SignalR Service.

### <a name="configuring-a-client-connection"></a>Konfigurera en klientanslutning

Om du vill ansluta till SignalR-tjänsten måste en klient slutföra en lyckad anslutningsförhandling som består av följande steg:

1. Gör en begäran till *den förhandlande* HTTP-slutpunkten som beskrivs ovan för att få giltig anslutningsinformation
1. Ansluta till SignalR-tjänsten med hjälp av tjänstens slutpunkts-URL och åtkomsttoken som hämtats från *slutpunkten förhandla*

SignalR-klient-SDK:er innehåller redan den logik som krävs för att utföra förhandlingshandskakningen. Skicka url:en för förhandla ändpunkt, minus `negotiate` segmentet, `HubConnectionBuilder`till SDK:erna . Här är ett exempel i JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Enligt konvention lägger SDK automatiskt `/negotiate` till webbadressen och använder den för att påbörja förhandlingen.

> [!NOTE]
> Om du använder JavaScript/TypeScript SDK i en webbläsare måste du [aktivera cors (Cross-origin Resource Sharing) i](#enabling-cors) funktionsappen.

Mer information om hur du använder SignalR-klienten SDK finns i dokumentationen för ditt språk:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Skicka meddelanden från en klient till tjänsten

Även om SignalR SDK tillåter klientprogram att anropa backend-logik i en SignalR-hubb, stöds den här funktionen ännu inte när du använder SignalR-tjänst med Azure-funktioner. Använd HTTP-begäranden om att anropa Azure Functions.

## <a name="azure-functions-configuration"></a>Konfiguration av Azure-funktioner

Azure Function-appar som integreras med Azure SignalR-tjänst kan distribueras som alla vanliga Azure Function-appar, med tekniker som [kontinuerlig distribution,](../azure-functions/functions-continuous-deployment.md) [zip-distribution](../azure-functions/deployment-zip-push.md)och [körning från paket](../azure-functions/run-functions-from-deployment-package.md).

Det finns dock ett par särskilda överväganden för appar som använder SignalR-tjänstbindningarna. Om klienten körs i en webbläsare måste CORS vara aktiverat. Och om appen kräver autentisering kan du integrera slutpunkten för förhandla med App Service Authentication.

### <a name="enabling-cors"></a>Aktivera Cors

JavaScript/TypeScript-klienten gör HTTP-begäranden till förhandlingsfunktionen för att initiera anslutningsförhandlingen. När klientprogrammet finns på en annan domän än Azure Function-appen måste CORS (Cross-Origin Resource Sharing) vara aktiverat i funktionsappen eller så blockerar webbläsaren begärandena.

#### <a name="localhost"></a>Localhost

När du kör funktionsappen på den `Host` lokala datorn kan du lägga till ett avsnitt i *local.settings.json* för att aktivera CORS. Lägg `Host` till två egenskaper i avsnittet:

* `CORS`- ange den grundläggande URL som är ursprunget klientprogrammet
* `CORSCredentials`- ställ `true` in den så att "withCredentials" förfrågningar

Exempel:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Moln - Azure-funktioner CORS

Om du vill aktivera CORS i en Azure Function-app går du till konfigurationsskärmen för CORS under fliken *Plattformsfunktioner* i din funktionsapp i Azure-portalen.

> [!NOTE]
> CORS-konfigurationen är ännu inte tillgänglig i Azure Functions Linux-förbrukningsplan. Använd [Azure API Management](#cloud---azure-api-management) för att aktivera CORS.

CORS med Åtkomst-Control-Allow-Autentiseringsuppgifter måste vara aktiverat för att SignalR-klienten ska kunna anropa förhandlingsfunktionen. Markera kryssrutan om du vill aktivera den.

I avsnittet Tillåtet ursprung lägger du till en post med *webbprogrammets* ursprungsbas-URL.

![Konfigurera CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Moln - Azure API-hantering

Azure API Management tillhandahåller en API-gateway som lägger till funktioner till befintliga backend-tjänster. Du kan använda den för att lägga till CORS i din funktionsapp. Det erbjuder en förbrukningsnivå med pay-per-action-priser och ett månatligt gratis bidrag.

Mer information om hur du [importerar en Azure Function-app](../api-management/import-function-app-as-api.md)finns i dokumentationen till API Management . När du har importerat kan du lägga till en inkommande princip för att aktivera CORS med stöd för åtkomstkontroll-tillåt-autentiseringsuppgifter.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Konfigurera SignalR-klienterna så att de använder API Management-URL:en.

### <a name="using-app-service-authentication"></a>Använda autentisering av apptjänst

Azure Functions har inbyggd autentisering som stöder populära leverantörer som Facebook, Twitter, Microsoft-konto, Google och Azure Active Directory. Den här funktionen kan integreras med *SignalRConnectionInfo-bindningen* för att skapa anslutningar till Azure SignalR-tjänst som har autentiserats till ett användar-ID. Ditt program kan skicka meddelanden med *signalr-utdatabindningen* som är riktade till det användar-ID:t.

Öppna fönstret Inställningar för *autentisering/auktorisering* i azure-portalen på fliken *Plattformsfunktioner* i funktionsappen. Följ dokumentationen för [App Service-autentisering](../app-service/overview-authentication-authorization.md) för att konfigurera autentisering med valfri identitetsleverantör.

När autentiserade, autentiserade `x-ms-client-principal-name` `x-ms-client-principal-id` HTTP-begäranden innehåller och rubriker som innehåller den autentiserade identitetens användarnamn och användar-ID, respektive.

Du kan använda dessa rubriker i *konfigurationen SignalRConnectionInfo* för att skapa autentiserade anslutningar. Här är ett exempel C# `x-ms-client-principal-id` förhandla funktion som använder huvudet.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Du kan sedan skicka meddelanden till `UserId` den användaren genom att ange egenskapen för ett SignalR-meddelande.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Information om andra språk finns i [Azure SignalR-tjänstbindningar](../azure-functions/functions-bindings-signalr-service.md) för Azure Functions-referens.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utvecklar och konfigurerar serverlösa SignalR-tjänstprogram med Hjälp av Azure Functions. Prova att skapa ett program själv med hjälp av en av snabbstarterna eller självstudierna på [översiktssidan för SignalR-tjänsten](index.yml).