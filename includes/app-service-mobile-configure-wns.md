---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187842"
---
1. I [Azure-portalen](https://portal.azure.com/)väljer du **Bläddra bland alla** > **App Services**. Välj sedan din bärbara mobilappars baksida. Under **Inställningar**väljer du **App Service Push**. Välj sedan namnet på meddelandehubben.
2. Gå till **Windows (WNS)**. Ange sedan **säkerhetsnyckeln** (klienthemligheten) och **Package SID** som du har fått från webbplatsen Live Services. Välj sedan **Spara**.

    ![Ange WNS-tangenten i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Din serverdel är nu konfigurerad för att använda WNS för att skicka push-meddelanden.
