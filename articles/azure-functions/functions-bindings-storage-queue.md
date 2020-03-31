---
title: Azure Queue storage-utlösare och bindningar för Azure Functions översikt
description: Förstå hur du använder Azure Queue storage-utlösaren och utdatabindningen i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277315"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue storage-utlösare och bindningar för Azure Functions översikt

Azure Functions kan köras när nya Azure Queue storage-meddelanden skapas och kan skriva kömeddelanden i en funktion.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion när kölagringsdata ändras | [Utlösare](./functions-bindings-storage-queue-trigger.md) |
| Skriva kölagringsmeddelanden |[Utdatabindning](./functions-bindings-storage-queue-output.md) |

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
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Funktioner 1.x-appar har automatiskt en referens till [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion som ändringar av kölagringsdata (utlösare)](./functions-bindings-storage-queue-trigger.md)
- [Skriva kölagringsmeddelanden (utdatabindning)](./functions-bindings-storage-queue-output.md)
