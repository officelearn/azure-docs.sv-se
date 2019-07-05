---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442281"
---
Det enklaste sättet att installera tillägg av bindning är att aktivera [tillägget paket](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Paket installeras automatiskt med paket har aktiverat en fördefinierad uppsättning tillägg.

Om du vill aktivera tillägget paket, öppna den *host.json* filen och uppdatera innehållet för att matcha följande kod:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```