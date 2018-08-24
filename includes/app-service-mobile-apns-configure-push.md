---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 25bb5cfb0073f7533faddec43b38fb5031b82a43
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811614"
---
1. Starta på en Mac **Nyckelhanterare**. I det vänstra navigeringsfältet under **kategori**öppnar **mina certifikat**. Hitta SSL-certifikatet som du hämtade i föregående avsnitt och lämna ut innehållet. Välj endast certifikatet (Välj inte den privata nyckeln). Sedan [exportera det](https://support.apple.com/kb/PH20122?locale=en_US).
2. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. 
3. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb. 
4. Gå till **Apple Push Notification Services** > **överför certifikat**. Ladda upp .p12-filen som att välja rätt **läge** (beroende på om dina SSL-klientcertifikat från tidigare är produktions- eller sandbox). Spara ändringarna.

Din tjänst har nu konfigurerats för att fungera med push-meddelanden på iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
