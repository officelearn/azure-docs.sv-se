---
title: ta med fil
description: ta med fil
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: a5d29e77f6ba10ed3069cb3f5a3c8089f49c237d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60423407"
---
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn och välj **Notification Hubs** i avsnittet **Mobil**. Välj stjärnan (`*`) intill namnet på tjänsten för att lägga till den i avsnittet **FAVORITER** på den vänstra menyn. När **Notification Hubs** har lagts till i **FAVORITER** väljer du det på den vänstra menyn. 

      ![Azure-portalen – välj Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet. 

      ![Notification Hubs – Lägga till verktygsfältsknapp](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. På sidan **Notification Hub** gör du följande: 
    1. Ange ett **namn** för **meddelandehubben**.  
    2. Ange ett **namn** för **namnrymden**. En namnrymd innehåller en eller flera hubbar. 
    3. Välj en **plats** där du vill skapa meddelandehubben. 
    4. Ange en befintlig resursgrupp eller ange ett namn för den nya **resursgruppen**.
    5. Välj **Skapa**. 

        ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Välj **Meddelanden** (klockikon) och välj **Gå till resurser**. Du kan också uppdatera listan på sidan **Notification Hubs** och välja din meddelandehubb. 

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Välj **Åtkomstprinciper** i listan. Anteckna de två anslutningssträngarna som är tillgängliga för dig. Du behöver dem för att hantera push-meddelanden senare.

      >[!IMPORTANT]
      >Använd **INTE** DefaultFullSharedAccessSignature i ditt program. Detta är endast avsett att användas i din serverdel.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
