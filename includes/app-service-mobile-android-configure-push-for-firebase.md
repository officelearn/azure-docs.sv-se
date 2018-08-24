---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811574"
---
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **Bläddra bland alla** > **Apptjänster**, och klicka sedan på din Mobile Apps-serverdel. Under **inställningar**, klickar du på **App Service Push**, och klicka sedan på namnet på din meddelandehubb.
2. Gå till **Google (GCM)**, ange den **servernyckel** värde som du fick från Firebase i föregående procedur, och klicka sedan på **spara**.

    ![Ange API-nyckeln i portalen](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Mobile Apps-serverdel har nu konfigurerats för att använda Firebase Cloud Messaging. På så sätt kan du skicka push-meddelanden till din app som körs på en Android-enhet med hjälp av meddelandehubben.
