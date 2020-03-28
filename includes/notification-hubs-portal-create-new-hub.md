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
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509072"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** på den vänstra menyn och välj sedan **Meddelandehubbar** i avsnittet **Mobil.** Välj stjärnikonen bredvid tjänstnamnet om du vill lägga till tjänsten i avsnittet **FAVORITER** på den vänstra menyn. När du har lagt till **meddelandehubbar** **i FAVORITER**markerar du det på den vänstra menyn.

      ![Azure-portalen – välj Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet.

      ![Notification Hubs – Lägga till verktygsfältsknapp](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. På sidan **Notification Hub** gör du följande:

    1. Ange ett namn i **Meddelandehubben**.  

    1. Ange ett namn i **Skapa ett nytt namnområde**. En namnrymd innehåller en eller flera hubbar.

    1. Välj ett värde i listrutan **Plats.** Det här värdet anger den plats där du vill skapa navet.

    1. Välj en befintlig resursgrupp i **Resursgrupp**eller skapa ett namn för en ny resursgrupp.

    1. Välj **Skapa**.

        ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Välj **Meddelanden** (klockikonen) och välj sedan **Gå till resurs**. Du kan också uppdatera listan på sidan **Meddelandehubbar** och välja hubb.

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Välj **Åtkomstprinciper** i listan. Observera att de två anslutningssträngarna är tillgängliga för dig. Du behöver dem senare för att hantera push-meddelanden.

      >[!IMPORTANT]
      >Använd *inte* standardprincipen **FullSharedAccessSignature** i ditt program. Detta är tänkt att användas i din bakdel bara.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
