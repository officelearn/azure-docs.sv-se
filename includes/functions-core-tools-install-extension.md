---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564718"
---
## <a name="register-extensions"></a>Registrera tillägg

Med undantag för HTTP-och timer-utlösare, är funktions bindningar i körnings version 2. x och högre implementerade som tilläggs paket. I version 2. x och senare av Azure Functions runtime måste du explicit registrera tilläggen för de bindnings typer som används i funktionerna. Undantagen till detta är HTTP-bindningar och timer-utlösare, vilket inte kräver tillägg.

Du kan välja att installera bindnings tillägg individuellt eller lägga till en paket referens i Host. JSON-projektfilen. Paket för tillägg tar bort risken för problem med att paketera kompatibilitetsproblem när du använder flera bindnings typer. Det är den rekommenderade metoden för att registrera bindnings tillägg. Tilläggs paketen tar också bort kravet på att installera .NET Core 2. x SDK. 

### <a name="extension-bundles"></a>Paket för tillägg

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Läs mer i [registrera Azure Functions bindnings tillägg](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Du bör lägga till tilläggs paket i Host. JSON innan du lägger till bindningar i function. JSON-filen.

### <a name="register-individual-extensions"></a>Registrera enskilda tillägg

Om du behöver installera tillägg som inte ingår i ett paket kan du registrera enskilda tilläggs paket manuellt för specifika bindningar. 

> [!NOTE]
> Om du vill registrera tillägg manuellt `func extensions install`med hjälp av måste du ha .net Core 2. x SDK installerat.

När du har uppdaterat din *Function. JSON* -fil för att inkludera alla bindningar som din funktion behöver kör du följande kommando i projektmappen.

```bash
func extensions install
```

Kommandot läser filen *Function. JSON* för att se vilka paket du behöver, installerar dem och återskapa tilläggs projektet. Den lägger till eventuella nya bindningar i den aktuella versionen men uppdaterar inte befintliga bindningar. Använd `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.
