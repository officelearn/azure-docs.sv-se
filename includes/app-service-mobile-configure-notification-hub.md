---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187846"
---
Funktionen Mobilappar i Azure App Service använder [Azure Notification Hubs] för att skicka push-enheter, så att du konfigurerar en meddelandehubb för din mobilapp.

1. Gå till **App Services**i [Azure-portalen]och välj sedan din app-back end. Under **Inställningar**väljer du **Push**.
2. Om du vill lägga till en aviseringsnavresurs i appen väljer du **Anslut**. Du kan antingen skapa ett nav eller ansluta till ett befintligt.

    ![Konfigurera ett nav](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nu har du anslutit en meddelandehubb till ditt backend-projekt för mobilappar. Senare konfigurerar du den här meddelandehubben för att ansluta till ett plattformsmeddelandesystem (PNS) för att skicka till enheter.

[Azure-portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
