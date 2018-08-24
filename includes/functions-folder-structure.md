---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811613"
---
Koden för alla funktioner i en viss funktionsapp finns i rotmappen för en (`wwwroot`) som innehåller en konfigurationsfil för värden och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion, som i följande exempel:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Host.json-filen innehåller vissa runtime-specifika konfigurationer och är placerad i rotmappen för funktionsappen. Information om inställningar som är tillgängliga finns i den [referens för host.json](../articles/azure-functions/functions-host-json.md).

Varje funktion har en mapp som innehåller en eller flera kodfiler, function.json-konfiguration och andra beroenden. För en C# klassbiblioteksprojektet, filen kompilerade class library (.dll) har distribuerats till den `bin` undermappen.

