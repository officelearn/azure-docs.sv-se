---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692587"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Skapa ett lagringskonto för Event Processor Host
Event Processor Host är en smart agent som gör det enklare att ta emot händelser från Event Hubs genom hantering av permanenta kontrollpunkter och parallella mottaganden. För att kunna använda kontrollpunkter måste Event Processor Host ha ett lagringskonto. I följande exempel visas hur du skapar ett lagringskonto och hur du kommer åt dess åtkomstnycklar:

1. På Portal-menyn i Azure väljer du **Skapa en resurs**.

    ![Skapa ett resursmenyalternativ, Microsoft Azure-portal](./media/event-hubs-create-storage/create-resource.png)

2. Välj > **lagringslagringskonto**. **Storage**
   
    ![Välj lagringskonto, Microsoft Azure-portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Gör följande på sidan **Skapa lagringskonto**: 

   1. Ange **namnet på lagringskontot**.
   2. Välj en **Azure-prenumeration** som innehåller händelsehubben.
   3. Välj eller skapa **resursgruppen** som har händelsehubben.
   4. Välj en **plats** där resursen ska skapas. 
   5. Välj **Granska + skapa**.
   
        ![Granska + skapa, Skapa lagringskonto, Microsoft Azure-portal](./media/event-hubs-create-storage/review-create.png)

4. På sidan **Granska + skapa** granskar du värdena och väljer **Skapa**. 

    ![Granska inställningar för lagringskonto och skapa Microsoft Azure-portal](./media/event-hubs-create-storage/create-storage-account.png)
5. När du har sett meddelandet **Lyckades med distributioner** i dina meddelanden väljer du **Gå till resursen** för att öppna sidan Lagringskonto. Du kan också expandera **distributionsinformation och** sedan välja den nya resursen i resurslistan.  

    ![Gå till resurs, distribution av lagringskonto, Microsoft Azure-portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Välj **Behållare**.

    ![Välj behållartjänsten Blobbar, lagringskonton, Microsoft Azure-portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Välj **+ Behållare** högst upp, ange ett **namn** för behållaren och välj **OK**. 

    ![Skapa en ny blob-behållare, lagringskonton, Microsoft Azure-portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Välj **Access-nycklar** på sidan **Lagringskonto** och kopiera värdet för **key1**.

    Spara följande värden på Anteckningar eller någon annan tillfällig plats.
    - Namn på lagringskontot
    - Åtkomstnyckel för lagringskontot
    - Behållarens namn
