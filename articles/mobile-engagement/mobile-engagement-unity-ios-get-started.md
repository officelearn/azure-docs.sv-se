---
title: "Kom igång med Azure Mobile Engagement för Unity iOS-distribution"
description: "Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Unity-appar som distribueras till iOS-enheter."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d


---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Kom igång med Azure Mobile Engagement för Unity iOS-distribution
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett Unity-program när du distribuerar till en iOS-enhet.
Den här kursen använder den klassiska kursen Unity Roll a Ball som startpunkt. Följ stegen i den här [kursen](mobile-engagement-unity-roll-a-ball.md) innan du fortsätter med Mobile Engagement-integreringen som visas i kursen nedan. 

Följande krävs för den här självstudiekursen:

* [Unity Editor](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode Editor

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Konfigurera Mobile Engagement för din iOS-app
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
1. Öppna skriptfilen **EngagementConfiguration** i SDK-mappen och uppdatera **IOS\_CONNECTION\_STRING** med den anslutningssträng som du tidigare hämtade i Azure Portal.  
   
    ![][73]
2. Spara filen. 

### <a name="configure-the-app-for-basic-tracking"></a>Konfigurera appen för grundläggande spårning
1. Öppna **PlayerController**-skriptet som är kopplat till Player-objektet för redigering. 
2. Lägg till följande med instruktionen:
   
        using Microsoft.Azure.Engagement.Unity;
3. Lägg till följande i metoden `Start()`
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Distribuera och kör appen
1. Anslut en iOS-enhet till datorn. 
2. Öppna **File -> Build Settings** (Arkiv -> Inställningar för byggande). 
   
    ![][40]
3. Välj **iOS** och klicka sedan på **Switch Platform** (Växla plattform).
   
    ![][41]
   
    ![][42]
4. Klicka på **Player settings** (Player-inställningar) och ange ett giltigt paket-ID. 
   
    ![][53]
5. Klicka slutligen på **Build And Run** (Bygg och kör).
   
    ![][54]
6. Du kan bli ombedd att ange ett mappnamn där iOS-paketet ska lagras. 
   
    ![][43]
7. Om allt går bra kompileras projektet och sedan kan du öppna det i XCode-programmet. 
8. Kontrollera att **paket-ID:t** är korrekt i projektet.  
   
    ![][75]
9. Kör appen i XCode så att paketet distribueras till den anslutna enheten. Sedan kan du se Unity-spelet på din mobil. 

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenable-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
Du behöver inte konfigurera något annat i appen för att ta emot meddelanden utan den är redan inställd för det.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png



<!--HONumber=Nov16_HO2-->


