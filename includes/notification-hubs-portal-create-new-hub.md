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
ms.openlocfilehash: b68fa345d4772134c30ce8b8b559f98113a0496f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453107"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Skapa en resurs** > **Mobilt** > **Notification Hub**.

      ![Azure Portal – skapa en Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)

3. I rutan **Notification Hub** skriver du ett unikt namn. Välj din **region**, **prenumeration** och **resursgrupp** (om du redan har en).

      Om du inte redan har ett namnområde för Service Bus kan du använda standardnamnet som skapas baserat på hubbnamnet (om namnområdesnamnet är tillgängligt).

      Om du redan har ett namnområde för Service Bus som du vill skapa hubben i följer du de här stegen

    a. I området **Namnområde** väljer du länken **Välj befintligt**.

    b. Välj **Skapa**.

    ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Välj **Meddelanden** (klockikon) och välj **Gå till resurser**.

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)
5. Välj **Åtkomstprinciper** i listan. Anteckna de två anslutningssträngarna som är tillgängliga för dig. Du behöver dem för att hantera push-meddelanden senare.

      >[!IMPORTANT]
      >Använd **INTE** DefaultFullSharedAccessSignature i ditt program. Detta är endast avsett att användas i din serverdel.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
