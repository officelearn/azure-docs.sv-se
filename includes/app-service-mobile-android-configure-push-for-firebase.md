---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187848"
---
1. Klicka på Bläddra **bland alla** > **AppTjänster**i [Azure-portalen](https://portal.azure.com/)och klicka sedan på din bärbara appars baksida. Klicka på **Push för App Service**under **Inställningar**och klicka sedan på namnet på meddelandehubben.
2. Gå till **Google (GCM)** anger du det **servernyckelvärde** som du fick från Firebase i föregående procedur och klickar sedan på **Spara**.

    ![Ange API-nyckeln i portalen](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Backend-backen mobiler är nu konfigurerad för att använda Firebase Cloud Messaging. På så sätt kan du skicka push-meddelanden till din app som körs på en Android-enhet med hjälp av meddelandehubben.
