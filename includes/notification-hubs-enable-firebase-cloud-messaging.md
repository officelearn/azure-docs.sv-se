---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509086"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. På den **Lägg till Firebase i din Android-app** utför följande steg: 
    1. För **Android paketnamn**, Kopiera värdet för din **applicationId** i ditt programs build.gradle-filen. I det här exemplet har `com.fabrikam.fcmtutorial1app`. 

        ![Ange paketnamnet på](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **registrera app**. 
4. Välj **hämta google-services.json**, spara filen till den **app** mappen i projektet och välj sedan **nästa**. 

    ![Hämta google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Se följande **konfigurationsändringar** till ditt projekt i Android Studio. 
    1.  I projektet på servernivå build.gradle-filen (&lt;projekt&gt;/build.gradle), Lägg till följande instruktion för att den **beroenden** avsnittet. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. I appnivå build.gradle-filen (&lt;projekt&gt;/&lt;app-module&gt;/build.gradle), Lägg till följande instruktion för att den **beroenden** avsnittet. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Lägg till följande rad i slutet av appnivå build.gradle-filen efter avsnittet beroenden. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Välj **synkronisera nu** i verktygsfältet. 
 
        ![build.gradle konfigurationsändringar](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Välj **Nästa**. 
7. Välj **hoppa över detta steg**. 

    ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har hämtat filen google-services.json till den **app** mapp i ditt Android Studio-projekt som du kan göra så vidare den här sidan. 
5. Växla till den **Cloud Messaging** fliken högst upp. 
6. Kopiera och spara den **servernyckel** för senare användning. Du kan använda det här värdet för att konfigurera din hubb.
