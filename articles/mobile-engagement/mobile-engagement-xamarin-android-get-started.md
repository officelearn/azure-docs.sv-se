---
title: Kom igång med Azure Mobile Engagement för Xamarin.Android
description: Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Xamarin.Android-appar.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 4ac6e85a0143fc083c22a92e99503689f463d813
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Kom igång med Azure Mobile Engagement för Xamarin.Android-appar
> [!IMPORTANT]
> Azure Mobile Engagement dras tillbaka den 31 mars 2018. Den här sidan tas bort strax efter det.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett Xamarin.Android-program.
I den här kursen går vi igenom ett enkelt scenario för sändning med Mobile Engagement. I kursen får du skapa en tom Xamarin.Android-app som samlar in grundläggande data och tar emot push-meddelanden med hjälp av Google Cloud Messaging (GCM).

> [!NOTE]
> Tjänsten Azure Mobile Engagement kommer att dras tillbaka i mars 2018 och är för närvarande endast tillgänglig för befintliga kunder. Mer information finns i [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

För den här kursen behöver du följande:

* [Xamarin Studio](http://xamarin.com/studio). Du kan även använda Visual Studio med Xamarin men i den här kursen används Xamarin Studio. Det finns installationsanvisningar i avsnittet om [konfiguration och installation för Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurera Mobile Engagement för din Android-app
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen
I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. 

Vi kommer att skapa en grundläggande app i Xamarin Studio för att demonstrera integrationen.

### <a name="create-a-new-xamarinandroid-project"></a>Skapa ett nytt Xamarin.Android-projekt
1. Öppna **Xamarin Studio** och gå till **File** -> **New** -> **Solution** (Arkiv -> Ny -> Lösning). 
   
    ![][1]
2. Välj **Android App** (Android-app) och kontrollera att det valda språket är **C#**. Klicka sedan på **Next** (Nästa).
   
    ![][2]
3. Fyll i **appens namn** och **organisations-ID:t**. Var noga med att markera **Google Play Services** och klicka sedan på **Next** (Nästa). 
   
    ![][3]
4. Uppdatera **projektets namn**, **lösningens namn** och **platsen** om det behövs, och klicka på **Create** (Skapa).
   
    ![][4]

Xamarin Studio skapar appen där Mobile Engagement ska integreras. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Anslut appen till Mobile Engagement-serverdelen
1. Högerklicka på mappen **Packages** (Paket) i lösningsfönstret och välj **Add Packages** (Lägg till paket).
   
    ![][5]
2. Sök efter **Microsoft Azure Mobile Engagement Xamarin SDK** och lägg till den i lösningen.  
   
    ![][6]
3. Öppna **MainActivity.cs** och lägg till följande med instruktionerna:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. Lägg till följande i metoden `OnCreate` för att initiera anslutningen till Mobile Engagement-serverdelen. Var noga med att lägga till din **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Lägga till behörigheter och en tjänstedeklaration
1. Öppna filen **Manifest.xml** under egenskapsmappen. Välj fliken Source (Källa) så att du uppdaterar XML-källan direkt.
2. Lägg till följande behörigheter i Manifest.xml (som finns under mappen **Properties** [Egenskaper]) i ditt projekt omedelbart före eller efter taggen `<application>`:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Lägg till följande mellan taggarna `<application>` och `</application>` för att deklarera agenttjänsten:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. Ersätt `"<Your application name>"` i etiketten i den kod som du precis klistrade in. Detta visas på **inställningsmenyn** där användarna kan se vilka tjänster som körs på enheten. Du kan till exempel lägga till ordet ”Service” eller ”Tjänst” i etiketten.

### <a name="send-a-screen-to-mobile-engagement"></a>Skicka en skärm till Mobile Engagement
För att kunna börja skicka data och försäkra dig om att användarna är aktiva måste du skicka minst en skärm till Mobile Engagement-serverdelen. Kontrollera att `MainActivity` ärver från `EngagementActivity` istället för från `Activity` för att göra detta.

    public class MainActivity : EngagementActivity

Som alternativ, om du inte kan ärva från `EngagementActivity`, måste du lägga till `.StartActivity`- och `.EndActivity`-metoder i `OnResume` respektive `OnPause`.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
Med Mobile Engagement kan du samverka med användarna, nå ut till dem och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
