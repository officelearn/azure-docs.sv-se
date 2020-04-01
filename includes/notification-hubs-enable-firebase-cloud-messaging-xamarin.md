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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728830"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Gör följande på sidan **Lägg till Firebase i din Android-app:** 
    1. För **Android-paketnamnet**anger du ett namn på ditt paket. Till exempel: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Ange paketnamnet](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **Registrera app**.  
    1. Välj **Ladda ner google-services.json**. Spara sedan filen i mappen i projektet och välj **Nästa**. Om du inte har skapat Visual Studio-projektet ännu kan du göra det här steget när du har skapat projektet. 

        ![Ladda ner google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Välj **Nästa**. 
    7. Välj **Hoppa över det här steget**. 

        ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har laddat ner **filen google-services.json** kan du ladda ner den på den här sidan. 

    ![Ladda ner google-services.json från fliken Allmänt](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Växla till fliken **Cloud Messaging** högst upp. Kopiera och spara **servernyckeln** för senare användning. Du använder det här värdet för att konfigurera meddelandehubben.

    ![Kopiera servernyckel](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
