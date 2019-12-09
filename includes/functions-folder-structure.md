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
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935878"
---
Koden för alla funktioner i en speciell Function-app finns i en rotmapp som innehåller en värd konfigurations fil och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion. Mappstrukturen visas i följande representation:

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

I version 2. x och högre av Functions-körningsmiljön måste alla funktioner i Function-appen dela samma språks tack.  

[Host. JSON](../articles/azure-functions/functions-host-json.md) -filen innehåller körnings bara konfigurationer och finns i rotmappen i Function-appen. En *bin* -mapp innehåller paket och andra biblioteks filer som funktions programmet kräver. Se språkspecifika krav för ett Function app-projekt:

* [C#klass bibliotek (. CSPROJ)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#över](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
