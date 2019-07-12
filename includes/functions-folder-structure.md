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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594542"
---
Koden för alla funktioner i en specifik funktionsapp finns i en rotmapp för projekt som innehåller en konfigurationsfil för värden och en eller flera undermappar. Varje undermapp innehåller kod för en separat funktion. Mappstrukturen visas i följande representation:

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

Den [host.json](../articles/azure-functions/functions-host-json.md) filen innehåller runtime-specifika konfigurationer och finns i rotmappen för funktionsappen. En *bin* mappen innehåller paket och andra biblioteksfiler som kräver att funktionsappen. Se kraven för ett funktionsapprojekt språkspecifika:

* [C#-klassbiblioteket (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#skriptet](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



