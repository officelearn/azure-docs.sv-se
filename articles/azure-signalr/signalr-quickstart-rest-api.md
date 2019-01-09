---
title: Snabbstart – Azure SignalR Service REST API
description: En snabbstart för att använda Azure SignalR Service REST API.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 1443508985c7c7fef313a7eea3f622e7759a71ef
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540104"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Snabbstart: Sända realtidsmeddelanden från konsolapp

Azure SignalR Service har [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) för att stödja server till klient-kommunikationsscenarier, till exempel sändning. Du kan använda valfritt programmeringsspråk som göra REST API-anrop. Du kan publicera meddelanden till alla anslutna klienter, en specifik klient per namn eller en grupp med klienter.

I den här snabbstarten får du lära dig hur du sänder meddelanden från en kommandoradsapp till anslutna klientappar i C#.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten kan köras på macOS, Windows eller Linux.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Valfri textredigerare eller kodredigerare.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras ska vi förbereda koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples.git), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Det här exemplet är en konsolapp som visar användningen av Azure SignalR Service. Den har två lägen:

- Serverläge: använder enkla kommandon för att anropa Azure SignalR Service REST API.
- Klientläge: anslut till Azure SignalR Service och få meddelanden från servern.

Du kan även hitta hur du skapar en åtkomsttoken för autentisering med Azure SignalR Service.

### <a name="build-the-executable-file"></a>Skapa den körbara filen

Vi använder macOS osx.10.13-x64 som exempel. Det finns [referenser](https://docs.microsoft.com/dotnet/core/rid-catalog) till hur du bygger på andra plattformar.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Starta en klient

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Starta en server

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Kör exemplet utan att publicera

Du kan även köra kommandot nedan för att starta en server eller klient

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Använda användarhemligheter för att ange anslutningssträng

Du kan köra `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` i exemplets rotkatalog. Efter det behöver du inte alternativet `-c "<ConnectionString>"` längre.

## <a name="usage"></a>Användning

När servern har startats använder du kommandot för att skicka meddelandet:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Du kan starta flera klienter med olika klientnamn.

## <a name="usage"> </a> Integrering med tjänster från tredje part

Azure SignalR-tjänsten tillåter tredjepartstjänster att integrera med systemet.

### <a name="definition-of-technical-specifications"></a>Definition av tekniska specifikationer

Följande tabell visar alla versioner av de REST API:er som stöds hittills. Du hittar även definitionsfilen för varje specifika version

Version | API-tillstånd | Door | Specifik
--- | --- | --- | ---
`1.0-preview` | Tillgängligt | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Tillgängligt | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Listan över tillgängliga API:er för varje specifika version finns i följande lista.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Sänd till alla](#broadcast) | **&#x2713;** | **&#x2713;**
[Sänd till en grupp](#broadcast-group) | **&#x2713;** | **&#x2713;**
Sänd till vissa grupper | **&#x2713;** (inaktuellt) | `N / A`
[Sänd till specifika användare](#send-user) | **&#x2713;** | **&#x2713;**
Skicka till vissa användare | **&#x2713;** (inaktuellt) | `N / A`
[Lägga till användare i en grupp](#add-user-to-group) | `N / A` | **&#x2713;**
[Ta bort en användare från en grupp](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Sänd till alla

Version | API HTTP-metod | URL för begäran | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Samma som ovan

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Sänd till en grupp

Version | API HTTP-metod | URL för begäran | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Samma som ovan

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Skicka till specifika användare

Version | API HTTP-metod | URL för begäran | Begärandetext
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Samma som ovan

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Lägga till användare i en grupp

Version | API HTTP-metod | URL för begäran
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Ta bort en användare från en grupp

Version | API HTTP-metod | URL för begäran
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
