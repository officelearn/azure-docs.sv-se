---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
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

Följande egenskaper är tillgängliga i `extensionBundle`:

| Egenskap | Beskrivning |
| -------- | ----------- |
| id | Namnområdet för Microsoft Azure Functions-tilläggsbuntar. |
| version | Den version av bunten som ska installeras. Körmiljön för Functions väljer alltid den högsta tillåtna versionen som definieras av versionsintervallet. Versionsvärdet ovan tillåter alla buntversioner från 1.0.0 upp till men inte inklusive 2.0.0. Mer information finns in [intervallnotationen för att ange versionsintervall](/nuget/reference/package-versioning#version-ranges). |

Buntversioner ökar när paket i bunten ändras. Högre versionsändringar sker när paket i bunten ökar med en högre version. Högre versionsändringar i bunten sammanfaller vanligtvis med en ändring i den högre versionen av körmiljön för Functions.  

Den aktuella uppsättningen tillägg som installeras av standardbunten räknas upp i den här [extensions.json-filen](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
