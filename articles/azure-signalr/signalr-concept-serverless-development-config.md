---
title: Utveckla & konfigurera Azure Functions app – Azure-SignalR
description: Information om hur du utvecklar och konfigurerar program utan server i real tid med hjälp av Azure Functions-och Azure SignalR-tjänsten
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523178"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions-utveckling och -konfiguration med Azure SignalR Service

Azure Functions program kan utnyttja [Azure SignalR-tjänstens bindningar](../azure-functions/functions-bindings-signalr-service.md) för att lägga till funktioner i real tid. Klient program använder klient-SDK: er tillgängliga på flera språk för att ansluta till Azure SignalR-tjänsten och ta emot meddelanden i real tid.

Den här artikeln beskriver koncepten för att utveckla och konfigurera en Azure Function-app som är integrerad med SignalR-tjänsten.

## <a name="signalr-service-configuration"></a>Signalerar tjänst konfiguration

Azure SignalR service kan konfigureras i olika lägen. När den används med Azure Functions måste tjänsten konfigureras i *Server* lös läge.

Leta upp sidan *Inställningar* för signal tjänst resursen i Azure Portal. Ange *tjänst läget* till *Server*lös.

![SignalR service läge](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-utveckling

Ett serverlöst realtidsprogram som skapats med Azure Functions och Azure SignalR-tjänsten kräver vanligtvis två Azure Functions:

* En ”negotiate-funktion” som klienten anropar för att hämta en giltig SignalR Service-åtkomsttoken och en tjänstslutpunkt-URL
* En eller flera funktioner som skickar meddelanden eller hanterar gruppmedlemskap

### <a name="negotiate-function"></a>funktionen Negotiate

Ett klient program kräver en giltig åtkomsttoken för att ansluta till Azure SignalR-tjänsten. En åtkomsttoken kan vara anonym eller autentiserad för ett angivet användar-ID. Det krävs en HTTP-slutpunkt med namnet "förhandla" för att hämta en token och annan anslutnings information, till exempel URL: en för SignalR-tjänstens slut punkt.

Använd en HTTP-utlöst Azure-funktion och *SignalRConnectionInfo* -indata-bindningen för att generera objektet anslutnings information. Funktionen måste ha en HTTP-väg som slutar med `/negotiate`.

Mer information om hur du skapar Negotiate-funktionen finns i referens för [ *SignalRConnectionInfo* -indata-bindning](../azure-functions/functions-bindings-signalr-service-input.md).

Information om hur du skapar en autentiserad token finns i [använda App Service autentisering](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Skicka meddelanden och hantera grupp medlemskap

Använd *signal* flödets utgående bindning för att skicka meddelanden till klienter som är anslutna till Azure SignalR-tjänsten. Du kan skicka meddelanden till alla klienter, eller så kan du skicka dem till en delmängd klienter som autentiseras med ett angivet användar-ID eller som har lagts till i en viss grupp.

Användare kan läggas till i en eller flera grupper. Du kan också använda *signal* flödets utgående bindning för att lägga till eller ta bort användare till/från grupper.

Mer information finns i [bindnings referens för *SignalR* -utdata](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Signals Hub

SignalR har begreppet "hubbar". Varje klient anslutning och varje meddelande som skickas från Azure Functions begränsas till en speciell hubb. Du kan använda hubbar som ett sätt att separera dina anslutningar och meddelanden till logiska namn områden.

## <a name="client-development"></a>Klient utveckling

Signaler klient program kan använda SignalR klient-SDK på ett av flera språk för att enkelt ansluta till och ta emot meddelanden från Azure SignalR-tjänsten.

### <a name="configuring-a-client-connection"></a>Konfigurera en klient anslutning

För att ansluta till signal tjänsten måste en klient slutföra en lyckad anslutnings förhandling som består av följande steg:

1. Gör en begäran till den *förhandlande* http-slutpunkt som beskrivs ovan för att få giltig anslutnings information
1. Ansluta till signal tjänsten med tjänstens slut punkts-URL och åtkomsttoken som hämtats från *Negotiate* -slutpunkten

SignalR klient-SDK: er innehåller redan den logik som krävs för att utföra förhandlings hand skakningen. Överför URL: en för Negotiate-slutpunkten, minus `negotiate` segmentet, till `HubConnectionBuilder`SDK: n. Här är ett exempel i Java Script:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per konvention läggs SDK automatiskt `/negotiate` till i URL: en och använder den för att påbörja förhandlingen.

> [!NOTE]
> Om du använder Java Script/TypeScript SDK i en webbläsare måste du [Aktivera resurs delning mellan ursprung (CORS)](#enabling-cors) på din Funktionsapp.

Mer information om hur du använder SignalR klient-SDK finns i dokumentationen för ditt språk:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Skicka meddelanden från en klient till tjänsten

Även om SDK: n för signalerar-SDK gör det möjligt för klient program att anropa Server dels logiken i en Signals Hub, stöds inte den här funktionen ännu när du använder SignalR-tjänsten med Azure Functions. Använd HTTP-begäranden för att anropa Azure Functions.

## <a name="azure-functions-configuration"></a>Azure Functions konfiguration

Azure Function-appar som integreras med Azure SignalR-tjänsten kan distribueras som en typisk Azure Function-app med hjälp av tekniker som [kontinuerlig distribution](../azure-functions/functions-continuous-deployment.md), [zip-distribution](../azure-functions/deployment-zip-push.md)och [Kör från paket](../azure-functions/run-functions-from-deployment-package.md).

Det finns dock några särskilda överväganden för appar som använder sig av tjänst bindningarna för SignalR. Om klienten körs i en webbläsare måste CORS vara aktive rad. Om appen kräver autentisering kan du integrera förhandlings slut punkten med App Service autentisering.

### <a name="enabling-cors"></a>Aktivera CORS

JavaScript/TypeScript-klienten gör HTTP-förfrågningar till Negotiate-funktionen för att initiera anslutnings förhandlingen. När klient programmet finns i en annan domän än Azure Function-appen måste resurs delning mellan ursprung (CORS) aktive ras på Function-appen eller så blockerar webbläsaren förfrågningarna.

#### <a name="localhost"></a>Värd

När du kör Function-appen på den lokala datorn kan du lägga till `Host` ett avsnitt i *Local. Settings. JSON* för att aktivera CORS. I `Host` avsnittet lägger du till två egenskaper:

* `CORS`-Ange bas-URL: en som är det ursprungliga klient programmet
* `CORSCredentials`– Ställ in den `true` på för att tillåta "withCredentials"-begär Anden

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

#### <a name="cloud---azure-functions-cors"></a>Cloud-Azure Functions CORS

Om du vill aktivera CORS i en Azure Function-app går du till skärmen CORS Configuration på fliken *plattforms funktioner* i din Function-app i Azure Portal.

> [!NOTE]
> CORS-konfigurationen är ännu inte tillgänglig i Azure Functions Linux-förbruknings plan. Använd [Azure API Management](#cloud---azure-api-management) för att aktivera CORS.

CORS med Access-Control-Allow-credentials måste vara aktiverat för att signal klienten ska kunna anropa Negotiate-funktionen. Markera kryss rutan för att aktivera den.

I avsnittet *tillåtna ursprung* lägger du till en post med ursprungs bas-URL: en för ditt webb program.

![Konfigurerar CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud – Azure-API Management

Azure API Management tillhandahåller en API-gateway som lägger till funktioner i befintliga Server dels tjänster. Du kan använda den för att lägga till CORS i din Function-app. Den erbjuder en förbruknings nivå med priser för betalning per åtgärd och en månatlig kostnads fri beviljande.

I API Management-dokumentationen hittar du information om hur du [importerar en Azure Function-app](../api-management/import-function-app-as-api.md). När du har importerat kan du lägga till en inkommande princip för att aktivera CORS med åtkomst kontroll-Tillåt-autentiseringsuppgifter-stöd.

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

Konfigurera dina signalerande klienter så att de använder API Management-URL: en.

### <a name="using-app-service-authentication"></a>Använda App Service autentisering

Azure Functions har inbyggd autentisering som stöder populära leverantörer som Facebook, Twitter, Microsoft-konto, Google och Azure Active Directory. Den här funktionen kan integreras med *SignalRConnectionInfo* -bindningen för att skapa anslutningar till Azure SignalR-tjänsten som har autentiserats till ett användar-ID. Ditt program kan skicka meddelanden med hjälp av *signal* flödes bindningen som är mål för det användar-ID: t.

Öppna fönstret inställningar för *autentisering/auktorisering* i den Azure Portal på fliken *plattform funktioner* i Function-appen. Följ dokumentationen för [App Service-autentisering](../app-service/overview-authentication-authorization.md) för att konfigurera autentisering med en identitetsprovider som du själv väljer.

När det har kon figurer ATS tas autentiserade `x-ms-client-principal-name` http `x-ms-client-principal-id` -förfrågningar med och rubriker som innehåller den autentiserade identitetens användar namn och användar-ID.

Du kan använda de här rubrikerna i din *SignalRConnectionInfo* -bindnings konfiguration för att skapa autentiserade anslutningar. Här är ett exempel på en funktion i `x-ms-client-principal-id` C# Negotiate som använder-rubriken.

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

Du kan sedan skicka meddelanden till användaren genom att `UserId` ange egenskapen för ett signal meddelande.

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

Information om andra språk finns i [bindningar för Azure SignalR-tjänsten](../azure-functions/functions-bindings-signalr-service.md) för Azure Functions referens.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utvecklar och konfigurerar serverbaserade signal tjänst program med hjälp av Azure Functions. Försök att skapa ett program själv med någon av snabb starterna eller självstudierna på [översikts sidan för SignalR-tjänsten](index.yml).