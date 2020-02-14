---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205739"
---
Lägg till stöd i önskad utvecklings miljö med hjälp av följande metoder.

| Utvecklingsmiljö  | Program typ      | För att lägga till stöd |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#klass bibliotek      | [Installera NuGet-paketet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Baserat på [kärn verktyg](../articles/azure-functions/functions-run-local.md) | [Registrera tilläggs paketet](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Vi rekommenderar att du installerar [tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Andra redigerare/IDE     | Baserat på [kärn verktyg](../articles/azure-functions/functions-run-local.md) | [Registrera tilläggs paketet](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure-portalen             | Endast online i portalen | Installeras när du lägger till en bindning<br /><br /> Se [Uppdatera dina tillägg](../articles/azure-functions/install-update-binding-extensions-manual.md) för att uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app. |
