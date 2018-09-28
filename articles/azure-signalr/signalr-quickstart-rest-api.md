---
title: Snabbstart – Azure SignalR Service REST API | Microsoft Docs
description: En snabbstart för att använda Azure SignalR Service REST API.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972767"
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

När servern har startats använder du kommandot för att skicka meddelandet

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Du kan starta flera klienter med olika klientnamn.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
