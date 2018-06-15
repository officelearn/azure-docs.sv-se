---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33835788"
---
1. Öppna Android SDK Manager genom att klicka på ikonen i verktygsfältet för Android Studio eller genom att klicka på **Verktyg** > **Android** > **SDK Manager** i menyn. Hitta målversionen av Android SDK:n som används i ditt projekt, öppna den genom att klicka på **Visa paketinformation** och välj **Google API:er** om det inte redan har installerats.
2. Klicka på fliken **SDK-verktyg**. Om du inte redan har installerat Google Play-tjänsten klickar du på **Google Play Services** enligt nedan. Klicka sedan på **Tillämpa** för att installera. 
   
    Anteckna SDK-sökvägen för användning i ett senare steg. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Öppna filen **build.gradle** i appkatalogen.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Lägg till följande rad under *beroenden*: 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Klicka på ikonen **Synkronisera projektet med Gradle-filer** i verktygsfältet.
6. Öppna **AndroidManifest.xml** och lägg till den här taggen till *program*-taggen.
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

