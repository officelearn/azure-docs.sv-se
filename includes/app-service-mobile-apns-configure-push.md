---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187841"
---
1. Starta **Nyckelringsåtkomst på**din Mac . Öppna Mina certifikat under **Kategori**i det vänstra **navigeringsfältet**. Leta reda på DET SSL-certifikat som du hämtade i föregående avsnitt och visa sedan dess innehåll. Välj bara certifikatet (välj inte den privata nyckeln). Exportera [den sedan](https://support.apple.com/kb/PH20122?locale=en_US).
2. I [Azure-portalen](https://portal.azure.com/)väljer du **Bläddra bland alla** > **App Services**. Välj sedan din bärbara mobilappars baksida. 
3. Under **Inställningar**väljer du **App Service Push**. Välj sedan namnet på meddelandehubben. 
4. Gå till**Uppladdningscertifikat för** **Apple Push Notification Services** > . Ladda upp .p12-filen och välj rätt **läge** (beroende på om klientens SSL-certifikat från tidigare är produktion eller sandlåda). Spara eventuella ändringar.

Tjänsten är nu konfigurerad för att fungera med push-meddelanden på iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
