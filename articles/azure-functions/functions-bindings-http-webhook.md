---
title: Azure Functions HTTP-utlösare och bindningar
description: Lär dig att använda HTTP-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462113"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Översikt över Azure Functions HTTP-utlösare och bindningar

Azure Functions kan anropas via HTTP-begäranden för att bygga Server lösa API: er och svara på [Webhooks](https://en.wikipedia.org/wiki/Webhook).

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion från en HTTP-begäran | [Utlösare](./functions-bindings-http-webhook-trigger.md) |
| Returnera ett HTTP-svar från en funktion |[Utgående bindning](./functions-bindings-http-webhook-output.md) |

Koden i den här artikeln använder .NET Core-syntaxen som standard, som används i functions version 2. x och högre. Information om syntaxen för 1. x finns i [mallarna för 1. x-funktioner](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Funktioner 2. x och högre

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Anmärkningar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paket], version 3. x | |
| C#Skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas för användning med Visual Studio Code. |
| C#Skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[core tools]: ./functions-run-local.md
[Tilläggs paketet]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion från en HTTP-begäran](./functions-bindings-http-webhook-trigger.md)
- [Returnera ett HTTP-svar från en funktion](./functions-bindings-http-webhook-output.md)
