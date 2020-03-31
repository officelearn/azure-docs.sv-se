---
title: ta med fil
description: ta med fil
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020880"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. Välj Microsoft Azure **App Service**i dialogrutan **Publicera** , välj **Skapa ny**och välj sedan **Publicera**.

   ![Välj publiceringsmål](./media/webjobs-publish-netcore/pick-publish-target.png)

1. I dialogrutan **Skapa apptjänst** använder du de värdinställningar som anges i tabellen under bilden:

    ![Dialogrutan Skapa App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[Hosting Plan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | En [App Service-plan](../articles/app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app. Du kan spara pengar när du är värd för flera appar genom att konfigurera webbapparna så att de delar en enda App Service-plan. App serviceplaner definierar region, instansstorlek, skalantal och SKU (gratis, delat, grundläggande, standard eller Premium). Välj **Ny** om du vill skapa en ny apptjänstplan. |

1. Klicka på **Skapa** om du vill skapa ett WebJob-och-relaterade resurser i Azure med dessa inställningar och distribuera projektkoden.