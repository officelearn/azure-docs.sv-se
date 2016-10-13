<properties
    pageTitle="Kom igång med Azure Apptjänst Mobile Apps för Xamarin.iOS-appar | Microsoft Azure"
    description="I den här kursen får du hjälp att komma igång med Mobile Apps för Xamarin.iOS-utveckling."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>



#Skapa en Xamarin.iOS-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.iOS-mobilapp med hjälp av en mobilappsserverdel i Azure.  Du skapar både en ny mobilappsserverdel och en enkel _Todo list_ Xamarin.iOS-app där appdata lagras hos Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Xamarin.iOS-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

##Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* En Mac med Xcode v7.0 eller senare och Xamarin Studio Community installerat. Se avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) och om [konfiguration, installation och verifieringar för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE] Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://tryappservice.azure.com/?appServiceName=mobile). Där kan du direkt skapa en tillfällig startmobilapp i Apptjänst. Inget kreditkort behövs och du gör inga åtaganden.

## Skapa en ny mobilappsserverdel i Azure

Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurera serverprojektet

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. I nästa steg får du ladda ned ett serverprojekt för en enkel todo-list-serverdel och publicera den på Azure.

Följ instruktionerna nedan för att ställa in om serverprojektet ska använda en Node.js- eller .NET-serverdel.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## Hämta och kör Xamarin.iOS-appen

1. Öppna [Azure-portalen] i ett webbläsarfönster.

2. På inställningsbladet för mobilappen klickar du på **Kom igång** > **Xamarin.iOS**. I steg 3 klickar du på **Skapa ny app** om det alternativet inte redan är förvalt.  Klicka sedan på **Hämta**.

    Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

3. Extrahera projektet som du laddade ned och öppna det i Xamarin Studio (eller Visual Studio).

    ![][9]

    ![][8]

4. Tryck på F5 för att skapa projektet och starta appen i iPhone-emulatorn.

5. Ange en beskrivande text i appen, till exempel  _Läs om Xamarin_, och klicka sedan på **+**.

    ![][10]

    En POST-begäran skickas till den nya mobilappserverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

>[AZURE.NOTE]Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen QSTodoService.cs.

##Nästa steg

* [Lägg till autentisering i appen ](app-service-mobile-xamarin-ios-get-started-users.md)
  <br/>Läs om hur du autentiserar användare i appen genom en identitetsleverantör.

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-ios-get-started-push.md)
  <br/>Läs om hur du skickar väldigt enkla push-meddelanden till appen.

<!-- Anchors. -->
[Komma igång med mobilappsserverdelar]:#getting-started
[Skapa en ny mobilappsserverdel]:#create-new-service
[Nästa steg]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com/



<!--HONumber=Oct16_HO1-->


