---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201943"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Under starten hämtar och installerar värden [tillägget Storage-bindning](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) och andra Microsoft-bindningstillägg. Den här installationen sker eftersom bindningstillägg är aktiverade som standard i *filen host.json* med följande egenskaper:
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
> Om du stöter på några fel relaterade till bindningstillägg kontrollerar du att ovanstående egenskaper finns i *host.json*.
::: zone-end  