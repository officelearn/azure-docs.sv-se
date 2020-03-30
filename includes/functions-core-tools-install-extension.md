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

Med undantag för HTTP- och timerutlösare implementeras funktionsbindningar i runtime version 2.x och högre som tilläggspaket. I version 2.x och utöver Azure Functions-körningen måste du uttryckligen registrera tilläggen för de bindningstyper som används i dina funktioner. Undantagen från detta är HTTP-bindningar och timerutlösare, som inte kräver tillägg.

Du kan välja att installera bindningstillägg individuellt eller lägga till en tilläggspaketreferens till projektfilen host.json. Tilläggspaket tar bort risken för paketkompatibilitetsproblem när du använder flera bindningstyper. Det är den rekommenderade metoden för registrering av bindande tillägg. Tilläggspaket tar också bort kravet på installation av .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Förlängningspaket

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Mer information finns i [Registrera Bindningstillägg för Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Du bör lägga till tilläggspaket till host.json innan du lägger till bindningar i filen function.json.

### <a name="register-individual-extensions"></a>Registrera enskilda tillägg

Om du behöver installera tillägg som inte finns i ett paket kan du manuellt registrera enskilda tilläggspaket för specifika bindningar. 

> [!NOTE]
> Om du vill registrera `func extensions install`tillägg manuellt med hjälp av måste du ha .NET Core 2.x SDK installerat.

När du har uppdaterat *filen function.json* så att den innehåller alla bindningar som funktionen behöver kör du följande kommando i projektmappen.

```bash
func extensions install
```

Kommandot läser *filen function.json* för att se vilka paket du behöver, installerar dem och återskapar tilläggsprojektet. Den lägger till nya bindningar i den aktuella versionen men uppdaterar inte befintliga bindningar. Använd `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.
