---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140117"
---
1. Starta på en Mac **Nyckelhanterare**. I det vänstra navigeringsfältet under **kategori**öppnar **mina certifikat**. Hitta SSL-certifikatet som du hämtade i föregående avsnitt och lämna ut innehållet. Välj endast certifikatet (Välj inte den privata nyckeln). Sedan [exportera det](https://support.apple.com/kb/PH20122?locale=en_US).
2. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. 
3. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb. 
4. Gå till **Apple Push Notification Services** > **överför certifikat**. Ladda upp .p12-filen som att välja rätt **läge** (beroende på om dina SSL-klientcertifikat från tidigare är produktions- eller sandbox). Spara ändringarna.

Din tjänst har nu konfigurerats för att fungera med push-meddelanden på iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
