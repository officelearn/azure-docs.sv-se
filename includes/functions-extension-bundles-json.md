---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878261"
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

Följande egenskaper är tillgängliga i `extensionBundle` :

| Egenskap | Beskrivning |
| -------- | ----------- |
| id | Namn området för Microsoft Azure Functions-paket för tillägg. |
| version | Den version av paketet som ska installeras. Functions-körningen plockar alltid den högsta tillåtna versionen som definieras av versions intervallet eller intervallet. Version svärdet ovan tillåter alla paket versioner från 1.0.0 upp till men inte inklusive 2.0.0. Mer information finns i [intervall notation för att ange versions intervall](/nuget/reference/package-versioning#version-ranges). |

Paket versioner ökar som paket i paket ändringen. Huvud versions ändringar sker när paket i paketet ökar med en högre version. Huvud versions ändringar i paketet sammanfaller vanligt vis med en ändring i huvud versionen av Functions-körningen.  

Den aktuella uppsättningen tillägg som installeras av standard paketet räknas upp i den här [extensions.jsi filen](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
