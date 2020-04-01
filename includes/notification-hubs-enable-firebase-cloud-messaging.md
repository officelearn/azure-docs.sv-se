---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70935123"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Gör följande på sidan **Lägg till Firebase i din Android-app:** 
    1. För **Android-paketnamn**kopierar du värdet för ditt **applicationId** i programmets build.gradle-fil. I det här exemplet `com.fabrikam.fcmtutorial1app`är det . 

        ![Ange paketnamnet](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **Registrera app**. 
4. Välj **Hämta google-services.json**, spara filen i **projektets appmapp** och välj sedan **Nästa**. 

    ![Ladda ner google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Gör följande **konfigurationsändringar** i projektet i Android Studio. 
    1.  Lägg till följande uttryck i&lt; **beroendeavsnittet** i filen build.gradle på projektnivå ( project&gt;/build.gradle). 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Lägg till följande satser i&lt;avsnittet&gt;/&lt;beroenden&gt;i avsnittet build.gradle på **dependencies** appnivå ( project app-module /build.gradle). 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Lägg till följande rad i slutet av filen build.gradle på appnivå efter avsnittet beroenden. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Välj **Synkronisera nu** i verktygsfältet. 
 
        ![build.gradle konfigurationsändringar](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Välj **Nästa**. 
7. Välj **Hoppa över det här steget**. 

    ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har laddat ner filen google-services.json till **appmappen** i ditt Android Studio-projekt kan du göra det på den här sidan. 
5. Växla till fliken **Cloud Messaging** högst upp. 
6. Kopiera och spara **servernyckeln** för senare användning. Du använder det här värdet för att konfigurera hubben.
