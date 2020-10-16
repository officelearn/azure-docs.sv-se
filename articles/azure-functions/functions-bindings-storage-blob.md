---
title: Azure Blob Storage-utlösare och bindningar för Azure Functions
description: Lär dig hur du använder Azure Blob Storage-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104367"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob Storage-bindningar för Azure Functions översikt

Azure Functions integreras med [Azure Storage](../storage/index.yml) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Blob Storage kan du skapa funktioner som reagerar på ändringar i BLOB-data samt läsa och skriva värden.

| Åtgärd | Typ |
|---------|---------|
| Kör en funktion som data ändringar i Blob Storage | [Utlösare](./functions-bindings-storage-blob-trigger.md) |
| Läsa Blob Storage-data i en funktion | [Binda in](./functions-bindings-storage-blob-input.md) |
| Tillåt en funktion att skriva BLOB Storage-data |[Utgående bindning](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när Blob Storage-data ändras](./functions-bindings-storage-blob-trigger.md)
- [Läsa Blob Storage-data när en funktion körs](./functions-bindings-storage-blob-input.md)
- [Skriv Blob Storage-data från en funktion](./functions-bindings-storage-blob-output.md)