---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140308"
---
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **Bläddra bland alla** > **Apptjänster**, och klicka sedan på din Mobile Apps-serverdel. Under **inställningar**, klickar du på **App Service Push**, och klicka sedan på namnet på din meddelandehubb.
2. Gå till **Google (GCM)** , ange den **servernyckel** värde som du fick från Firebase i föregående procedur, och klicka sedan på **spara**.

    ![Ange API-nyckeln i portalen](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Mobile Apps-serverdel har nu konfigurerats för att använda Firebase Cloud Messaging. På så sätt kan du skicka push-meddelanden till din app som körs på en Android-enhet med hjälp av meddelandehubben.
