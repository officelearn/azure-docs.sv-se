---
title: Komma igång med Azure Mobile Services för Android-appar (JavaScript-serverdel)
description: Den här kursen hjälper dig att komma igång med Azure Mobile Services för Android-utveckling (JavaScript-serverdel).
services: mobile-services
documentationcenter: android
author: RickSaling
manager: reikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: ricksal

---
# Komma igång med Mobile Services för Android (JavaScript-serverdel)
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Motsvarande avsnitt för Mobile Apps finns i [Skapa en iOS-app i Azure Mobile Apps](../app-service-mobile/app-service-mobile-android-get-started.md).
> 
> 

I den här kursen får du veta hur du lägger till en molnbaserad serverdelstjänst för en Android-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel **To do list**-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services/player]
> 
> 

En skärmbild från den färdiga appen:

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Krav
För den här kursen krävs [Android Developer Tools](https://developer.android.com/sdk/index.html) (utvecklingsverktyg för Android) som omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen. Android 4.2 eller senare krävs.

Det nedladdade snabbstartsprojektet innehåller Azure Mobile Services SDK för Android.

> [!IMPORTANT]
> För den här kursen behöver du ett Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).
> 
> 

## Skapa en ny mobiltjänst
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny Android-app
När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet får du skapa en ny Android-app som är ansluten till mobiltjänsten.

1. Gå till den klassiska Azure Portal, klicka på **Mobile Services** och sedan på mobiltjänsten du precis skapat.
2. Välj fliken quickstart, klicka på **Android** under **Choose platform** (Välj plattform) och expandera **Create a new Android app** (Skapa en ny Android-app).
   
    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)
   
    Därmed visas tre enkla steg för att skapa en Android-app som är ansluten till din mobiltjänst.
   
    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)
3. Ladda ned och installera [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125) (utvecklingsverktyg för Android) på den lokala eller virtuella datorn, om du inte redan gjort det.
4. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.
5. Ladda ned appen genom att trycka på knappen **Ladda ned**.

## Köra Android-appen
[!INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Nästa steg
Nu när du har slutfört snabbstartskursen kan du gå vidare och lära dig hur du utför fler viktiga uppgifter i Mobile Services:

* [Komma igång med data]
  <br/>Läs mer om lagring och hämtning av data med Mobile Services.
* [Komma igång med autentisering]
  <br/>Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Komma igång med push-meddelanden]
  <br/>Läs om hur du skickar väldigt enkla push-meddelanden till appen.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- URLs. -->
[Komma igång (Eclipse)]: mobile-services-android-get-started-ec.md
[Komma igång med data]: mobile-services-android-get-started-data.md
[Komma igång med autentisering]: mobile-services-android-get-started-users.md
[Komma igång med push-meddelanden]: mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533




<!--HONumber=sep16_HO1-->


