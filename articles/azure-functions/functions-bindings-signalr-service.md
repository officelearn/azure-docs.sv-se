---
title: Azure Functions SignalR-tjänstbindningar
description: Förstå hur du använder SignalR-tjänstbindningar med Azure-funktioner.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523044"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

Den här uppsättningen artiklar förklarar hur du autentiserar och skickar meddelanden i realtid till klienter som är anslutna till [Azure SignalR-tjänsten](https://azure.microsoft.com/services/signalr-service/) med hjälp av SignalR-tjänstbindningar i Azure-funktioner. Azure Functions stöder indata- och utdatabindningar för SignalR Service.

| Åtgärd | Typ |
|---------|---------|
| Returnera tjänstens slutpunkts-URL och åtkomsttoken | [Indatabindning](./functions-bindings-signalr-service-input.md) |
| Skicka SignalR-servicemeddelanden |[Utdatabindning](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i appen Funktioner

### <a name="functions-2x-and-higher"></a>Funktioner 2.x och högre

Att arbeta med utlösaren och bindningarna kräver att du refererar till rätt paket. NuGet-paketet används för .NET-klassbibliotek medan tilläggspaketet används för alla andra programtyper.

| Språk                                        | Lägg till efter...                                   | Anmärkningar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrera [tilläggspaketet]          | [Azure Tools-tillägget] rekommenderas att använda med Visual Studio-kod. |
| C# Script (endast online i Azure-portalen)         | Lägga till en bindning                            | Information om hur du uppdaterar befintliga bindningstillägg utan att behöva publicera om [funktionsappen]finns i Uppdatera tilläggen . |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[förlängningspaket]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools-tillägg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Mer information om hur du konfigurerar och använder SignalR-tjänst och Azure-funktioner tillsammans finns i [Azure Functions utveckling och konfiguration med Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Anteckningsbibliotek (endast Java)

Om du vill använda SignalR-tjänstanteckningarna i Java-funktioner måste du lägga till ett beroende i anslagen för *azure-functions-java-library-signalr* (version 1.0 eller senare) i din *pom.xml-fil.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Nästa steg

- [Returnera tjänstens slutpunkts-URL och åtkomsttoken (indatabindning)](./functions-bindings-signalr-service-input.md)
- [Skicka SignalR-tjänstmeddelanden (utdatabindning)](./functions-bindings-signalr-service-output.md) 
