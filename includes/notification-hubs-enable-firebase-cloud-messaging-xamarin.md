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
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509924"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. På den **Lägg till Firebase i din Android-app** utför följande steg: 
1. 
    1. För den **Android paketnamn**, ange ett namn för ditt paket. Till exempel: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Ange paketnamnet på](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. Välj **registrera app**. 
1. 
1. Välj **hämta google-services.json**. Spara sedan filen till den **app** mappen i projektet och välj **nästa**. 

    ![Hämta google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. Välj **Nästa**. 
7. Välj **hoppa över detta steg**. 

    ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Om du inte har hämtat den **google-services.json** -fil som du kan göra ladda ned den på den här sidan. 

1. Växla till den **Cloud Messaging** fliken högst upp. 

1. Kopiera och spara den **äldre servernyckel** för senare användning. Du kan använda det här värdet för att konfigurera din meddelandehubb.
