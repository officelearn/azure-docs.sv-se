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
ms.openlocfilehash: 38ba8ccad63120d33ce06faf39439bb58706e78d
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660453"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Skapa ett lagringskonto för Event Processor Host
Event Processor Host är en smart agent som gör det enklare att ta emot händelser från Event Hubs genom hantering av permanenta kontrollpunkter och parallella mottaganden. För att kunna använda kontrollpunkter måste Event Processor Host ha ett lagringskonto. I följande exempel visas hur du skapar ett lagringskonto och hur du kommer åt dess åtkomstnycklar:

1. I Azure-portalen väljer du **Skapa en resurs** högst upp till vänster på skärmen.

2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
   
    ![Välja lagringskonto](./media/event-hubs-create-storage/create-storage1.png)

3. Gör följande på sidan **Skapa lagringskonto**: 

    1. Ange ett namn för lagringskontot. 
    2. Välj en Azure-prenumeration som innehåller händelsehubben.
    3. Välj den resursgrupp som innehåller händelsehubben.
    4. Välj en plats där du vill skapa resursen. 
    5. Klicka därefter på **Granska + skapa**.
   
    ![Skapa lagringskonto – sida](./media/event-hubs-create-storage/create-storage2.png)

4. På sidan **Granska + skapa** granskar du värdena och väljer **Skapa**. 

    ![Granska inställningarna för lagringskontot och skapa](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Efter att du ser den **distributioner lyckades** meddelandet markerar **gå till resurs** överst på sidan. Du kan också starta sidan Storage-konto genom att välja ditt lagringskonto i resurslistan.  

    ![Välja lagringskontot från distributionen](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. I fönstret **Essentials** väljer du **Blobar**. 

    ![Välj BLOB-tjänst](./media/event-hubs-create-storage/select-blobs-service.png)
1. Välj **+ behållare** högst upp, ange en **namn** för behållaren och välj **OK**. 

    ![Skapa en blobcontainer](./media/event-hubs-create-storage/create-blob-container.png)
1. Välj **åtkomstnycklar** i menyn till vänster och kopiera värdet för **key1**. 

    Spara följande värden i anteckningar eller någon annan tillfällig plats.
    - Namnet på lagringskontot
    - Åtkomstnyckeln för lagringskontot
    - Namnet på behållaren
