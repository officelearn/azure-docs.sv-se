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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132578"
---
Funktioner gör det enkelt att lägga till Integrering av Application Insights i en funktionsapp från [Azure-portalen].

1. I [portalen][Azure] `Function Apps` Portal skriver du i sökfältet högst upp på sidan, väljer din funktionsapp och väljer sedan **att programstatistiken inte är konfigurerad** högst upp i fönstret. Om du inte ser den här bannern har appen redan application insights aktiverat.

    ![Aktivera programinsikter från portalen](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Skapa en Application Insights-resurs med hjälp av de inställningar som anges i tabellen under bilden.

   ![Skapa en Application Insights-resurs](media/functions-connect-new-app-insights/ai-general.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Unikt appnamn | Det är enklast att använda samma namn som din funktionsapp, som måste vara unik i din prenumeration. | 
    | **Location** | Europa, västra | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som funktionsappen eller en som ligger nära den regionen. |

1. Välj **OK**. Application Insights-resursen skapas i samma resursgrupp och prenumeration som din funktionsapp. När resursen har skapats stänger du fönstret Programstatistik.

1. Tillbaka i funktionsappen väljer du **Programinställningar**och bläddrar sedan ned till **Programinställningar**. Om du ser `APPINSIGHTS_INSTRUMENTATIONKEY`en inställning med namnet är Application Insights-integrering aktiverad för din funktionsapp som körs i Azure.

[Azure Portal]: https://portal.azure.com
