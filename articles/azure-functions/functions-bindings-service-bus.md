---
title: Azure Service Bus bindningar för Azure Functions
description: Lär dig att skicka Azure Service Bus utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e15dfec049197fa056cbd55fd839b3eb93be77c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530377"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus bindningar för Azure Functions

Azure Functions integreras med [Azure Service Bus](https://azure.microsoft.com/services/service-bus) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Service Bus kan du bygga funktioner som reagerar på och skickar kö-eller ämnes meddelanden.

| Action | Typ |
|---------|---------|
| Köra en funktion när en Service Bus kö eller ett ämnes meddelande skapas | [Utlösare](./functions-bindings-service-bus-trigger.md) |
| Skicka Azure Service Bus meddelanden |[Utgående bindning](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

> [!NOTE]
> Service Bus-bindningen stöder för närvarande inte autentisering med en hanterad identitet. Använd i stället en [Service Bus signatur för delad åtkomst](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 4. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg] rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när en Service Bus kö eller ett ämnes meddelande skapas (utlösare)](./functions-bindings-service-bus-trigger.md)
- [Skicka Azure Service Bus meddelanden från Azure Functions (utgående bindning)](./functions-bindings-service-bus-output.md)
