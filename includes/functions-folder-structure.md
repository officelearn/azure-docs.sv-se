---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293835"
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

* [C#-klassbiblioteket (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F #-skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



