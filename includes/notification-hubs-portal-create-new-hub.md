---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 22c1d24042072de5d57d41da379a5fad18180de7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972528"
---
1. Logga in på [Azure Portal](https://portal.azure.com).

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

