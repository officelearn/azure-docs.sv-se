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
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509072"
---
1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** på den vänstra menyn och välj sedan **Meddelandehubbar** i den **Mobile** avsnittet. Välj stjärnikonen bredvid namnet på tjänsten för att lägga till tjänsten för att den **Favoriter** avsnitt i den vänstra menyn. När du lägger till **Meddelandehubbar** till **Favoriter**, markerar den på den vänstra menyn.

      ![Azure-portalen – välj Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet.

      ![Notification Hubs – Lägga till verktygsfältsknapp](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. På sidan **Notification Hub** gör du följande:

    1. Ange ett namn i **Notification Hub**.  

    1. Ange ett namn i **skapa ett nytt namnområde**. En namnrymd innehåller en eller flera hubbar.

    1. Välj ett värde från den **plats** nedrullningsbara listrutan. Det här värdet anger den plats där du vill skapa hubben.

    1. Välj en befintlig resursgrupp i **resursgrupp**, eller skapa ett namn för en ny resursgrupp.

    1. Välj **Skapa**.

        ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Välj **meddelanden** (klockikon) och välj sedan **gå till resurs**. Du kan också uppdatera listan på den **Meddelandehubbar** och välj din hubb.

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Välj **Åtkomstprinciper** i listan. Observera att de två anslutningssträngarna är tillgängliga för dig. Du behöver dem senare för att hantera push-meddelanden.

      >[!IMPORTANT]
      >Gör *inte* använder den **DefaultFullSharedAccessSignature** principen i ditt program. Detta är avsett att användas i serverdelen endast.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
