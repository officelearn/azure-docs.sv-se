<properties
    pageTitle="Komma igång med Mobile Services för Xamarin.Android | Microsoft Azure"
    writer="craigd"
    description="Läs om hur du använder Azure Mobile Services med din Xamarin.Android app-app."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/10/2016"
    ms.author="donnam"/>

# <a name="getting-started"></a>Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Xamarin.Android-app](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en Xamarin.Android-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten.

Om du vill kan du titta på videoklippet i stället. Det visar samma steg som kursen.

Video: "Getting Started with Xamarin and Azure Mobile Services" med Craig Dunn, developer evangelist för Xamarin  (varaktighet: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

En skärmbild från den färdiga appen:

![][0]

Den här kursen kräver XCode och Xamarin Studio för OS X eller Visual Studio i Windows med en nätverksansluten Mac. Fullständiga instruktioner finns i avsnittet om [konfiguration och installation](https://msdn.microsoft.com/library/mt613162.aspx) för Visual Studio och Xamarin. 

Det nedladdade exempelprojektet innehåller Azure Mobile Services-komponenten för Xamarin.Android. Målversionen för det här projektet är Android 4.2 eller senare, men för Mobile Services SDK krävs bara Android 2.2 eller senare.

> [AZURE.IMPORTANT] Du behöver ett Azure-konto för att genomföra kursen. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny Xamarin.Android-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet skapar du en ny Xamarin.Android-app som är ansluten till din mobiltjänst.

1.  Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

2. Välj snabbstartsfliken, klicka på **Xamarin.Android** under **Choose platform** (Välj plattform) och expandera **Create a new Android app** (Skapa ny Android-app).

    ![][6]

    Därmed visas tre enkla steg för att skapa en Xamarin.Android-app som är ansluten till din mobiltjänst.

    ![][7]

3. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.

4. Klicka på **Download** (Ladda ned) under **Download and run app** (Ladda ned och kör app).

    Projektet för _To do list_-appen som är ansluten till din mobiltjänst laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Köra Android-appen

Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna och expandera filerna på datorn.

2. Klicka på **File** (Fil) i Xamarin Studio eller Visual Studio och sedan på **Open** (Öppna), navigera till de okomprimerade exempelfilerna och välj **XamarinTodoQuickStart.Android.sln** för att öppna filen.

3. Tryck på **Run** (Kör) för att skapa projektet och starta appen. Du blir ombedd att välja en emulator eller en ansluten USB-enhet.

    > [AZURE.NOTE] För att kunna köra projektet i Android-emulatorn måste du definiera minst en Android Virtual Device (AVD). Du kan skapa och hantera dessa enheter med AVD Manager.

4. Ange en beskrivande text i appen, till exempel om att _slutföra kursen_, och klicka sedan på **Add** (Lägg till).

    ![][10]

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.

    > [AZURE.NOTE] Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i C#-filen ToDoActivity.cs.

6. Gå till den [Klassisk Azure-portal], välj fliken **Data** och klicka sedan på tabellen **TodoItems**.

    ![][11]

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![][12]

## <a name="next-steps"> </a>Nästa steg
Nu när du har slutfört guiden kan du gå vidare och lära dig fler viktiga uppgifter i Mobile Services:

* [Komma igång med datasynkronisering offline] Läs om hur du kan använda datasynkronisering offline för att ge appen kortare svarstid och göra den mer robust. 

* [Komma igång med autentisering] Läs om hur du autentiserar användare i appen med en identitetsprovider.

* [Komma igång med push-meddelanden] Läs om hur du skickar grundläggande push-meddelanden till appen.



[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Komma igång med data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Komma igång med datasynkronisering offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Komma igång med autentisering]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Komma igång med push-meddelanden]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Klassisk Azure-portal]: https://manage.windowsazure.com/




<!--HONumber=Jun16_HO2-->


