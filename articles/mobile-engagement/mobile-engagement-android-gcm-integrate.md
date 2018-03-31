---
title: Azure Mobile Engagement Android SDK Integration
description: Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Hur du integrerar GCM med Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!IMPORTANT]
> Du måste följa integration proceduren i hur du integrerar Engagement på Android dokumentet innan du följer den här guiden.
> 
> Det här dokumentet är användbar bara om du redan har integrerat med Reach-modulen och planera att skicka Google Play-enheter. Om du vill integrera Reach-kampanjer i ditt program, Läs först hur du integrerar Engagement nå på Android.
> 
> 

## <a name="introduction"></a>Introduktion
Integrera GCM kan ditt program ska kunna skickas.

GCM-nyttolaster pushas till SDK alltid innehålla den `azme` nyckel i dataobjektet. Därför om du använder GCM för andra ändamål i ditt program, kan du filtrera push-meddelanden baserat på nyckeln.

> [!IMPORTANT]
> Endast enheter som kör Android 2.2 eller senare med Google Play installerad och som har Google bakgrund anslutning aktiverad flyttas med GCM; Du kan dock integrera koden på ett säkert sätt på stöds inte enheter (bara avsikter används).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Skapa ett Google Cloud Messaging-projekt med API-nyckel
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>SDK-integration
### <a name="managing-device-registrations"></a>Hantera registreringar för enhet
Varje enhet måste skicka ett kommando för registrering för Google-servrarna, annars de inte kan nås.

En enhet kan också avregistrera från GCM-meddelanden (enheten är automatiskt avregistrera om programmet har avinstallerats).

Om du inte använder [Google Play-SDK] eller du inte redan skickar registrering avsikten själv, kan du se Engagement registreras enheten automatiskt åt dig.

För att möjliggöra detta, Lägg till följande till din `AndroidManifest.xml` filen inuti den `<application/>` tagg:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Kommunicera registrerings-id till Engagement Push-tjänsten och ta emot meddelanden
För att kunna kommunicera registrerings-id för enheten till Engagement Push-tjänsten och ta emot dess meddelanden, lägger du till följande till din `AndroidManifest.xml` filen inuti den `<application/>` tagga (även om du hanterar enheten registreringar själv):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Se till att du har följande behörigheter i din `AndroidManifest.xml` (när den `</application>` tagg).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Bevilja Mobile Engagement åtkomst till din GCM API-nyckel
Följ [handboken](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) bevilja Mobile Engagement åtkomst till din GCM API-nyckel.

[Google Play-SDK]:https://developers.google.com/cloud-messaging/android/start
