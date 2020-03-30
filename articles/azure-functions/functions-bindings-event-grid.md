---
title: Azure Event Grid-bindningar för Azure-funktioner
description: Förstå hur du hanterar eventrutnätshändelser i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461087"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid-bindningar för Azure-funktioner

Den här referensen förklarar hur du hanterar [eventrutnätshändelser](../event-grid/overview.md) i Azure Functions. Mer information om hur du hanterar meddelanden i händelserutnät i en HTTP-slutpunkt finns i [Ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md).

Event Grid är en Azure-tjänst som skickar HTTP-begäranden för att meddela dig om händelser som inträffar i *utgivare*. En utgivare är den tjänst eller resurs som kommer från händelsen. Ett Azure blob-lagringskonto är till exempel en utgivare och [en blob-överföring eller borttagning är en händelse](../storage/blobs/storage-blob-event-overview.md). Vissa [Azure-tjänster har inbyggt stöd för publicering av händelser i Event Grid](../event-grid/overview.md#event-sources).

Händelsehanterare tar emot och *bearbetar* händelser. Azure Functions är en av flera [Azure-tjänster som har inbyggt stöd för hantering av Event Grid-händelser](../event-grid/overview.md#event-handlers). I den här referensen lär du dig att använda en händelserutnätsutlösare för att anropa en funktion när en händelse tas emot från Event Grid och använda utdatabindningen för att skicka händelser till ett [anpassat händelserutnät](../event-grid/post-to-custom-topic.md).

Om du vill kan du använda en HTTP-utlösare för att hantera händelser i händelserutnätet. se [Ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md). För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i [CloudEvents-schemat](../event-grid/cloudevents-schema.md#azure-functions). Använd i stället en HTTP-utlösare.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion när en händelseutnätshändelse skickas | [Utlösare](./functions-bindings-event-grid-trigger.md) |
| Skickar en händelse rutnätshändelse |[Utdatabindning](./functions-bindings-event-grid-output.md) |

Koden i den här referensen är standard .NET Core-syntax, som används i Functions version 2.x och senare. Information om syntaxen på 1.x finns i [1.x-funktionsmallarna](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Funktioner 1.x-appar har automatiskt en referens till [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x.

## <a name="next-steps"></a>Nästa steg
* [Köra en funktion när en händelseutnätshändelse skickas](./functions-bindings-event-grid-trigger.md)
* [Skicka en händelse rutnätshändelse](./functions-bindings-event-grid-trigger.md)
