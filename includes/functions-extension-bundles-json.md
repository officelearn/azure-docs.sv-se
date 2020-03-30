---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79382056"
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

Följande egenskaper finns `extensionBundle`i:

| Egenskap | Beskrivning |
| -------- | ----------- |
| id | Namnområdet för Microsoft Azure Functions-tilläggspaket. |
| version | Den version av paketet som ska installeras. Funktionskörningen väljer alltid den högsta tillåtna versionen som definieras av versionsintervallet eller intervallet. Versionsvärdet ovan tillåter alla paketversioner från 1.0.0 upp till men exklusive 2.0.0. Mer information finns i [intervallaktionen för att ange versionsintervall](/nuget/reference/package-versioning#version-ranges). |

Paketversionerna ökar när paketen i paketet ändras. Huvudversionsändringar inträffar när paket i paketet ökar med en huvudversion. Viktiga versionsändringar i paketet sammanfaller vanligtvis med en ändring i huvudversionen av funktionskörningen.  

Den aktuella uppsättningen tillägg som installeras av standardpaketet räknas upp i den här [filen extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
