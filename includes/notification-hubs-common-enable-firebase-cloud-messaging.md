---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081559"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging för Android

1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt genom att ange **PushDemo** som **projekt namn**.

    > [!NOTE]
    > Ett unikt namn skapas åt dig. Som standard består detta av en gemen variant av det namn du angav plus ett genererat nummer avgränsat med ett bindestreck. Du kan ändra detta om du vill ange att den fortfarande är globalt unik.

1. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**.

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Utför följande steg på sidan **Lägg till Firebase på din Android-app** .
    1. Ange ett namn för ditt paket för **Android-paketets namn**. Exempel: `com.<organization_identifier>.<package_name>`.

        ![Ange paket namnet](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Välj **Registrera app**.  
    1. Välj **hämta google-services.jspå**. Spara sedan filen i en lokal mapp för senare användning och välj **Nästa**.  

        ![Ladda ned google-services.jspå](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Välj **Nästa**.
    1. Välj **Fortsätt till konsol**

        > [!NOTE]
        > Om knappen **Fortsätt till konsol** inte är aktive rad, på grund av *Verifiera installations* kontroll, väljer du **hoppa över det här steget**.

1. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Om du inte har hämtat **google-services.js** filen kan du ladda ned den på den här sidan.

1. Växla till fliken **Cloud Messaging** överst. Kopiera och spara **Server nyckeln** för senare användning. Du använder det här värdet för att konfigurera Notification Hub.

    ![Kopiera server nyckel](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
