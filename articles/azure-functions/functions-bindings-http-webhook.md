---
title: HTTP-utlösare och bindningar i Azure Functions
description: Lär dig att använda HTTP-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462113"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP-utlösare och bindningar översikt

Azure Functions kan anropas via HTTP-begäranden om att skapa serverlösa API:er och svara på [webhooks](https://en.wikipedia.org/wiki/Webhook).

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion från en HTTP-begäran | [Utlösare](./functions-bindings-http-webhook-trigger.md) |
| Returnera ett HTTP-svar från en funktion |[Utdatabindning](./functions-bindings-http-webhook-output.md) |

Koden i den här artikeln används som standard .NET Core-syntax, som används i Functions version 2.x och senare. Information om syntaxen på 1.x finns i [1.x-funktionsmallarna](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Lägg till i appen Funktioner

### <a name="functions-2x-and-higher"></a>Funktioner 2.x och högre

Att arbeta med utlösaren och bindningarna kräver att du refererar till rätt paket. NuGet-paketet används för .NET-klassbibliotek medan tilläggspaketet används för alla andra programtyper.

| Språk                                        | Lägg till efter...                                   | Anmärkningar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrera [tilläggspaketet]          | [Azure Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas att använda med Visual Studio-kod. |
| C# Script (endast online i Azure-portalen)         | Lägga till en bindning                            | Information om hur du uppdaterar befintliga bindningstillägg utan att behöva publicera om [funktionsappen]finns i Uppdatera tilläggen . |

[core tools]: ./functions-run-local.md
[förlängningspaket]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Funktioner 1.x-appar har automatiskt en referens till [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x.

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion från en HTTP-begäran](./functions-bindings-http-webhook-trigger.md)
- [Returnera ett HTTP-svar från en funktion](./functions-bindings-http-webhook-output.md)
