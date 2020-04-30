---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "68728830"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Utför följande steg på sidan **Lägg till Firebase på din Android-app** : 
    1. Ange ett namn för ditt paket för **Android-paketets namn**. Till exempel: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Ange paket namnet](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **Registrera app**.  
    1. Välj **Hämta Google-Services. JSON**. Spara sedan filen i mappen i projektet och välj **Nästa**. Om du inte har skapat Visual Studio-projektet ännu kan du göra det här när du har skapat projektet. 

        ![Ladda ned Google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Välj **Nästa**. 
    7. Välj **hoppa över det här steget**. 

        ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har hämtat **Google-Services. JSON-** filen kan du ladda ned den på den här sidan. 

    ![Ladda ned Google-Services. JSON från fliken Allmänt](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Växla till fliken **Cloud Messaging** överst. Kopiera och spara **Server nyckeln** för senare användning. Du använder det här värdet för att konfigurera Notification Hub.

    ![Kopiera server nyckel](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
