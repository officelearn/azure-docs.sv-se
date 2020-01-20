---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279422"
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
