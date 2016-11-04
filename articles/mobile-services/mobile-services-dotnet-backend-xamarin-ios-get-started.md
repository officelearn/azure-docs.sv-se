---
title: Komma igång med Mobile Services för Xamarin iOS-appar | Microsoft Docs
description: Den här kursen hjälper dig att komma igång med Azure Mobile Services för Xamarin iOS-utveckling
services: mobile-services
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: mollybos

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"> </a>Komma igång med Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Xamarin.iOS-app](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).
> 
> 

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en Xamarin iOS-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används .NET-språk som stöds med Visual Studio för affärslogik på serversidan och för att hantera mobiltjänsten. Om du vill skriva affärslogik på serversidan i JavaScript kan du läsa [JavaScript-serverdelsversion] i det här avsnittet.

> [!NOTE]
> I det här avsnittet beskrivs hur du skapar ett nytt mobiltjänstprojekt med den klassiska Azure-portalen. Med Visual Studio 2013 Update 2 kan du också lägga till ett nytt mobiltjänstprojekt i en befintlig Visual Studio-lösning. Mer information finns i [Snabbguide: Lägga till en mobiltjänst (.NET-serverdel)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)
> 
> 

En skärmbild från den färdiga appen:

![][0]

Att slutföra den här kursen är en förutsättning för alla andra kurser i Mobile Services för Xamarin iOS-appar.

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Kostnadsfri utvärderingsversion av Azure</a>.<br />För kursen krävs att du har <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Det finns en kostnadsfri utvärderingsversion.
> 
> 

## Skapa en ny mobiltjänst
[!INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Skapa en ny Xamarin iOS-app
När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet laddar du ned en ny Xamarin iOS-app och ett tjänstprojekt för din mobiltjänst.

1. Installera Visual Studio med Xamarin, om du inte redan gjort det. Instruktioner finns i avsnittet om [konfiguration och installation](https://msdn.microsoft.com/library/mt613162.aspx) för Visual Studio och Xamarin. Du kan också använda Xamarin Studio på en Mac OS X-dator. Mer information finns i avsnittet om [konfiguration, installation och verifieringar för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx).
2. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.
3. Välj fliken quickstart, klicka på **Xamarin** under **Choose platform (Välj plattform)** och expandera **Create a new Xamarin app (Skapa en ny Xamarin-app)**.
   
    ![][6]
   
    Därmed visas tre enkla steg för att skapa en Xamarin iOS-app som är ansluten till din mobiltjänst.
   
    ![][7]
4. Välj **iOS** under **Download and publish your service to the cloud (Ladda ned och publicera tjänsten i molnet)** och klicka på **Download (Ladda ned)**.
   
    En fil med projekt för både mobiltjänsten och *To do list*-appen som är ansluten till mobiltjänsten laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.
5. Ladda ned din publiceringsprofil, spara den nedladdade filen lokalt på datorn och notera var du sparar den.

## Testa mobiltjänsten
[!INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicera mobiltjänsten
[!INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Kör Xamarin iOS-appen
Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Navigera till klientprojektet i mobiltjänstlösningen i Visual Studio eller Xamarin Studio.
   
    ![][8]
   
    ![][9]
2. Tryck på **Run** för att skapa klientprojektet och starta appen i iPhone-emulatorn.
3. Ange en beskrivande text i appen, till exempel *Slutföra kursen*, och klicka sedan på plustecknet (**+**).
   
    ![][10]
   
    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.

> [!NOTE]
> Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i C#-filen QSTodoService.cs.
> 
> 

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

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Nästa steg]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Komma igång med datasynkronisering offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Komma igång med autentisering]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Komma igång med push-meddelanden]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript och HTML]: mobile-services-win8-javascript/
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[JavaScript-serverdelsversion]: mobile-services-ios-get-started.md
[Felsöka en Mobile Services .NET-serverdel]: mobile-services-dotnet-backend-how-to-troubleshoot.md


<!--HONumber=sep16_HO1-->


