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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020880"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I dialog rutan **publicera** väljer du **Microsoft Azure App Service**, väljer **Skapa ny**och väljer sedan **publicera**.

   ![Välj publicerings mål](./media/webjobs-publish-netcore/pick-publish-target.png)

1. I dialog rutan **skapa App Service** använder du värd inställningarna som anges i tabellen nedanför bilden:

    ![Dialogrutan Skapa App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[Värd plan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | En [App Service-plan](../articles/app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app. Du kan spara pengar när du är värd för flera appar genom att konfigurera webbapparna så att de delar en enda App Service-plan. App Service planer definierar region, instans storlek, skalnings antal och SKU (kostnads fri, delad, Basic, standard eller Premium). Välj **ny** för att skapa en ny app service plan. |

1. Klicka på **skapa** för att skapa ett webb jobb och relaterade resurser i Azure med de här inställningarna och distribuera projekt koden.