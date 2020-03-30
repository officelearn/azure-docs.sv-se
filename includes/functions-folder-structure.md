---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205741"
---
Koden för alla funktioner i en viss funktionsapp finns i en rotprojektmapp som innehåller en värdkonfigurationsfil och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion. Mappstrukturen visas i följande representation:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

I version 2.x och högre av funktionskörningen måste alla funktioner i funktionsappen dela samma språkstack.  

[Filen host.json](../articles/azure-functions/functions-host-json.md) innehåller körningsspecifika konfigurationer och finns i rotmappen för funktionsappen. En *lagerplatsmapp* innehåller paket och andra biblioteksfiler som funktionsappen kräver. Se de språkspecifika kraven för ett funktionsappprojekt:

* [C# klassbibliotek (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [Javascript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
