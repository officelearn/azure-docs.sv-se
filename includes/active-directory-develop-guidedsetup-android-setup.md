---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 02b36d8f8c8c9be2532b440bd9858766e8b524ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060852"
---
## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Vill du hämta det här exemplet Android Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip), och gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan du kör den.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt 
1.  Öppna Android Studio och välj sedan **filen** > **New** > **nytt projekt**.
2.  Namnge programmet och välj sedan **nästa**.
3.  Välj **API 21 eller nyare (Android 5.0)**, och välj sedan **nästa**.
4.  Lämna **tom aktivitet** eftersom den är Välj **nästa**, och välj sedan **Slutför**.


### <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet
1.  I Android Studio väljer **Gradle-skripten** > **build.gradle (modul: app)**.
2.  Under **beroenden**, klistra in följande kod:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>Om det här paketet

Paketet i föregående kod installerar Microsoft Authentication Library. MSAL hanterar alla token åtgärder, inklusive hämtar, cachelagring, uppdatera och ta bort.  Token behövs för att få åtkomst till API: er som skyddas av Microsoft identity-plattformen.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Skapa appens användargränssnitt

1. Gå till **res** > **layout**, och öppna sedan **activity_main.xml**. 
2. Ändra layouten aktivitet från `android.support.constraint.ConstraintLayout` eller andra till `LinearLayout`.
3. Lägg till den `android:orientation="vertical"` egenskap enligt den `LinearLayout` noden.
4. Klistra in följande kod till den `LinearLayout` nod, ersätter det aktuella innehållet:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
