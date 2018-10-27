---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0154aac14168c9d897698a15e31b3124b208db46
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142476"
---
## <a name="add-the-applications-registration-to-your-code"></a>Lägg till programmets registrering i din kod

I det här steget kan du behöva lägga till programmet / klient-ID: T i projektet.

1. Öppna `MainActivity` (under `app`  >  `java`  >  *`{host}.{namespace}`*)
2. Ersätt den rad som börjar med `final static String CLIENT_ID` med:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Öppna: `app` > `manifests` > `AndroidManifest.xml`
4. Lägg till att följande aktiviteter `manifest\application`. Den`BrowserTabActivity` tillåter Microsoft att ringa tillbaka till ditt program när du har slutfört autentiseringen:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```
