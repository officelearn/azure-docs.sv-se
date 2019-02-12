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
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823325"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn och välj **Notification Hubs** i avsnittet **Mobil**. Välj stjärnan (`*`) intill namnet på tjänsten för att lägga till den i avsnittet **FAVORITER** på den vänstra menyn. När **Notification Hubs** har lagts till i **FAVORITER** väljer du det på den vänstra menyn. 

      ![Azure-portalen – välj Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet. 

      ![Notification Hubs – Lägga till verktygsfältsknapp](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. På sidan **Notification Hub** gör du följande: 
    1. Ange ett **namn** för **meddelandehubben**.  
    2. Ange ett **namn** för **namnrymden**.
    3. Välj en **plats** där du vill skapa meddelandehubben. 
    4. Ange en befintlig resursgrupp eller ange ett namn för den nya **resursgruppen**.
    5. Välj **Skapa**. 

        ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Välj **Meddelanden** (klockikon) och välj **Gå till resurs**, eller uppdatera listan på sidan **Notification Hubs**, och välj din meddelandehubb. 

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Välj **Åtkomstprinciper** i listan. Anteckna de två anslutningssträngarna som är tillgängliga för dig. Du behöver dem för att hantera push-meddelanden senare.

      >[!IMPORTANT]
      >Använd **INTE** DefaultFullSharedAccessSignature i ditt program. Detta är endast avsett att användas i din serverdel.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
