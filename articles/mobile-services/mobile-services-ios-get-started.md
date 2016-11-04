---
title: Komma igång med Azure Mobile Services för iOS-appar | Microsoft Docs
description: Den här kursen hjälper dig att komma igång med Azure Mobile Services för iOS-utveckling
services: mobile-services
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: krisragh

---
# <a name="getting-started"> </a>Komma igång med Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Motsvarande avsnitt för Mobile Apps finns i [Create an iOS app in Azure Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md) (Skapa en iOS-app i Azure Mobile Apps).
> 
> 

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en iOS-app med Azure Mobile Services.

I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används JavaScript för affärslogik på serversidan. Information om hur du skapar en mobiltjänst med affärslogik på serversidan i .NET finns under [.NET-serverdelsversion] i det här avsnittet.

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och [få mobiltjänster utan kostnad som du kan fortsätta använda även efter utvärderingsperiodens slut](https://azure.microsoft.com/pricing/details/mobile-services/). Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).
> 
> 

## <a name="create-new-service"> </a>Skapa en ny mobiltjänst
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny iOS-app
Du kan enkelt skapa en ny app som är ansluten till mobiltjänsten med hjälp av en snabbstartsguide i den klassiska Azure-portalen.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.
2. Välj snabbstartsfliken, klicka på **iOS** under **Choose platform** (Välj plattform) och expandera **Create a new iOS app** (Skapa en ny iOS-app). Därmed visas anvisningar för att skapa en iOS-app som är ansluten till din mobiltjänst.
3. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.
4. Under **Download and run your app** (Ladda ned och kör appen) klickar du på **Download** (Ladda ned). Projektet för *To do list*-appen som är ansluten till mobiltjänsten laddas ned tillsammans med Mobile Services iOS SDK. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Köra den nya iOS-appen
[!INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4">

<li><p>Gå till den [Klassisk Azure-portal], välj fliken **DATA** och klicka sedan på tabellen **TodoItem**. Nu kan du bläddra bland de data som infogats i tabellen via appen.<p></li></ol></p>

## <a name="next-steps"> </a>Nästa steg
Läs om hur du utför fler viktiga uppgifter i Mobile Services:

* [Komma igång med datasynkronisering offline]
    <br/>Läs om hur du kan använda datasynkronisering offline för att ge appen kortare svarstid och göra den mer robust. 
* [Lägga till autentisering i en befintlig app]
    <br/>Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Lägga till push-meddelanden för en befintlig app]
    <br/>Läs om hur du skickar grundläggande push-meddelanden till appen.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Komma igång med datasynkronisering offline]: mobile-services-ios-get-started-offline-data.md
[Lägga till autentisering i en befintlig app]: mobile-services-dotnet-backend-ios-get-started-users.md
[Lägga till push-meddelanden för en befintlig app]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET-serverdelsversion]: mobile-services-dotnet-backend-ios-get-started.md



<!--HONumber=sep16_HO1-->


