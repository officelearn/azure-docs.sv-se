---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169924"
---
| | |
|--|--|
|**`<DESTINATION>`**| Målet som loggarna skickas till. Giltiga värden är `AppInsights` och `Blob` .<br/>`AppInsights`Se till att [Application Insights är aktive rad i din Function-app](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration)när du använder.<br/>När du ställer in målet till `Blob` skapas loggar i en BLOB-behållare med namnet `azure-functions-scale-controller` i standard lagrings kontot som angetts i `AzureWebJobsStorage` program inställningen. |
|**`<VERBOSITY>`** | Anger loggnings nivån. Värden som stöds är `None` , `Warning` och `Verbose` .<br/>När den är inställd på `Verbose` loggar skalnings styrenheten av en orsak för varje ändring i antalet arbets uppgifter, samt information om utlösare som ingår i dessa beslut. Utförliga loggar innehåller Utlös ande varningar och de hash-värden som används av utlösarna innan och efter att styrenheten körs. |

> [!CAUTION]
> Lämna inte loggning av skalnings styrenhet aktive rad. Aktivera loggning tills du har samlat in tillräckligt med data för att förstå hur skalnings styrenheten fungerar, och inaktivera den sedan.