---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817442"
---
Funktionen Mobile Apps i Azure App Service använder [Azure Notification Hubs] att skicka push-meddelanden, så att du ska konfigurera en meddelandehubb för din mobilapp.

1. I den [Azure Portal]går du till **Apptjänster**, och välj sedan din app-serverdel. Under **inställningar**väljer **Push**.
2. Om du vill lägga till en notification hub-resurs i appen, Välj **Connect**. Du kan skapa en hubb, eller så kan du ansluta till en befintlig.

    ![Konfigurera ett nav](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nu har du anslutit en notification hub i Mobile Apps backend-projektet. Senare kan du konfigurera den här meddelandehubben för att ansluta till en plattformsspecifika meddelandesystem (PNS) för att skicka till enheter.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
