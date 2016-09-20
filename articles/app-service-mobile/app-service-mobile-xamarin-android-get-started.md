<properties
    pageTitle="Kom igång med Azure Mobile Apps för Xamarin.Android-appar"
    description="I den här kursen får du hjälp att komma igång med Azure Mobile Apps för Xamarin Android-utveckling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="glenga" />

#Skapa en Xamarin.Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Android-app. Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md).

En skärmbild från den färdiga appen:

![][0]

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobile Apps för Xamarin.Android-appar.

##Krav

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).  
 
>[AZURE.NOTE] Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto går du till [Prova Apptjänst](https://tryappservice.azure.com/?appServiceName=mobile). Där kan du direkt skapa en tillfällig startmobilapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.

## Skapa en ny mobilappsserverdel i Azure

Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. I nästa steg får du ladda ned ett serverprojekt för en enkel todo-list-serverdel och publicera den på Azure.

## Konfigurera serverprojektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Hämta och kör Xamarin.Android-appen

1. Under **Hämta och kör ditt Xamarin.Android-projekt** klickar du på **Hämta**.

    Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

2. Tryck på **F5** för att skapa projektet och starta appen.

3. Ange en beskrivande text i appen, till exempel _Slutför kursen_, och klicka sedan på **Lägg till**.

    ![][10]

    En POST-begäran skickas till den nya mobilappserverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

    > [AZURE.NOTE]  Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen ToDoActivity.cs.

##Nästa steg

* [Lägg till autentisering i appen ](app-service-mobile-xamarin-android-get-started-users.md)  
Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Lägg till push-meddelanden i Xamarin.Android-appen](app-service-mobile-xamarin-android-get-started-push.md)  
Läs om hur du lägger till push-meddelanden i appen.
* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
Läs om hur du arbetar med den hanterade klient-SDK:n i Xamarin-appen. 


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=sep16_HO1-->


