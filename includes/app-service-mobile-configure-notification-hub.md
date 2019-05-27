---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140299"
---
Funktionen Mobile Apps i Azure App Service använder [Azure Notification Hubs] att skicka push-meddelanden, så att du ska konfigurera en meddelandehubb för din mobilapp.

1. I den [Azure Portal]går du till **Apptjänster**, och välj sedan din app-serverdel. Under **inställningar**väljer **Push**.
2. Om du vill lägga till en notification hub-resurs i appen, Välj **Connect**. Du kan skapa en hubb, eller så kan du ansluta till en befintlig.

    ![Konfigurera ett nav](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nu har du anslutit en notification hub i Mobile Apps backend-projektet. Senare kan du konfigurera den här meddelandehubben för att ansluta till en plattformsspecifika meddelandesystem (PNS) för att skicka till enheter.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
