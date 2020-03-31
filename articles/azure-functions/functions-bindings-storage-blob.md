---
title: Azure Blob storage-utlösare och bindningar för Azure Functions
description: Lär dig att använda Azure Blob-lagringsutlösaren och bindningarna i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277237"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob storage bindningar för Azure Functions översikt

Azure Functions integreras med [Azure Storage](https://docs.microsoft.com/azure/storage/) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Blob-lagring kan du skapa funktioner som reagerar på ändringar i blob-data samt läs- och skrivvärden.

| Åtgärd | Typ |
|---------|---------|
| Köra en funktion som blob lagringsdata ändringar | [Utlösare](./functions-bindings-storage-blob-trigger.md) |
| Läsa bloblagringsdata i en funktion | [Indatabindning](./functions-bindings-storage-blob-input.md) |
| Tillåt att en funktion skriver bloblagringsdata |[Utdatabindning](./functions-bindings-storage-blob-output.md) |

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

- [Köra en funktion när blob-lagringsdata ändras](./functions-bindings-storage-blob-trigger.md)
- [Läsa bloblagringsdata när en funktion körs](./functions-bindings-storage-blob-input.md)
- [Skriva bloblagringsdata från en funktion](./functions-bindings-storage-blob-output.md)
