---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129060"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Följande egenskaper är tillgängliga i `extensionBundle`:

| Egenskap | Beskrivning |
| -------- | ----------- |
| id | Namn området för Microsoft Azure Functions-paket för tillägg. |
| version | Den version av paketet som ska installeras. Functions-körningen plockar alltid den högsta tillåtna versionen som definieras av versions intervallet eller intervallet. Version svärdet ovan tillåter alla paket versioner från 1.0.0 upp till men inte inklusive 2.0.0. Mer information finns i [intervall notation för att ange versions intervall](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Paket versioner ökar som paket i paket ändringen. Huvud versions ändringar sker när paket i paketet ökar med en högre version. Huvud versions ändringar i paketet sammanfaller vanligt vis med en ändring i huvud versionen av Functions-körningen.  

Den aktuella uppsättningen tillägg som installeras av standard paketet räknas upp i [filen Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
