---
title: Azure Mobile Engagement demo-appen | Microsoft Docs
description: "Beskriver var du hämtar, hur du använder och fördelarna med att använda Azure Mobile Engagement demo-appen"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement demo-appen
Vi har publicerat en Azure Mobile Engagement demo-appen för **iOS**, **Android**, och **Windows** plattformar för att hjälpa dig att hitta användbara resurser och lär dig mer om Mobile Engagement.

Appen kan du:

* Enkelt hitta länkar till Mobile Engagement resurser som videor, dokumentation, Supportforum och var du kan öka funktionsförfrågningar.
* Upplevelse exempel meddelanden som stöds av Mobile Engagement att hämta idéer för mobila program.
* Använd en referensimplementering för att undersöka hur du implementerar Mobile Engagement i din egen app. Du kan lära dig att:
  
  * Samla in analysdata.
  * Implementera avancerade scenarier för meddelanden av typerna som *mellan enskilda muskler helskärmsläge* eller *popup-*.
  * Implementera undersökningar och avsökningar.
  * Implementera tysta push data och push-scenarier.   

## <a name="app-installation"></a>Installationen av appen
Den här appen finns i följande appbutiker:

* **Windows Universal demoappen**:
  
  * Ladda ned appen på den [App för Windows store](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * Appen har utvecklats som en Windows 10 Universal-app. Källkoden är tillgängligt på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **iOS demonstrera app**:
  
  * Ladda ned appen på den [Apple store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * Appen har utvecklats iOS Swift. Källkoden är tillgängligt på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Android demoappen**:
  
  * Ladda ned appen på den [Google Play store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * Källkoden är tillgängligt på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Universella Windows-demo-appen][1]

![iOS demonstrera app][2]
![Android demo-appen][3]

## <a name="usage"></a>Användning
Du kan använda den här appen på följande sätt:

**Hämta appen på enheten från programmet store länkarna (tidigare):**

> [!IMPORTANT]
> Du behöver ett Azure-konto eller om du behöver ansluta appen till en serverdel. Appen fungerar oberoende av varandra.
> 
> 

* När du har appen på enheten, kan du gå igenom länkar i vänster-menyn för att hitta användbara resurser om Mobile Engagement.
* Vi har lagt till den [tjänstens RSS-feed](https://aka.ms/azmerssfeed) i det här programmet så att du alltid är uppdaterad om de senaste produktuppdateringarna.
* Du kan också gå igenom notification exempelscenarier upplevelse av typ av meddelanden som stöds av Mobile Engagement för varje plattform. Dessa aviseringar kan förväntas lokalt--som är kan du klicka på knapparna i skärmar att visa meddelanden upplevelse, som är identisk med att skicka meddelanden från Mobile Engagement-plattformen.

![Appmenyn för Windows][4]

![För iOS-appmenyn][5]
![appmenyn för Android][6]

**Ladda ned källkoden från GitHub länkarna (tidigare):**

* När du har laddat ned källkoden kan du öppna det i respektive utvecklingsmiljön--XCode för iOS, Android Studio för Android och Visual Studio för Windows.
* Du bör följa nästa vår [grundläggande SDK-integreringen](mobile-engagement-windows-store-dotnet-get-started.md) så att du kan ansluta den här appen till sin egen Mobile Engagement backend-instans.
  * Du måste konfigurera en anslutningssträng i appen.
  * Du måste också konfigurera push notification-plattformen för din app.
* Lägg märke till att själva appen är försett med Mobile Engagement. Därför som du öppnar appen vid anslutning till serverdelen du kommer att kunna se användarsession, aktiviteter, händelser och så vidare på den **övervakaren** fliken.
* Du måste också kunna skicka meddelanden till den här appen från din egen Mobile Engagement-instans i stället för lokala meddelanden.
  
  * Här du kan lägga till enheten som en testenhet med hjälp av den **hämta enhets-ID** menyalternativet i appen. Detta ger dig en enhets-ID som du sedan registrera som en testenhet för din plattform backend-instans.
    
    ![Enhets-ID i Windows][7]
    
    ![Enhets-ID på iOS][8]
    ![enhets-ID på Android][9]

## <a name="key-features-of-the-demo-app"></a>Viktiga funktioner i demo-appen
* Som nämnts tidigare, med den här appen har du alla viktiga resurser för Mobile Engagement i din hand. Du kan gå via länkarna på den vänstra menyn.
* Du kan få ut av appen meddelanden för varje plattform. Dessa meddelanden kan skickas som **endast meddelande**, där att klicka på meddelandet bara öppnas en inbyggd skärm för programmet (med hjälp av **djup länka**)-- eller som en **Web meddelande**, där du kan ge ytterligare HTML-innehåll från Mobile Engagement-serverdelen som ska visas när användaren klickar på meddelandet.
  
    ![Out av appen meddelanden][29]
* Du måste stänga appen för att se de out app eller system push-meddelandena på iOS. Du kan titta på implementeringen för att lägga till **knappar**, som de som har lagts till i meddelandet out av programmet för *Feedback* och *resursen* (så att användaren kan vidta åtgärder direkt från själva meddelandet).
  
    ![Out av appen meddelanden på iOS][11] ![Out-appmeddelande visas på iOS][14]
* På Android alternativ som stöds lägger till flera rader text (**stor Text**) eller en avbildning för meddelande (**översikt**) på meddelandet, tillsammans med den **knappar** (som stöds av iOS).
  
    ![Out av appen meddelanden på Android][12] ![Out-appmeddelande visas på Android][15]
* På Windows 10, kan du se hur meddelanden på samma dator. Det här meddelandet visar även i Windows 10 **Meddelandecentret**. Det finns inget stöd för att lägga till **knappar** för tillfället i Windows SDK.
  
    ![Out av appen meddelanden i Windows][10] ![Out av programmet visas i Windows][13]
* Du kan uppleva standard ”app” meddelanden för varje plattform. Det här är en tvåstegsverifiering upplevelse där en **meddelande** fönster som visas först. När du klickar på den öppnas helskärm **meddelande**, som visas i följande skärmbild. Innehållet i det här meddelandet kommer från din Mobile Engagement backend-instans. SDK innehåller mallar för både aviseringar och meddelanden. Du kan enkelt anpassa dem, som visas i den här demoappen och Lägg till vår logotyp och färgläggning.  
  
    ![Meddelanden i appen i Windows][16]
  
    ![I appen meddelanden på iOS][17]  ![Meddelanden i appen på Android][18]
  
    **iOS**, **Android**
* Du kan också använda den **kategori** funktion i Mobile Engagement du anpassar den här standard. Vi har visas två vanliga sätt att ändra upplevelsen av meddelanden i demoappen. Observera att funktionen kategori inte är ännu stöds i Windows SDK.
  
    **Helskärmsläge mellan enskilda muskler:**
  
    ![Avisering via app - mellan enskilda muskler kategori][30]
  
    ![Mellan enskilda muskler kategori på iOS][21]     ![Mellan enskilda muskler kategori på Android][22]
  
    **Popup-meddelande:**
  
    ![Avisering via app - popup-kategori][31]
  
    ![Popup-meddelande på iOS][19]    ![Popup-meddelande på Android][20]

**iOS**, **Android**

* Mobile Engagement stöder också en specialiserad typ av avisering via app kallas **Polls**. På så sätt kan du skicka ut snabb undersökningar till användarna segmenterade app. Du kan lägga till frågor och alternativ för varje fråga som i följande skärmbild. Detta kommer sedan hämta visas som ett meddelande i appen till app-användare.   
  
    ![Avsökningen meddelanden][32]
  
    ![Undersökning i Windows][26]
  
    ![En undersökning på iOS][27]   ![Undersökning på Android][28]

**iOS**, **Android**

* Mobile Engagement även stöd för att skicka tyst **Data Push** meddelanden. Med dessa aviseringar kan du skicka data från din tjänst (till exempel JSON data i följande exempel), som du kan hantera i din app och vidta vissa åtgärder. Ett exempel är hur vi ändrar priset på en artikel selektivt med hjälp av data-push-meddelanden.
  
    ![Data-push-meddelanden][33]
  
    ![Data-push-meddelanden i Windows][23]
  
    ![Data-push-meddelanden på iOS][24]  ![Data-push-meddelande på Android][25]

**iOS**, **Android**

> [!NOTE]
> Du kan visa detaljerade stegvisa anvisningar för någon av dessa aviseringar genom att klicka på **Klicka här för instruktioner om hur du skickar dessa meddelanden från Mobile Engagement-plattformen** på alla exempel anmälan skärm.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
