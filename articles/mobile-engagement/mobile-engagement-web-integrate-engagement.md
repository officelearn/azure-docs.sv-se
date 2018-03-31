---
title: Azure Mobile Engagement Web SDK integration | Microsoft Docs
description: De senaste uppdateringarna och procedurer för Azure Mobile Engagement Web SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrera Azure Mobile Engagement i ett webbprogram
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Procedurerna i den här artikeln beskriver det enklaste sättet att aktivera analyser och övervakning funktioner i Azure Mobile Engagement i ditt webbprogram.

Följ stegen för att aktivera loggen rapporterna som behövs för att beräkna all statistik om användare, sessioner, aktiviteter, krascher och technicals. För program-beroende statistik, till exempel händelser, fel och jobb, måste du aktivera loggen rapporter manuellt med hjälp av Azure Mobile Engagement-API: et. Mer information lär du dig [hur du använder avancerade Mobile Engagement API-märkning i ett webbprogram](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduktion
[Hämta Azure Mobile Engagement Web SDK](http://aka.ms/P7b453).
Mobile Engagement Web SDK levereras som en JavaScript-fil, azure-engagement.js som du behöver ta på varje sida i din webbplats eller programmet.

> [!IMPORTANT]
> Innan du kör skriptet måste du köra ett skript eller kod kodavsnitt som du skriver för att konfigurera Mobile Engagement för ditt program.
> 
> 

## <a name="browser-compatibility"></a>Webbläsarkompatibilitet
Mobile Engagement Web SDK använder inbyggd JSON kodning och avkodning förutom domänerna AJAX-begäranden (förlita dig på specifikationen W3C CORS). Den är kompatibel med följande webbläsare:

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5 +
* Chrome 4 +
* Safari 6+
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Konfigurera Mobile Engagement
Skriva ett skript som skapar en global `azureEngagement` JavaScript-objekt, som i följande exempel. Eftersom din plats kan ha flera sidor, förutsätter det här exemplet att det här skriptet ingår i varje sida. I det här exemplet JavaScript-objekt med namnet `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

Den `connectionString` värdet för ditt program visas i Azure-portalen.

> [!NOTE]
> `appVersionName` och `appVersionCode` är valfria. Vi rekommenderar dock att du konfigurerar dem så att analytics kan bearbeta versionsinformation.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Inkludera Mobile Engagement skript i sidorna
Lägga till Mobile Engagement-skript på sidorna i något av följande sätt:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Eller:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
När skriptet Mobile Engagement Web SDK har lästs in, skapas den **engagement** alias SDK-API: er. Du kan inte använda detta alias för att definiera SDK-konfiguration. Det här aliaset används som en referens i den här dokumentationen.

Observera att om aliaset som standard i konflikt med en annan global variabel från din sida, du kan ändra den i konfigurationen på följande sätt innan du läser in Mobile Engagement Web SDK:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Grundläggande reporting
Grundläggande rapportering i Mobile Engagement innehåller statistik för session-nivå, till exempel statistik om användare, sessioner, aktiviteter och krascher.

### <a name="session-tracking"></a>Sessionsspårning
En Mobile Engagement-session är uppdelad i en serie aktiviteter, alla identifierade med ett namn.

I klassiska webbplats rekommenderar vi att du deklarera en annan aktivitet på varje sida i din webbplats. För en webbplats eller webbprogram program där den aktuella sidan aldrig ändras, kanske du vill spåra aktiviteter i mindre skala, som på sidan.

Oavsett hur för att starta eller ändra den aktuella användare aktiviteten anropa den `engagement.agent.startActivity` funktion. Exempel:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Mobile Engagement-servern avslutar automatiskt en öppen session inom tre minuter efter appen på sidan är stängd.

Alternativt kan du avsluta en session manuellt genom att anropa `engagement.agent.endActivity`. Anger den aktuella användare aktiviteten inaktiv.  Sessionen avslutas 10 sekunder senare om ett nytt anrop till `engagement.agent.startActivity` fortsätter sessionen.

Du kan konfigurera fördröjning 10 sekunder i globala engagement-objekt på följande sätt:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Du kan inte använda `engagement.agent.endActivity` i den `onunload` motringning eftersom du inte göra AJAX-anrop i det här skedet.
> 
> 

## <a name="advanced-reporting"></a>Avancerad rapportering
Du kan också om du vill rapportera programspecifika händelser, fel och jobb behöver du använda API: T för Mobile Engagement. Du har åtkomst till Mobile Engagement-API via den `engagement.agent` objekt.

Du kan komma åt alla av de avancerade funktionerna i Mobile Engagement i Mobile Engagement-API. API: et beskrivs i artikeln [hur du använder avancerade Mobile Engagement API-märkning i ett webbprogram](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Anpassa URL: er som används för AJAX-anrop
Du kan anpassa URL: er som använder Mobile Engagement Web SDK. Till exempel definiera om loggnings-URL (SDK slutpunkten för loggning) kan du åsidosätta konfigurationen så här:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Om URL-funktioner returnerar en sträng som börjar med `/`, `//`, `http://`, eller `https://`, schemat som standard används inte. Som standard den `https://` schema används för dessa URL: er. Om du vill anpassa standardschema åsidosätta konfigurationen, så här:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
