<properties
    pageTitle="Komma igång med Mobile Services för Xamarin iOS-appar | Microsoft Azure"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för Xamarin iOS-utveckling."
    services="mobile-services"
    documentationCenter="xamarin"
    authors="conceptdev"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/16/2016"
    ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Komma igång med Mobile Services
[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Xamarin.iOS-app](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en Xamarin.iOS-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten.

Om du vill kan du titta på videoklippet i stället. Det visar samma steg som kursen.

Video: "Getting Started with Xamarin and Azure Mobile Services" med Craig Dunn, developer evangelist för Xamarin  (varaktighet: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

En skärmbild från den färdiga appen:

![][0]

Den här kursen kräver XCode och Xamarin Studio för OS X eller Visual Studio i Windows med en nätverksansluten Mac. Fullständiga instruktioner finns i avsnittet om [konfiguration och installation](https://msdn.microsoft.com/library/mt613162.aspx) för Visual Studio och Xamarin. 

> [AZURE.IMPORTANT] Du behöver ett Azure-konto för att genomföra kursen. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny Xamarin.iOS-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet skapar du en ny Xamarin.iOS-app som är ansluten till din mobiltjänst.

1.  Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

2. Välj snabbstartsfliken, klicka på **Xamarin.iOS** under **Choose platform** (Välj plattform) och expandera **Create a new Xamarin.iOS app** (Skapa en ny Xamarin.iOS-app).

    ![][6]

    Därmed visas tre enkla steg för att skapa en Xamarin.iOS-app som är ansluten till din mobiltjänst.

    ![][7]

3. Ladda ned och installera Xcode (vi rekommenderar att du använder den senaste versionen, d.v.s. Xcode 6.0 eller senare) och [Xamarin Studio], om du inte redan gjort det.

4. Klicka på **Create TodoItems table** (Skapa TodoItems-tabell) för att skapa en tabell för lagring av appdata.

5. Klicka på **Download** (Ladda ned) under **Download and run app** (Ladda ned och kör app).

    Därmed hämtas projektet för _To do list_-appen som är ansluten till din mobiltjänst och refererar till Azure Mobile Services-komponenten för Xamarin.iOS. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Köra den nya Xamarin.iOS-appen

Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna, expandera filerna på datorn och öppna lösningsfilen **XamarinTodoQuickStart.iOS.sln** i Xamarin Studio eller Visual Studio.

    ![][8]

    ![][9]

2. Tryck på **Kör** för att skapa klientprojektet och starta appen i iPhone-emulatorn (vilket är standard för det här projektet).

3. Ange en beskrivande text i appen, till exempel _Slutföra kursen_, och klicka sedan på plustecknet (**+**).

    ![][10]

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.

    > [AZURE.NOTE] Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i C#-filen TodoService.cs.

4. Gå till den [Klassisk Azure-portal], välj fliken **Data** och klicka sedan på tabellen **TodoItems**.

    ![][11]

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![][12]


## Nästa steg
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
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Komma igång med datasynkronisering offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Komma igång med autentisering]: partner-xamarin-mobile-services-ios-get-started-users.md
[Komma igång med push-meddelanden]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Klassisk Azure-portal]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


