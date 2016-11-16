---
title: "Kom igång med Azure Mobile Engagement för Unity Android-distribution"
description: "Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Unity-appar som distribueras till iOS-enheter."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf0b758159d475b4ed7eadb84227e4824e11ba86


---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Kom igång med Azure Mobile Engagement för Unity Android-distribution
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett Unity-program när du distribuerar till en Android-enhet.
Den här kursen använder den klassiska kursen Unity Roll a Ball som startpunkt. Följ stegen i den här [kursen](mobile-engagement-unity-roll-a-ball.md) innan du fortsätter med Mobile Engagement-integreringen som visas i kursen nedan. 

Följande krävs för den här självstudiekursen:

* [Unity Editor](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* Google Android SDK

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-android-app"></a><a id="setup-azme"></a>Konfigurera Mobile Engagement för din Android-app
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen
### <a name="import-the-unity-package"></a>Importera Unity-paketet
1. Hämta [Mobile Engagement Unity-paketet](https://aka.ms/azmeunitysdk) och spara det på din lokala dator. 
2. Gå till **Assets -> Import Package -> Custom Package** (Tillgångar -> Importera paket -> Anpassat paket) och välj paketet som du hämtade i steget ovan. 
   
    ![][70] 
3. Kontrollera att alla filer har markerats och klicka på knappen **Import** (Importera). 
   
    ![][71] 
4. När importen är klar visas de importerade SDK-filerna i projektet.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Uppdatera EngagementConfiguration
1. Öppna skriptfilen **EngagementConfiguration** i SDK-mappen och uppdatera **ANDROID\_CONNECTION\_STRING** med den anslutningssträng som du hämtade tidigare i Azure Portal.  
   
    ![][73]
2. Spara filen 
3. Kör **File -> Engagement -> Generate Android Manifest** (Arkiv -> Engagement -> Generera Android-manifest). Det här är plugin-programmet som läggs till av Mobile Engagement SDK. När du klickar på det uppdateras automatiskt dina projektinställningar. 
   
    ![][74]

> [!IMPORTANT]
> Var noga med att köra programmet varje gång du uppdaterar filen **EngagementConfiguration**, annars återspeglas inte dina ändringar i appen. 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>Konfigurera appen för grundläggande spårning
1. Öppna **PlayerController**-skriptet som är kopplat till Player-objektet för redigering. 
2. Lägg till följande med instruktionen:
   
        using Microsoft.Azure.Engagement.Unity;
3. Lägg till följande i metoden `Start()`
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Distribuera och kör appen
Kontrollera att Android SDK är installerat på datorn innan du försöker distribuera den här Unity-appen till din enhet. 

1. Anslut en Android-enhet till datorn. 
2. Öppna **File -> Build Settings** (Arkiv -> Inställningar för byggande). 
   
    ![][40]
3. Välj **Android** och klicka sedan på **Switch Platform** (Växla plattform).
   
    ![][51]
   
    ![][52]
4. Klicka på **Player settings** (Player-inställningar) och ange ett giltigt paket-ID. 
   
    ![][53]
5. Klicka slutligen på **Build And Run** (Bygg och kör).
   
    ![][54]
6. Du kan bli ombedd att ange ett mappnamn där Android-paketet ska lagras. 
7. Om allt går bra distribueras paketet till den anslutna enheten. Sedan kan du se Unity-spelet på din mobil. 

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenable-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>Uppdatera EngagementConfiguration
1. Öppna skriptfilen **EngagementConfiguration** i SDK-mappen och uppdatera **ANDROID\_GOOGLE\_NUMBER** med det **Google-projektnummer** som du tidigare hämtade från Google Cloud Developer-portalen. Det här är ett strängvärde så var noga med att omge det med dubbla citattecken. 
   
    ![][75]
2. Spara filen. 
3. Kör **File -> Engagement -> Generate Android Manifest** (Arkiv -> Engagement -> Generera Android-manifest). Det här är plugin-programmet som läggs till av Mobile Engagement SDK. När du klickar på det uppdateras automatiskt dina projektinställningar. 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>Konfigurera appen för att ta emot meddelanden
1. Öppna **PlayerController**-skriptet som är kopplat till Player-objektet för redigering. 
2. Lägg till följande i metoden `Start()`
   
        EngagementReachAgent.Initialize();
3. Nu när appen har uppdaterats kan du distribuera och köra appen på en enhet enligt anvisningarna nedan. 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png



<!--HONumber=Nov16_HO2-->


