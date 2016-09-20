
<properties
    pageTitle="Komma igång med Azure Mobile Services för Android-appar"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för Android-utveckling"
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="ricksal"/>


# <a name="getting-started"> </a>Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en iOS-app i Azure Mobile Apps](../app-service-mobile/app-service-mobile-android-get-started.md).

I den här kursen får du veta hur du lägger till en molnbaserad serverdelstjänst för en Android-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel _To do list_-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används .NET-språk som stöds med Visual Studio för affärslogik på serversidan och för att hantera mobiltjänsten. Om du vill skriva affärslogik på serversidan i JavaScript kan du läsa [JavaScript-serverdelsversion](mobile-services-android-get-started.md) i det här avsnittet.

En skärmbild från den färdiga appen:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

För den här kursen krävs [Android Developer Tools] (utvecklingsverktyg för Android) med [Android Studio] som omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen. Android 4.2 eller senare krävs.

Det nedladdade snabbstartsprojektet innehåller Mobile Services SDK för Android.

> [AZURE.IMPORTANT] Du behöver ett Azure-konto för att slutföra den här självstudien. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## <a name="create-new-service"> </a>Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Ladda ned mobiltjänsten till den lokala datorn

Nu när du har skapat den mobila tjänsten laddar du ned det anpassade mobiltjänstprojektet som du kan köra på den lokala eller virtuella datorn.

1. Klicka på mobiltjänsten du precis skapade och gå in på fliken quickstart. Klicka på **Android** under **Choose platform** (Välj plattform) och expandera **Create a new Android app** (Skapa en ny Android-app).

    ![][1]

2. Om du inte redan gjort det, laddar du ned och installerar [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934), eller en senare version.

3. I steg två klickar du på **Download** (Ladda ned) under **Download and publish your service to the cloud** (Ladda ned och publicera tjänsten i molnet).

    Visual Studio-projektet som implementerar din mobiltjänst laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Testa mobiltjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicera mobiltjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Skapa en ny Android-app

I det här avsnittet får du skapa en ny Android-app som är ansluten till mobiltjänsten.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

2. Välj fliken quickstart, klicka på **Android** under **Choose platform** (Välj plattform) och expandera **Create a new Android app** (Skapa en ny Android-app).

    ![][2]

3. Ladda ned och installera [Android Developer Tools][Android SDK] på den lokala eller virtuella datorn, om du inte redan gjort det.

4. Under **Download and run your app** (Ladda ned och kör appen) klickar du på **Download** (Ladda ned).

    Projektet för _To do list_-appen som är ansluten till din mobiltjänst laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Köra Android-appen

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Nästa steg
Nu när du har slutfört snabbstartskursen kan du gå vidare och lära dig hur du utför fler viktiga uppgifter i Mobile Services:

* [Lägg till push-meddelanden i appen]
  <br/>Läs om hur du skickar väldigt enkla push-meddelanden till appen.

* [Lägg till autentisering i appen]
  <br/>Läs om hur du begränsar åtkomsten till serverdelsdata till specifika registrerade användare för appen.

* [Felsöka en Mobile Services .NET-serverdel]
  <br/> Läs om hur du diagnostiserar och åtgärdar problem som kan uppstå med Mobile Services .NET-serverdelen.

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Komma igång (Eclipse)]: mobile-services-dotnet-backend-android-get-started-ec.md
[Lägga till push-meddelanden för appen]: mobile-services-dotnet-backend-android-get-started-push.md
[Lägg till autentisering i appen]: mobile-services-dotnet-backend-android-get-started-auth.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Felsöka en Mobile Services .NET-serverdel]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Klassisk Azure-portal]: https://manage.windowsazure.com/



<!--HONumber=sep16_HO1-->


