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
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132578"
---
Med Functions är det enkelt att lägga till Application Insights integration till en Function-app från [Azure Portal].

1. I [portalens][Azure-Portal]skriver `Function Apps` du in Sök fältet längst upp på sidan, väljer din function-app och väljer sedan **Application Insights inte har kon figurer ATS** överst i fönstret. Om du inte ser den här banderollen har din app redan Application Insights aktive rad.

    ![Aktivera Application Insights från portalen](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Skapa en Application Insights resurs genom att använda de inställningar som anges i tabellen under bilden.

   ![Skapa en Application Insights-resurs](media/functions-connect-new-app-insights/ai-general.png)

    | Inställningen      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Unikt namn på App | Det är enklast att använda samma namn som din Function-app, vilket måste vara unikt i din prenumeration. | 
    | **Position** | Europa, västra | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som din Function-app eller en som är nära den regionen. |

1. Välj **OK**. Application Insights resursen skapas i samma resurs grupp och prenumeration som din Function-app. När resursen har skapats stänger du Application Insightss fönstret.

1. Tillbaka i din Function-app väljer du **program inställningar**och rullar sedan ned till **program inställningar**. Om du ser en inställning med `APPINSIGHTS_INSTRUMENTATIONKEY`namnet är Application Insights integration aktive rad för din Function-app som körs i Azure.

[Azure Portal]: https://portal.azure.com
