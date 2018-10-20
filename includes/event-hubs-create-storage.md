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
ms.openlocfilehash: e499a0f7bec47e672c599c729a15cc3e3d04a28a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471662"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Skapa ett lagringskonto för Event Processor Host
Event Processor Host är en smart agent som gör det enklare att ta emot händelser från Event Hubs genom hantering av permanenta kontrollpunkter och parallella mottaganden. För att kunna använda kontrollpunkter måste Event Processor Host ha ett lagringskonto. I följande exempel visas hur du skapar ett lagringskonto och hur du kommer åt dess åtkomstnycklar:

1. I Azure-portalen och välj **skapa en resurs** på upp till vänster på skärmen.

2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
   
    ![Välj lagringskonto](./media/event-hubs-create-storage/create-storage1.png)

3. På den **skapa lagringskonto** utför följande steg: 

    1. Ange ett namn för lagringskontot. 
    2. Välj en Azure-prenumeration som innehåller event hub.
    3. Välj den resursgrupp som har event hub.
    4. Välj en plats där du vill skapa resursen. 
    5. Klicka sedan på **granska + skapa**.
   
    ![Skapa lagringskonto - sidan](./media/event-hubs-create-storage/create-storage2.png)

4. På den **granska + skapa** , granska värdena och välj **skapa**. 

    ![Granska inställningarna för lagringskontot och skapa](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Efter att du ser den **distributioner lyckades** meddelandet markerar **tillgång till resursen** överst på sidan. Du kan också starta sidan Storage-konto genom att välja ditt lagringskonto i resurslistan.  

    ![Välj lagringskonto från distribution](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. I den **Essentials** väljer **Blobar**. 

    ![Välj BLOB-tjänst](./media/event-hubs-create-storage/select-blobs-service.png)
1. Välj **+ behållare** högst upp, ange en **namn** för behållaren och välj **OK**. 

    ![Skapa en blobcontainer](./media/event-hubs-create-storage/create-blob-container.png)
1. Välj **åtkomstnycklar** i menyn till vänster och kopiera värdet för **key1**. 

    Spara följande värden i anteckningar eller någon annan tillfällig plats.
    - Namnet på lagringskontot
    - Åtkomstnyckeln för lagringskontot
    - Namnet på behållaren