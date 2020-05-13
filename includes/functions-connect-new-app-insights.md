---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121601"
---
Med Functions är det enkelt att lägga till Application Insights integration till en Function-app från [Azure Portal].

1. I [Azure Portal][Azure Portal]söker du efter och väljer **Function-appen**och väljer sedan din Function-app. 

1. Välj **Application Insights inte har kon figurer ATS** överst i fönstret. Om du inte ser den här banderollen har din app redan Application Insights aktive rad.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Aktivera Application Insights från portalen":::

1. Skapa en Application Insights resurs genom att använda de inställningar som anges i tabellen under bilden.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Skapa en Application Insights resurs":::

    | Inställningen      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Unikt namn på App | Det är enklast att använda samma namn som din Function-app, vilket måste vara unikt i din prenumeration. | 
    | **Position** | Europa, västra | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som din Function-app eller en som är nära den regionen. |

1. Välj **Använd**. Application Insights resursen skapas i samma resurs grupp och prenumeration som din Function-app. När resursen har skapats stänger du Application Insightss fönstret.

1. Tillbaka i din Function-app väljer du **Inställningar**  >  **konfiguration**och väljer sedan **program inställningar**. Om du ser en inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY` är Application Insights integration aktive rad för din Function-app som körs i Azure.

[Azure Portal]: https://portal.azure.com
