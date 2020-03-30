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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205739"
---
Lägg till stöd i du föredrog utvecklingsmiljö med hjälp av följande metoder.

| Utvecklingsmiljö  | Programtyp      | Så här lägger du till stöd |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# klassbibliotek      | [Installera NuGet-paketet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio-koden       | Baserat på [kärnverktyg](../articles/azure-functions/functions-run-local.md) | [Registrera förlängningspaketet](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Installation av [Azure Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas. |
| Någon annan redaktör/IDE     | Baserat på [kärnverktyg](../articles/azure-functions/functions-run-local.md) | [Registrera förlängningspaketet](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Endast online i portalen | Installeras när du lägger till en bindning<br /><br /> Se [Uppdatera dina tillägg](../articles/azure-functions/install-update-binding-extensions-manual.md) för att uppdatera befintliga bindningstillägg utan att behöva publicera om funktionsappen. |
