---
title: Azure Service Bus-bindningar för Azure-funktioner
description: Lär dig att skicka Azure Service Bus-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277419"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindningar för Azure-funktioner

Azure Functions integreras med [Azure Service Bus](https://azure.microsoft.com/services/service-bus) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Service Bus kan du skapa funktioner som reagerar på och skicka kö- eller ämnesmeddelanden.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion när en servicebusskö eller ett ämnesmeddelande skapas | [Utlösare](./functions-bindings-service-bus-trigger.md) |
| Skicka Azure Service Bus-meddelanden |[Utdatabindning](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i appen Funktioner

### <a name="functions-2x-and-higher"></a>Funktioner 2.x och högre

Att arbeta med utlösaren och bindningarna kräver att du refererar till rätt paket. NuGet-paketet används för .NET-klassbibliotek medan tilläggspaketet används för alla andra programtyper.

| Språk                                        | Lägg till efter...                                   | Anmärkningar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrera [tilläggspaketet]          | [Azure Tools-tillägget] rekommenderas att använda med Visual Studio-kod. |
| C# Script (endast online i Azure-portalen)         | Lägga till en bindning                            | Information om hur du uppdaterar befintliga bindningstillägg utan att behöva publicera om [funktionsappen]finns i Uppdatera tilläggen . |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[förlängningspaket]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools-tillägg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Funktioner 1.x-appar har automatiskt en referens till [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x.

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion när en servicebusskö eller ett ämnesmeddelande skapas (utlösare)](./functions-bindings-service-bus-trigger.md)
- [Skicka Azure Service Bus-meddelanden från Azure Functions (utdatabindning)](./functions-bindings-service-bus-output.md)
