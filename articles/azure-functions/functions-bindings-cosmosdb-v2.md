---
title: Azure Cosmos DB-bindningar för funktioner 2.x
description: Förstå hur du använder Azure Cosmos DB-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605770"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB-utlösare och bindningar för Azure Functions 2.x översikt

> [!div class="op_single_selector" title1="Välj den version av Azure Functions-körningen som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här uppsättningen artiklar förklarar hur du arbetar med [Azure Cosmos](../cosmos-db/serverless-computing-database.md) DB-bindningar i Azure Functions 2.x. Azure Functions stöder utlösande, indata och utdatabindningar för Azure Cosmos DB.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion när ett Azure Cosmos DB-dokument skapas eller ändras | [Utlösare](./functions-bindings-cosmosdb-v2-trigger.md) |
| Läsa ett Azure Cosmos DB-dokument | [Indatabindning](./functions-bindings-cosmosdb-v2-input.md) |
| Spara ändringar i ett Azure Cosmos DB-dokument  |[Utdatabindning](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Den här referensen gäller [Azure Functions version 2.x](functions-versions.md).  Information om hur du använder dessa bindningar i Functions 1.x finns i [Azure Cosmos DB-bindningar för Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Den här bindningen hette ursprungligen DocumentDB. I Functions version 2.x heter utlösaren, bindningarna och paketet Cosmos DB.

## <a name="supported-apis"></a>API:er som stöds

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Lägg till i appen Funktioner

### <a name="functions-2x-and-higher"></a>Funktioner 2.x och högre

Att arbeta med utlösaren och bindningarna kräver att du refererar till rätt paket. NuGet-paketet används för .NET-klassbibliotek medan tilläggspaketet används för alla andra programtyper.

| Språk                                        | Lägg till efter...                                   | Anmärkningar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrera [tilläggspaketet]          | [Azure Tools-tillägget] rekommenderas att använda med Visual Studio-kod. |
| C# Script (endast online i Azure-portalen)         | Lägga till en bindning                            | Information om hur du uppdaterar befintliga bindningstillägg utan att behöva publicera om [funktionsappen]finns i Uppdatera tilläggen . |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[förlängningspaket]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./install-update-binding-extensions-manual.md
[Azure Tools-tillägg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Funktioner 1.x-appar har automatiskt en referens till [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x.

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Läsa ett Azure Cosmos DB-dokument (indatabindning)](./functions-bindings-cosmosdb-v2-input.md)
- [Spara ändringar i ett Azure Cosmos DB-dokument (utdatabindning)](./functions-bindings-cosmosdb-v2-output.md)
