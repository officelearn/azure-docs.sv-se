---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403888"
---
Functions gör det enkelt att lägga till Application Insights-integrering i en funktionsapp från den [Azure Portal].

1. I den [portal][azure portal]väljer **alla tjänster > Funktionsappar**, markera din funktionsapp och välj sedan den **Application Insights** banderoll överst i fönstret

    ![Aktivera Application Insights från portalen](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Skapa en Application Insights-resurs med hjälp av inställningarna i tabellen nedanför bilden:

   ![Skapa en Application Insights-resurs](media/functions-connect-new-app-insights/ai-general.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Unikt namn | Det är enklast att använda samma namn som din funktionsapp, som måste vara unikt i din prenumeration. | 
    | **Plats** | Västra Europa | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som funktionsappen eller nära den. |

1. Välj **OK**. Application Insights-resursen skapas i samma resursgrupp och prenumeration som funktionsappen. När du har skapats, stänger du fönstret Application Insights.

1. Tillbaka i din funktionsapp väljer **programinställningar**, och bläddra ned till **programinställningar**. När du ser en inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY`, betyder det att Application Insights-integrering är aktiverad för din funktionsapp som körs i Azure.

[Azure Portal]: https://portal.azure.com
