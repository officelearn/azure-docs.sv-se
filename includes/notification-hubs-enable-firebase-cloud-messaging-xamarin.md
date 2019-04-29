---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60423427"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. På den **Lägg till Firebase i din Android-app** gör du följande steg: 
    1. För **Android paketnamn**, ange ett namn för ditt paket. Till exempel: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Ange paketnamnet på](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **registrera app**. 
4. Välj **hämta google-services.json**, spara filen till den **app** mappen i projektet och välj sedan **nästa**. 

    ![Hämta google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Välj **nästa** på sidan. 
7. Välj **hoppa över detta steg** på sidan. 

    ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har hämtat den **google-services.json** -fil som du kan göra så vidare den här sidan. 
5. Växla till den **Cloud Messaging** fliken högst upp. 
6. Kopiera och spara den **servernyckel** för senare användning. Du kan använda det här värdet för att konfigurera din meddelandehubb.
