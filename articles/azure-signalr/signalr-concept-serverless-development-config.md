---
title: Utveckla och konfigurera Azure Functions SignalR Service-program
description: Information om hur du utvecklar och konfigurera program utan server i realtid med Azure Functions och Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569177"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions-utvecklingen och konfiguration med Azure SignalR Service

Azure Functions-program kan utnyttja den [Azure SignalR Service-bindningar](../azure-functions/functions-bindings-signalr-service.md) att lägga till funktioner i realtid. Klientprogram använda klient-SDK som är tillgängligt på flera språk för att ansluta till Azure SignalR Service och ta emot meddelanden i realtid.

Den här artikeln beskriver begrepp för att utveckla och konfigurera en Azure Function-app som är integrerad med SignalR Service.

## <a name="signalr-service-configuration"></a>SignalR Service-konfiguration

Azure SignalR Service kan konfigureras i olika lägen. När den används med Azure Functions, tjänsten måste konfigureras i *serverlösa* läge.

I Azure-portalen, letar du upp den *inställningar* sidan för din SignalR Service-resurs. Ange den *tjänstläge* till *Serverless*.

![SignalR-tjänstläge](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-utveckling

Ett program för utan server i realtid som har skapats med Azure Functions och Azure SignalR Service normalt kräver två Azure-funktioner:

* En ”förhandla”-funktion som klienten begär för att få en giltig SignalR Service åtkomsttoken och där tjänsten slutpunkts-URL
* En eller flera funktioner som skickar meddelanden eller hantera gruppmedlemskap

### <a name="negotiate-function"></a>förhandla funktion

Ett klientprogram kräver en giltig åtkomsttoken för att ansluta till Azure SignalR Service. En åtkomsttoken kan vara anonym eller autentiserade till en viss användare-ID. Program utan Server SignalR Service kräver en HTTP-slutpunkt med namnet ”förhandla” för att hämta en token och annan anslutningsinformation, till exempel SignalR Service slutpunkts-URL.

Azure-funktion som utlöses av Använd HTTP och *SignalRConnectionInfo* indatabindning att generera information anslutningsobjektet. Funktionen måste ha en HTTP-väg som slutar med `/negotiate`.

Mer information om hur du skapar förhandla funktionen finns i den [ *SignalRConnectionInfo* indata schemabindning som hänvisar](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Läs om hur du skapar en autentiserad token [med hjälp av App Service-autentisering](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Skicka meddelanden och hantera gruppmedlemskap

Använd den *SignalR* bindning av utdata för att skicka meddelanden till klienter är anslutna till Azure SignalR Service. Du kan skicka meddelanden till alla klienter eller du kan skicka dem till en delmängd av klienterna som autentiseras med ett specifikt användar-ID eller har lagts till i en specifik grupp.

Användare kan läggas till en eller flera grupper. Du kan också använda den *SignalR* utdatabindning att lägga till eller ta bort användare till och från grupper.

Mer information finns i den [ *SignalR* utdata schemabindning som hänvisar](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>SignalR Hubs

SignalR har en begreppet ”hubbar”. Varje klientanslutning och alla meddelanden som skickas från Azure Functions är begränsad till en specifik hubb. Du kan använda hubs som ett sätt att dela dina anslutningar och meddelanden i logiska namnområden.

## <a name="client-development"></a>Klientutveckling

SignalR klientprogram kan använda SignalR-klientens SDK i någon av flera språk att enkelt ansluta till och ta emot meddelanden från Azure SignalR Service.

### <a name="configuring-a-client-connection"></a>Konfigurera en klientanslutning

Om du vill ansluta till SignalR Service, måste en klient utföra en lyckad anslutning förhandling som består av följande:

1. Skicka en förfrågan om att den *förhandla* HTTP-slutpunkt som beskrivs ovan till hämta giltig anslutningsinformation
1. Ansluta till SignalR Service med hjälp av slutpunkts-URL och åtkomsttoken har erhållits från den *förhandla* slutpunkt

SignalR-klient SDK innehåller redan den logik som krävs för att utföra förhandling-handskakning. Skicka förhandla-slutpunkts-URL, minus de `negotiate` segment i SDK `HubConnectionBuilder`. Här är ett exempel i JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Enligt konventionen används SDK lägger automatiskt till `/negotiate` till URL och används för att börja förhandlingen.

> [!NOTE]
> Om du använder JavaScript/TypeScript-SDK i en webbläsare kan du behöva [aktivera resursdelning för korsande ursprung (CORS) för resursdelning](#enabling-cors) på din Funktionsapp.

Mer information om hur du använder SignalR-klientens SDK finns i dokumentationen för ditt språk:

* [.NET-standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Skicka meddelanden från en klient till tjänsten

Även om SignalR SDK vilken klientprogram att anropa backend-logik i SignalR hub, stöds den här funktionen inte ännu när du använder SignalR Service med Azure Functions. Använd HTTP-begäranden för att anropa Azure Functions.

## <a name="azure-functions-configuration"></a>Azure Functions-konfiguration

Azure-funktionsappar som integreras med Azure SignalR Service kan distribueras som alla vanliga Azure-funktionsapp med hjälp av tekniker som [kontinuerligt distribution](../azure-functions/functions-continuous-deployment.md), [zip-distribution](../azure-functions/deployment-zip-push.md), och [kör från paketet](../azure-functions/run-functions-from-deployment-package.md).

Det finns dock några speciella överväganden för appar som använder SignalR Service-bindningar. Om klienten körs i en webbläsare, måste CORS aktiveras. Och om appen kräver autentisering, kan du integrera förhandla slutpunkten med App Service-autentisering.

### <a name="enabling-cors"></a>Aktivera CORS

JavaScript/TypeScript-klienten skickar HTTP-begäranden till funktionen förhandla att initiera anslutningen-förhandling. När klientprogrammet finns på en annan domän än Azure Function-app, cross-origin resource sharing (CORS) måste vara aktiverat på funktionsappen eller webbläsaren blockeras begäranden.

#### <a name="localhost"></a>Localhost

När du kör appen på din lokala dator, kan du lägga till en `Host` avsnitt *local.settings.json* aktiverar du CORS. I den `Host` lägger du till två egenskaper:

* `CORS` – Ange den grundläggande Webbadressen som är ursprunget klientprogrammet
* `CORSCredentials` -Ange det till `true` att tillåta ”withCredentials” begäranden

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

#### <a name="azure"></a>Azure

Om du vill aktivera CORS på en Azure-funktionsapp går du till CORS-konfigurationsskärmen under den *plattformsfunktioner* fliken på din funktionsapp i Azure-portalen.

CORS med Access-Control-Tillåt-autentiseringsuppgifter måste aktiveras att anropa funktionen förhandla SignalR-klienten. Markera kryssrutan för att aktivera den.

I den *tillåtna ursprung* lägger du till en post med ursprung bas-URL för ditt webbprogram.

![Konfigurera CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Med hjälp av App Service-autentisering

Azure Functions har inbyggd autentisering, stöd för populära leverantörer, till exempel Facebook, Twitter, Account, Google och Azure Active Directory. Den här funktionen kan integreras med den *SignalRConnectionInfo* bindning skapa anslutningar till Azure SignalR Service som autentiserats till ett användar-ID. Ditt program kan skicka meddelanden med den *SignalR* utdatabindning som är riktade till den användar-ID.

Azure-portalen i din funktionsapp *plattformsfunktioner* fliken, öppna den *autentisering/auktorisering* fönstret inställningar. Följ dokumentationen för [Apptjänstautentisering](../app-service/overview-authentication-authorization.md) konfigurera autentisering med hjälp av en identitetsprovider som du önskar.

När du konfigurerat autentiserade HTTP-begäranden inkluderar `x-ms-client-principal-name` och `x-ms-client-principal-id` rubriker som innehåller den autentiserade identiteter användarnamn och användar-ID.

Du kan använda dessa huvuden i din *SignalRConnectionInfo* bindning konfiguration för att skapa autentiserade anslutningar. Här är ett exempel C# förhandla funktion som använder den `x-ms-client-principal-id` rubrik.

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

Du kan sedan skicka meddelanden till användaren genom att ange den `UserId` egenskapen för ett SignalR-meddelande.

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

Information om andra språk finns i den [Azure SignalR Service-bindningar](../azure-functions/functions-bindings-signalr-service.md) för refererar till Azure Functions.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utvecklar och konfigurera SignalR Service program utan server med Azure Functions. Försök att skapa ett program själv med hjälp av snabbstarter eller självstudier på den [SignalR Service översiktssidan](index.yml).