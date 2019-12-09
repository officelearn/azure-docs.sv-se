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
ms.openlocfilehash: a050ce62f745591608249b41ba56992d8fd35204
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935781"
---
## <a name="register-extensions"></a>Registrera tillägg

Med undantag för HTTP-och timer-utlösare, är funktions bindningar i körnings version 2. x och högre implementerade som tilläggs paket. I version 2. x och senare av Azure Functions runtime måste du explicit registrera tilläggen för de bindnings typer som används i funktionerna. Undantagen till detta är HTTP-bindningar och timer-utlösare, vilket inte kräver tillägg.

Du kan välja att installera bindnings tillägg individuellt eller lägga till en paket referens i Host. JSON-projektfilen. Paket för tillägg tar bort risken för problem med att paketera kompatibilitetsproblem när du använder flera bindnings typer. Det är den rekommenderade metoden för att registrera bindnings tillägg. Tilläggs paketen tar också bort kravet på att installera .NET Core 2. x SDK. 

### <a name="extension-bundles"></a>Paket för tillägg

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Läs mer i [registrera Azure Functions bindnings tillägg](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Du bör lägga till tilläggs paket i Host. JSON innan du lägger till bindningar i functions. JSON-filen.

### <a name="register-individual-extensions"></a>Registrera enskilda tillägg

Om du behöver installera tillägg som inte ingår i ett paket kan du registrera enskilda tilläggs paket manuellt för specifika bindningar. 

> [!NOTE]
> Om du vill registrera tillägg manuellt genom att använda `func extensions install`måste du ha .NET Core 2. x SDK installerat.

När du har uppdaterat din *Function. JSON* -fil för att inkludera alla bindningar som din funktion behöver kör du följande kommando i projektmappen.

```bash
func extensions install
```

Kommandot läser filen *Function. JSON* för att se vilka paket du behöver, installerar dem och återskapa tilläggs projektet. Den lägger till eventuella nya bindningar i den aktuella versionen men uppdaterar inte befintliga bindningar. Använd alternativet `--force` om du vill uppdatera befintliga bindningar till den senaste versionen när du installerar nya.