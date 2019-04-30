---
title: ta med fil
description: ta med fil
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
origin.date: 02/19/2019
ms.date: 03/18/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832404"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I den **publicera** dialogrutan **Microsoft Azure App Service**, Välj **Skapa ny**, och välj sedan **publicera**.

   ![Välj publicera mål](./media/webjobs-publish-netcore/pick-publish-target.png)

1. I den **skapa App Service** dialogrutan, använder du värdinställningarna som anges i tabellen nedanför bilden:

    ![Dialogrutan Skapa App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[Värdplan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | En [App Service-plan](../articles/app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app. Du kan spara pengar när du är värd för flera appar genom att konfigurera webbapparna så att de delar en enda App Service-plan. App Service-planer definierar region, instansstorlek, skalningsantal och SKU (kostnadsfri, delad, Basic, Standard eller Premium). Välj **New** att skapa en ny App Service-plan. |

1. Klicka på **skapa** att skapa ett WebJob och relaterade resurser i Azure med de här inställningarna och distribuera projektkoden.