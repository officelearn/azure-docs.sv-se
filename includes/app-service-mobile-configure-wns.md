---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134290"
---
1. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb.
2. Gå till **Windows (WNS)**. Ange sedan den **säkerhetsnyckel** (klienthemlighet) och **paket-SID** som du fick från webbplatsen för Live-tjänster. Välj sedan **spara**.

    ![Ange WNS-nyckeln i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Serverdelen har nu konfigurerats så att WNS för att skicka push-meddelanden.
