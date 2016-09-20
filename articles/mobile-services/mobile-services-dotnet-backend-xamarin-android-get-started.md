<properties
    pageTitle="Komma igång med Mobile Services för Xamarin Android-appar | Microsoft Azure"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för Xamarin Android-utveckling"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="donnam"/>

# <a name="getting-started"> </a>Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Xamarin.Android-app](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

I den här kursen får du veta hur du lägger till en molnbaserad serverdelstjänst för en Xamarin Android-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel _To do list_-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används .NET-språk som stöds med Visual Studio för affärslogik på serversidan och för att hantera mobiltjänsten. Om du vill skriva affärslogik på serversidan i JavaScript kan du läsa [JavaScript-serverdelsversion] i det här avsnittet.

>[AZURE.NOTE]I det här avsnittet beskrivs hur du skapar ett nytt mobiltjänstprojekt med den klassiska Azure-portalen. Med Visual Studio 2013 Update 2 kan du också lägga till ett nytt mobiltjänstprojekt i en befintlig Visual Studio-lösning. Mer information finns i [Snabbguide: Lägga till en mobiltjänst (.NET-serverdel)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

En skärmbild från den färdiga appen:

![][0]

Du måste slutföra den här kursen innan du kan göra de andra kurserna i Mobile Services för Xamarin iOS-appar.

>[AZURE.NOTE]Du behöver ett Azure-konto för att slutföra den här självstudien. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started).
>För kursen krävs att du har [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Det finns en kostnadsfri utvärderingsversion.

## Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Skapa en ny Xamarin Android-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska portalen om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet laddar du ned en ny Xamarin Android-app och ett tjänstprojekt för din mobiltjänst.

1. Installera Visual Studio med Xamarin, om du inte redan gjort det. Instruktioner finns i avsnittet om [konfiguration och installation](https://msdn.microsoft.com/library/mt613162.aspx) för Visual Studio och Xamarin. Du kan också använda Xamarin Studio på en Mac OS X-dator. Mer information finns i avsnittet [Konfiguration, installation och verifiering för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx).  

2. Gå till den [klassisk portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

3. Välj fliken quickstart, klicka på **Xamarin** under **Choose platform** (Välj plattform) och expandera **Create a new Xamarin app** (Skapa en ny Xamarin-app).

    ![][6]

    Därmed visas tre enkla steg för att skapa en Xamarin Android-app som är ansluten till din mobiltjänst.

    ![][7]

4. Under **Download and publish your service to the cloud** (Ladda ned och publicera tjänsten i molnet) väljer du **Android** och klickar på **Download** (Ladda ned).

    En fil med projekt för både mobiltjänsten och _To do list_-appen som är ansluten till mobiltjänsten laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

5. Ladda ned din publiceringsprofil, spara den nedladdade filen lokalt på datorn och notera var du sparar den.

## Testa mobiltjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicera mobiltjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Kör Xamarin Android-appen

Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Navigera till klientprojektet i mobiltjänstlösningen i Visual Studio eller Xamarin Studio.

2. Tryck på **Run** (Kör) för att skapa projektet och starta appen. Du blir ombedd att välja en emulator eller en ansluten USB-enhet.

    > [AZURE.NOTE] För att kunna köra projektet i Android-emulatorn måste du definiera minst en Android Virtual Device (AVD). Du kan skapa och hantera dessa enheter med AVD Manager.

3. Ange en beskrivande text i appen, till exempel _Slutföra kursen_, och klicka sedan på plustecknet (**+**).

    ![][10]

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.

    > [AZURE.NOTE]
    > Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data som finns i C#-filen ToDoActivity.cs.

## Nästa steg
Nu när du har slutfört snabbstartskursen kan du gå vidare och lära dig hur du utför fler viktiga uppgifter i Mobile Services:

* [Komma igång med datasynkronisering offline]
  <br/>Läs om hur datasynkronisering offline används i guiden för att ge appen kortare svarstid och göra den mer robust.

* [Komma igång med autentisering]
  <br/>Läs om hur du autentiserar användare i appen med en identitetsleverantör.

* [Komma igång med push-meddelanden]
  <br/>Läs om hur du skickar väldigt enkla push-meddelanden till appen.

* [Felsöka en Mobile Services .NET-serverdel]
  <br/> Läs om hur du diagnostiserar och åtgärdar problem som kan uppstå med Mobile Services .NET-serverdelen.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Nästa steg]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Komma igång med datasynkronisering offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Komma igång med autentisering]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Komma igång med push-meddelanden]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript och HTML]: mobile-services-win8-javascript/
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[klassisk portal]: https://manage.windowsazure.com/
[JavaScript-serverdelsversion]: mobile-services-android-get-started.md
[Felsöka en Mobile Services .NET-serverdel]: mobile-services-dotnet-backend-how-to-troubleshoot.md


<!--HONumber=sep16_HO1-->


