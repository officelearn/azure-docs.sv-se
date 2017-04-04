---
title: "Komma igång med Azure Mobile Engagement för Cordova/Phonegap"
description: "Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Cordova/Phonegap-appar."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Komma igång med Azure Mobile Engagement för Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I det här avsnittet beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i en mobilapp utvecklad med Cordova.

I den här självstudiekursen skapar vi en tom Cordova-app med Mac och integrerar sedan Mobile Engagement SDK. Den samlar in grundläggande analysdata och tar emot push-meddelanden via Apple Push Notification System (APNS) för iOS och Google Cloud Messaging (GCM) för Android. Vi distribuerar sedan appen till en iOS- eller Android-enhet och testar den. 

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

För den här kursen behöver du följande:

* XCode som kan laddas ned och installeras från Mac App Store (för att distribuera till iOS)
* [Android SDK & Emulator](http://developer.android.com/sdk/installing/index.html) (för att distribuera till Android)
* Certifikat för push-meddelanden (.p12) som kan hämtas från Apple Dev Center för APNS
* Ett GCM-projektnummer som kan hämtas från Google Developer Console för GCM
* [Cordova-pluginprogrammet för Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Du hittar källkoden och filen ReadMe (Viktigt) för Cordova-plugin-programmet på [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Konfigurera Mobile Engagement för din Cordova-app
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen
I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. 

Vi skapar en grundläggande app i Cordova för att demonstrera integrationen:

### <a name="create-a-new-cordova-project"></a>Skapa ett nytt Cordova-projekt
1. Starta fönstret *Terminal* på Mac-datorn och skriv in följande för att skapa ett nytt Cordova-projekt utifrån standardmallen. Kontrollera att publiceringsprofilen som du använder för att distribuera iOS-appen med använder ”com.mycompany.myapp” som app-ID. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Kör följande om du vill konfigurera ditt projekt för **iOS** och kör den i iOS-emulatorn:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Kör följande om du vill konfigurera ditt projekt för **Android** och köra den i Android-emulatorn: Kontrollera att dina inställningar för Android SDK Emulator har Google API:er (Google Inc.) som mål med CPU/ABI som Google API:er ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Lägg till pluginprogrammet Cordova Console. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Anslut appen till Mobile Engagement-serverdelen
1. Installera pluginprogrammet Cordova Console för Azure Mobile Engagement och ange följande variabler vid konfigurationen:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Reach-ikon för Android*: Måste utgöras av namnet på resursen utan filtillägg eller drawable-prefix (exempel: mynotificationicon). Ikonfilen måste kopieras till ditt Android-projekt (platforms/android/res/drawable).

*Reach-ikon för iOS*: Måste utgöras av namnet på resursen med filtillägg (exempel: mynotificationicon.png). Ikonfilen måste läggas till i ditt iOS-projekt med XCode (använd menyn Add Files [Lägg till filer]).

## <a id="monitor"></a>Aktivera realtidsövervakning
1. Redigera **www/js/index.js** i Cordova-projektet och lägg till ett anrop till Mobile Engagement som deklarerar en ny aktivitet efter att händelsen *deviceReady* har tagits emot.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Kör programmet:
   
   * **För iOS**
     
       Gå till fönstret `Terminal` och starta din app i en ny instans av simulatorn genom att köra följande:
     
           cordova run ios
   * **För Android**
     
       Gå till fönstret `Terminal` och starta din app i en ny instans av emulatorn genom att köra följande:
     
           cordova run android
3. Följande visas i loggarna för konsolen:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används till detta heter REACH i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Konfigurera push-autentiseringsuppgifter för Mobile Engagement
Om du vill låta Mobile Engagement skicka push-meddelanden från dig så behöver du ge det åtkomst till ditt Apple iOS-certifikat eller GCM-serverns API-nyckel. 

1. Gå till portalen för Mobile Engagement. Kontrollera att du befinner dig i appen som vi använder för det här projektet och klicka sedan på **starta**-knappen längst ned:
   
    ![][1]
2. Du hamnar på inställningssidan i Engagement-portalen. Därifrån klickar du dig fram till avsnittet för **systemspecifik push-avisering**:
   
    ![][2]
3. Konfigurera iOS-certifikat/API-nyckel för GCM-server
   
    **[iOS]**
   
    a. Välj ditt .p12-certifikat, ladda upp det och ange ditt lösenord:
   
    ![][3]
   
    **[Android]**
   
    a. Klicka på redigeringsikonen framför **API-nyckeln** i avsnittet GCM-inställningar och klistra in GCM-servernyckeln. Klicka på **OK** i det popup-fönster som visas. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Aktivera push-meddelanden i Cordova-appen
Redigera **www/js/index.js** för att lägga till anropet till Mobile Engagement som begär push-meddelanden och deklarerar en hanterare:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Kör appen
**[iOS]**

1. Vi använder XCode för att skapa appen, distribuera den och testa push-meddelanden på enheten eftersom iOS endast tillåter push-avisering till en fysisk enhet. Gå till platsen där Cordova-projektet skapats och navigera till **...\platforms\ios**. Öppna filen .xcodeproj i XCode. 
2. Skapa och distribuera Cordova-appen till iOS-enheten med hjälp av det konto som har den etableringsprofil som innehåller det certifikat du just har laddat upp till Mobile Engagement-portalen och det app-ID som matchar det du angav när du skapade Cordova-appen. Se *Bundle identifier* (Paket-ID) i filen **Resources\*-info.plist** i XCode för matchning. 
3. Då visas ett standardmeddelande i iOS där du ombeds ge din tillåtelse till att appen skickar meddelanden. Ge din tillåtelse. 

**[Android]**

Du behöver bara använda emulatorn för att köra Android-appen eftersom GCM-meddelanden stöds i Android-emulatorn. 

    cordova run android

## <a id="send"></a>Skicka ett meddelande till din app
Nu skapar vi en enkel kampanj med push-meddelanden till den app som körs på enheten:

1. Gå till fliken **Räckvidd** i Mobile Engagement-portalen
2. Klicka på **Nytt meddelande** för att skapa en kampanj med push-meddelanden
   
    ![][6]
3. Ange indata för att skapa din kampanj **[Android]**
   
   * Ge kampanjen ett **namn**. 
   * Gå till **Leveranstyp** och välj *Systemmeddelande* *Enkelt*
   * Gå till **Leveranstid** och välj *När som helst*.
   * Ange en **rubrik** som hamnar först i ditt push-meddelande.
   * Gå till **Meddelande** och skriv en meddelandetext (brödtext). 
     
     ![][11]
4. Ange indata för att skapa din kampanj **[iOS]**
   
   * Ge kampanjen ett **namn**. 
   * Gå till **Leveranstid** och välj *Enbart utanför appen*.
   * Ange en **rubrik** som hamnar först i ditt push-meddelande.
   * Gå till **Meddelande** och skriv en meddelandetext (brödtext). 
     
     ![][12]
5. Rulla nedåt och välj **Endast meddelande** i innehållsavsnittet.
   
    ![][8]
6. [Valfritt] Du kan även ange åtgärdens URL-adress. Se till att den använder ett URL-schema som stöds i konfigurationen av plugin-programmets variabel **AZME\_REDIRECT\_URL**, till exempel *myapp://test*.  
7. Nu är du klar med att ställa in en grundläggande kampanj. Rulla nedåt igen och klicka på **skapa**-knappen för att spara din kampanj.
8. Slutligen **aktiverar** du din kampanj
   
    ![][10]
9. Du bör nu se ett push-meddelande på enheten eller i emulatorn som en del av den här kampanjen. 

## <a id="next-steps"></a>Nästa steg
[Översikt över alla metoder som är tillgängliga med Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


