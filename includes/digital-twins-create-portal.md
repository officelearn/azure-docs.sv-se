---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 92b9a4754769566feb3658e07081e9fdae78fcfc
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903857"
---
1. Logga in på [Azure Portal](https://portal.azure.com).

1. På Start sidan väljer du **+ skapa en resurs**. Sök efter **digitala dubbla**och välj **digitala dubbla**. Välj **Skapa** för att starta distributionsprocessen.

   [![val för att skapa en ny Digitals dubbla instanser](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. I fönstret **Digital Twins** anger du följande information:
   * **Resursnamn**: Skapa ett unikt namn på Digital Twins-instansen.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa Digital Twins-instansen. 
   * **Resursgrupp**: Välj eller skapa en [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) för Digital Twins-instansen.
   * **Plats**: Välj den plats som är närmast enheten.

     [![Digitals sammanflätade fönster med angiven information](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Granska din Digital Twins-information och välj sedan **Skapa**. Det kan ta några minuter för Digital Twins-instansen att skapas. Du kan övervaka förloppet i **meddelandefönstret**.

1. Öppna **översiktsfönstret** för Digital Twins-instansen. Observera länken under **Hanterings-API**.

   URL:en för **hanterings-API** har formatet `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Denna URL tar dig till dokumentationen för Azure Digital Twins REST API som gäller för din instans. Läs om [hur du använder Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) för att få reda på hur du läser och använder den här API-dokumentationen.

    Kopiera och ändra **hanterings-API** -URL: en till det här formatet: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Programmet använder den ändra URL:en som bas-URL för att få åtkomst till instansen. Kopiera denna ändrade URL till en temporär fil. Du behöver URL:en i nästa avsnitt.

    [API för ![hantering](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)