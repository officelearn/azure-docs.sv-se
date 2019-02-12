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
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823298"
---
1. Öppna **Android Studio**, välj **Verktyg** på menyn och välj **SDK Manager**. 
2. Välj målversionen av det Android-SDK som används i projektet och välj **Visa paketinformation**. 

    ![Android SDK Manager – välj målversion](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Välj **Google APIs** om det inte redan är installerat.

    ![Android SDK Manager – Google APIs valt](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Växla till fliken **SDK-verktyg**. Om du inte redan har installerat Google Play Service väljer du **Google Play Services** så som visas i bilden nedan. Klicka sedan på **Tillämpa** för att installera. Anteckna SDK-sökvägen för användning i ett senare steg.

    ![Android SDK Manager – Google Play Services valt](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Om du ser dialogrutan **Bekräfta ändringen** väljer du **OK**. De begärda komponenterna installeras. Välj **Slutför** när komponenterna har installerats.
4. Välj **OK** för att stänga dialogrutan **Inställningar för nya projekt**.  
5. Öppna filen `build.gradle` i katalogen **app** och lägg till följande rad under `dependencies`. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Välj ikonen **Synkronisera nu** i verktygsfältet.

    ![Synkronisering med Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Öppna **AndroidManifest.xml** och lägg till den här taggen till *program*-taggen.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
