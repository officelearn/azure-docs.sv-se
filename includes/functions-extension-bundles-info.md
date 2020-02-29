---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201943"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Under starten hämtar värden och installerar [lagrings bindnings tillägget](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) och andra tillägg för Microsoft-bindning. Den här installationen sker eftersom bindnings tillägg är aktiverade som standard i *Host. JSON* -filen med följande egenskaper:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Om du stöter på fel som rör bindnings tillägg kontrollerar du att egenskaperna ovan finns i *Host. JSON*.
::: zone-end  