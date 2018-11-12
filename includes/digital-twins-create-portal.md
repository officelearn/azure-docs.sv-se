---
title: ta med fil
description: ta med fil
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 7bac2b291bec2ceda118c877cde997a2fa9f9f37
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284010"
---
1. Logga in på [Azure-portalen](http://portal.azure.com).

1. I navigeringsfönstret till vänster klickar du på **Skapa en resurs**. Sök efter *digital twins* och välj **Digital Twins (förhandsversion)**. Klicka på **Skapa** för att starta distributionsprocessen.

    ![Skapa Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)

1. I fönstret **Digital Twins** anger du följande information:
   * **Resursnamn**: Skapa ett unikt namn på Digital Twins-instansen.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa Digital Twins-instansen. 
   * **Resursgrupp**: Välj eller skapa en [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) för Digital Twins-instansen.
   * **Plats**: Välj den plats som är närmast enheten.

    ![Skapa Digital Twins](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Granska Digital Twins-informationen och klicka på **Skapa**. Det kan ta några minuter för Digital Twins-instansen att skapas. Du kan övervaka förloppet i **meddelandefönstret**.

1. Öppna **översiktsfönstret** för Digital Twins-instansen. Observera länken som visas under **API för hantering**.

    1. URL:en för **API för hantering** har formatet: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_**. Denna URL tar dig till dokumentationen för Azure Digital Twins REST API som gäller för din instans. Läs om [hur du använder Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) för att få reda på hur du läser och använder den här API-dokumentationen.

    1. Ändra URL:en för **API för hantering** till det här formatet: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_**. Programmet använder den ändra URL:en som bas-URL för att få åtkomst till instansen. Kopiera denna ändrade URL till en temporär fil. Du behöver ändra detta i avsnittet nedan.

    ![API:er för hantering](./media/create-digital-twins-portal/digital-twins-management-api.png)