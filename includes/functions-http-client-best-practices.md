---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d5de8efc62984b9738c9247b12225f35265309c5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187111"
---
> [!TIP]
>
> Om du planerar att använda HTTP-eller webhook-bindningarna bör du planera för att undvika att port överbelastningen kan orsakas av `HttpClient`felaktig instansiering av. Mer information finns i [hantera anslutningar i Azure Functions](../articles/azure-functions/manage-connections.md).
