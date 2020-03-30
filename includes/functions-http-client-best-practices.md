---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d5de8efc62984b9738c9247b12225f35265309c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187111"
---
> [!TIP]
>
> Om du planerar att använda HTTP- eller WebHook-bindningarna planerar du att undvika `HttpClient`portutmattning som kan orsakas av felaktig instansiering av . Mer information finns [i Så här hanterar du anslutningar i Azure Functions](../articles/azure-functions/manage-connections.md).
