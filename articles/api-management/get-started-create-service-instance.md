---
title: Skapa en instans av Azure API Management | Microsoft Docs
description: "Följ stegen i den här kursen hjälper dig att skapa en ny Azure API Management-instans."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Skapa en ny instans av Azure API Management-tjänsten

Den här självstudiekursen beskriver steg för att skapa en ny API-hantering instans med hjälp av [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Skapa en ny tjänst

1. I den [Azure-portalen](https://portal.azure.com/)väljer **ny** > **Enterprise Integration** > **API management**.

    Du kan också välja **ny**, typen `API management` i sökrutan och tryck på RETUR. Klicka på **Skapa**.

2. I den **API Management-tjänsten** och ange ett unikt **namn** för API Management-tjänsten. Det här namnet kan inte ändras senare.

    > [!TIP]
    > Tjänstens namn används för att generera ett standard-domännamnet i form av *{name} .azure-api.net.* Om du vill använda ett anpassat domännamn Se [konfigurera en anpassad domän](configure-custom-domain.md). <br/>
    > Tjänstens namn används för att referera till tjänsten och motsvarande Azure-resurs.

5. Välj en **prenumeration** mellan de olika Azure-prenumerationer som du har åtkomst till.
6. I **Resursgrupp** väljer du ny eller befintlig resurs.  En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Lär dig mer [här](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. I **plats**, väljer du den geografiska region där API Management-tjänsten har skapats. Endast de tillgängliga API Management service-regionerna visas i den nedrullningsbara listrutan. 
9. Ange en **organisationsnamn**. Det här namnet används i ett antal platser. Till exempel titeln developer-portalen och avsändaren av e-postmeddelanden.
10. I **administratör e-post**, Ställ in e-postadress till vilken alla meddelanden från **API Management** kommer att skickas.
11. I **prisnivå**, ange **Developer** nivån för att utvärdera tjänsten. Det här skiktet är inte för produktion. Läs mer om att skala API Management-nivåer, [uppgradera och skala](upgrade-and-scale.md).
12. Välj **Skapa**.

    > [!TIP]
    > Det tar vanligtvis mellan 20 och 30 minuter att skapa en API Management-tjänsten. Att välja **fäst på instrumentpanelen** gör att hitta en nyligen skapad service.

## <a name="next-steps"></a>Nästa steg

[Publicera ett API med Azure API Management](#api-management-getstarted-publish-api.md)