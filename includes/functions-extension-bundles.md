---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639117"
---
Det enklaste sättet att installera bindnings tillägg är att aktivera [paket för tillägg](../articles/azure-functions/functions-bindings-register.md#extension-bundles). När du aktiverar paket installeras en fördefinierad uppsättning tilläggs paket automatiskt.

Om du vill aktivera tilläggs paket öppnar du Host. JSON-filen och uppdaterar innehållet så att det matchar följande kod:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```