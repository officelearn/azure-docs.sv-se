---
title: Azure Mobile Engagement Android SDK Integration
description: Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4c3b8f60333e6096411aad8499bb4bfc36e53f3c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-adm-with-engagement"></a>Hur du integrerar ADM med Engagement
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!IMPORTANT]
> Du måste följa integration proceduren i hur du integrerar Engagement på Android dokumentet innan du följer den här guiden.
> 
> Det här dokumentet är användbar bara om du redan har integrerat med Reach-modulen och planera att skicka Amazon-enheter. Om du vill integrera Reach-kampanjer i ditt program, Läs först hur du integrerar Engagement nå på Android.
> 
> 

## <a name="introduction"></a>Introduktion
Integrera ADM kan ditt program ska kunna skickas när måldatorn Amazon Android-enheter.

ADM-nyttolaster pushas till SDK alltid innehålla den `azme` nyckel i dataobjektet. Därför om du använder ADM för andra ändamål i ditt program, kan du filtrera push-meddelanden baserat på nyckeln.

> [!IMPORTANT]
> Endast Amazon Kindle enheter som kör Android 4.0.3 eller senare som stöds av Amazon Device Messaging; Du kan dock integrera koden på ett säkert sätt på andra enheter.
> 
> 

## <a name="sign-up-to-adm"></a>Registrera dig för ADM
Om du inte redan har gjort, måste du aktivera ADM på Amazon-konto.

Förfarandet som beskrivs i: [ <https://developer.amazon.com/sdk/adm/credentials.html> ].

När du slutför proceduren får du:

* OAuth-autentiseringsuppgifter (klient-ID och en Klienthemlighet) för Engagement för att kunna push dina enheter.
* En API-nyckel som integreras i ditt program.

## <a name="sdk-integration"></a>SDK-integration
### <a name="managing-device-registrations"></a>Hantera registreringar för enhet
Varje enhet måste skicka ett kommando för registrering till ADM-servrar, annars de inte kan nås.

Om du redan använder den [ADM-klientbiblioteket], och redan har [integrerad ADM] du kan gå direkt till android-sdk-adm-ta emot.

Om du inte har integrerat ADM, har Engagement ett enklare sätt att aktivera den i ditt program:

Redigera din `AndroidManifest.xml` fil:

* Lägg till namnområdet för Amazon filen ska börja så här:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* I den `<application/>` tagg, lägga till det här avsnittet:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Du kan ha ett build-fel om ditt mål för projektgenerering är lägre än Android 2.1 när du lägger till amazon-taggen. Du måste använda ett **Android 2.1 +** skapa mål (oroa dig inte, kan du fortfarande ha en `minSdkVersion` värdet 4).
* Integrera ADM API-nyckeln som en tillgång genom att följa [proceduren].

Följ sedan anvisningarna i nästa avsnitt.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Kommunicera registrerings-id till Engagement Push-tjänsten och ta emot meddelanden
För att kunna kommunicera registrerings-id för enheten till Engagement Push-tjänsten och ta emot dess meddelanden, lägger du till följande till din `AndroidManifest.xml` filen inuti den `<application/>` tagga (även om du använder ADM utan behov av):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Se till att du har följande behörigheter i din `AndroidManifest.xml` (innan den `</application>` tagg).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Bevilja Engagement OAuth-autentiseringsuppgifter
Skicka dina inloggningsuppgifter för OAuth (klient-ID och Klienthemlighet) i Engagement-portalen.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM-klientbiblioteket]:https://developer.amazon.com/sdk/adm/setup.html
[integrerad ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[proceduren]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
