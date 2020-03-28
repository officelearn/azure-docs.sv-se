---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895439"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Markera fältet på hemmasidan och sedan **+ Skapa en resurs**. 

   [![Expandera startsidan och välj sedan + Skapa en resurs](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Sök efter **Digitala tvillingar**och välj **Digitala tvillingar.** 

   [![Val för att skapa en ny Digital Twins-instans](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   Du kan också välja **Sakernas Internet**och välja **Digitala tvillingar (förhandsgranskning)**.

1. Välj **Skapa** för att starta distributionsprocessen.

   [![Skapa och bekräfta resursdistributionen](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. I fönstret **Digital Twins** anger du följande information:
   * **Resursnamn**: Skapa ett unikt namn på Digital Twins-instansen.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa Digital Twins-instansen. 
   * **Resursgrupp**: Välj eller skapa en [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) för Digital Twins-instansen.
   * **Plats**: Välj den plats som är närmast enheten.

     [![Digital Twins-fönstret med angiven information](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Granska din Digital Twins-information och välj sedan **Skapa**. Det kan ta några minuter för Digital Twins-instansen att skapas. Du kan övervaka förloppet i **meddelandefönstret**.

1. Öppna **översiktsfönstret** för Digital Twins-instansen. Observera länken under **Hanterings-API**. Api-URL:en för **hantering** är formaterad som: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Denna URL tar dig till dokumentationen för Azure Digital Twins REST API som gäller för din instans. Läs om [hur du använder Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) för att få reda på hur du läser och använder den här API-dokumentationen. Kopiera och ändra **URL:en för hanterings-API** till det här formatet: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Programmet använder den ändra URL:en som bas-URL för att få åtkomst till instansen. Kopiera denna ändrade URL till en temporär fil. Du behöver URL:en i nästa avsnitt.

   [![Översikt över API för hantering](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)