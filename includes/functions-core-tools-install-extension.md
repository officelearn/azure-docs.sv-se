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
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608381"
---
## <a name="register-extensions"></a>Registrera tillägg

Med undantag för HTTP och timer utlösare fungerar bindningar i körningsversion 2.x implementeras som tilläggspaket. I version 2.x av Azure Functions-runtime som du behöver registrera tillägg för bindningstyper används i dina funktioner. Detta är HTTP-bindningar och timerutlösare, som inte kräver tillägg.

Du kan välja att installera tillägg av bindning individuellt eller du kan lägga till en paket-referens för tillägg till host.json projektfilen. Tillägget paket tar bort risken för paketet kompatibilitetsproblem när du använder flera bindningstyper. Det är den rekommenderade metoden för att registrera tillägg av bindning. Tillägget paket tar också bort kravet för att installera .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>tillägget paket

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Mer information finns i [registrera Azure Functions tillägg av bindning](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Bör du lägga till tillägget paket i host.json innan du lägger till bindningar till filen functions.json.

### <a name="register-individual-extensions"></a>Registrera enskilda tillägg

Om du vill installera tillägg som inte tillhör ett paket kan du registrera enskilda tilläggspaket för specifika bindningar manuellt. 

> [!NOTE]
> Att manuellt registrera tillägg med hjälp av `func extensions install`, måste du ha .NET Core 2.x SDK är installerat.

När du har uppdaterat din *function.json* filen för att inkludera alla bindningar som din funktion behöver, kör följande kommando i projektmappen.

```bash
func extensions install
```

Kommandot läser den *function.json* fil för att se vilka paket som du behöver, installerar dem och återskapar tillägg-projektet. Den lägger till några nya bindningar på den aktuella versionen men uppdaterar inte befintliga bindningar. Använd den `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.