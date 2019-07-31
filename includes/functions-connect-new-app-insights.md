---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669845"
---
Med Functions är det enkelt att lägga till Application Insights integration till en Function-app från [Azure Portal].

1. I [portalen][azure portal]väljer du **alla tjänster > Function-appar**, väljer din Function-app och väljer sedan **Application Insightss** banderollen högst upp i fönstret

    ![Aktivera Application Insights från portalen](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Skapa en Application Insights resurs genom att använda de inställningar som anges i tabellen under bilden.

   ![Skapa en Application Insights-resurs](media/functions-connect-new-app-insights/ai-general.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Unikt namn på App | Det är enklast att använda samma namn som din Function-app, vilket måste vara unikt i din prenumeration. | 
    | **Location** | Västra Europa | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som din Function-app eller en som är nära den regionen. |

1. Välj **OK**. Application Insights resursen skapas i samma resurs grupp och prenumeration som din Function-app. När resursen har skapats stänger du Application Insightss fönstret.

1. Tillbaka i din Function-app väljer du **program inställningar**och rullar sedan ned till **program inställningar**. Om du ser en inställning med `APPINSIGHTS_INSTRUMENTATIONKEY`namnet är Application Insights integration aktive rad för din Function-app som körs i Azure.

[Azure Portal]: https://portal.azure.com
