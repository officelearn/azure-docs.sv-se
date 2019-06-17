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
ms.openlocfilehash: 9f74365f3fe935be45fa9c45e5b12c45b97b2f8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068345"
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

I version 2.x av funktionskörningen alla funktioner i funktionsappen måste dela samma språk-stacken.  

Den [host.json](../articles/azure-functions/functions-host-json.md) filen som innehåller vissa runtime-specifika konfigurationer finns i rotmappen för funktionsappen. En `bin` mappen innehåller paket och andra library-filer som krävs av funktionsappen. Se kraven för ett funktionsapprojekt språkspecifika:

* [C#-klassbiblioteket (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#skriptet](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



