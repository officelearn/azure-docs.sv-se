---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731251"
---
Koden för alla funktioner i en specifik funktionsapp finns i en rotmapp för projekt som innehåller en konfigurationsfil för värden och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion, som i följande bild:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

I version 2.x av funktionskörningen alla funktioner i funktionsappen måste dela samma språk worker.  

Den [host.json](../articles/azure-functions/functions-host-json.md) filen som innehåller vissa runtime-specifika konfigurationer finns i rotmappen för funktionsappen. En `bin` mappen innehåller paket och andra library-filer som krävs av funktionsappen. Se kraven för ett funktionsapprojekt språkspecifika:

- [C#-klassbiblioteket (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [F#skriptet](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

