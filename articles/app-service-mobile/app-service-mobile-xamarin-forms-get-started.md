<properties
    pageTitle="Kom igång med Mobilappar med hjälp av Xamarin.Forms"
    description="I den här kursen får du hjälp att komma igång med Azure Mobilappar för Xamarin.Forms-utveckling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#Skapa en Xamarin.Forms-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Forms-mobilapp med hjälp av en mobilappsserverdel i Azure. Du skapar både en ny mobilappsserverdel och en enkel _Todo list_ Xamarin.Forms-app där appdata lagras i Azure.

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobilappar för Xamarin.Forms-appar.

##Krav

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

* En Mac med Xcode v7.0 eller senare och Xamarin Studio Community installerat. Se avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) och om [konfiguration, installation och verifieringar för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://tryappservice.azure.com/?appServiceName=mobile). Där kan du direkt skapa en tillfällig startmobilapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.

## Skapa en ny mobilappsserverdel i Azure

Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. I nästa steg får du ladda ned ett serverprojekt för en enkel todo-list-serverdel och publicera den på Azure.

## Konfigurera serverprojektet

Följ instruktionerna nedan för att ställa in om serverprojektet ska använda en Node.js- eller .NET-serverdel.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##Hämta och kör Xamarin.Forms-lösningen

Här har du ett antal olika alternativ. Du kan ladda ned lösningen till en Mac och öppna den i Xamarin Studio eller så kan du ladda ned lösningen till en dator med Windows och öppna den i Visual Studio genom en nätverksansluten Mac och sedan bygga iOS-appen där. I avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) finns utförligare instruktioner för konfiguration av Xamarin.

Då går vi vidare:

 1. Öppna [Azure Portal] i ett webbläsarfönster på Mac- eller Windowsdatorn.
 2. På inställningsbladet för mobilappen klickar du på **Kom igång** (under Mobilt) > **Xamarin.Forms**. I steg 3 klickar du på **Skapa ny app** om det alternativet inte redan är förvalt.  Klicka sedan på **Hämta**.

    Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

 3. Extrahera projektet som du laddade ned och öppna det i Xamarin Studio eller Visual Studio.

    ![][9]

    ![][8]


##(Valfritt) Kör iOS-projektet

Det här avsnittet handlar om att köra Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

####I Xamarin Studio

1. Högerklicka på iOS-projektet, och klicka sedan på **Set As Startup Project**.
2. Gå till menyn **Run** och klicka på **Start Debugging** för att bygga projektet och starta appen i iPhone-emulatorn.

####I Visual Studio 2013
1. Högerklicka på iOS-projektet, och klicka sedan på **Set as StartUp Project**.
2. Gå till menyn **Build** och klicka på **Configuration Manager**.
3. I dialogrutan **Configuration Manager** markerar du kryssrutorna **Build** och **Deploy** för iOS-projektet.
4. Tryck på **F5** för att skapa projektet och starta appen i iPhone-emulatorn.

    >[AZURE.NOTE] Om du får problem i bygget kör du NuGet Package Manager och uppdaterar till den senaste versionen av stödpaketen till Xamarin. Ibland dröjer det innan snabbstartsprojekt uppdateras till den senaste versionen.    

Ange en beskrivande text i appen, till exempel  _Läs om Xamarin_, och klicka sedan på **+**.

![][10]

En POST-begäran skickas till den nya mobilappserverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

>[AZURE.NOTE]
> Du hittar koden som ansluter till mobilappsserverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.

##(Valfritt) Kör Androidprojektet

Det här avsnittet handlar om att köra Xamarin droid-projektet för Android. Du kan hoppa över det här avsnittet om du inte arbetar med Androidenheter.

####I Xamarin Studio

1. Högerklicka på Androidprojektet, och klicka sedan på **Set As Startup Project**.
2. Gå till menyn **Run** och klicka på **Start Debugging** för att bygga projektet och starta appen i en Androidemulator.

####I Visual Studio 2013
1. Högerklicka på Androidprojektet (Droid), och klicka sedan på **Set as StartUp Project**.
4. Gå till menyn **Build** och klicka på **Configuration Manager**.
5. I dialogrutan **Configuration Manager** markerar du kryssrutorna **Build** och **Deploy** för Androidprojektet.
6. Tryck på **F5** för att skapa projektet och starta appen i en Androidemulator.

    >[AZURE.NOTE] Om du får problem i bygget kör du NuGet Package Manager och uppdaterar till den senaste versionen av stödpaketen till Xamarin. Ibland dröjer det innan snabbstartsprojekt uppdateras till den senaste versionen.    


Ange en beskrivande text i appen, till exempel  _Läs om Xamarin_, och klicka sedan på **+**.

![][11]

En POST-begäran skickas till den nya mobilappserverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

> [AZURE.NOTE]
> Du hittar koden som ansluter till mobilappsserverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.


##(Valfritt) Kör Windowsprojektet


Det här avsnittet handlar om att köra Xamarin WinApp-projektet för Windowsenheter. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.


####I Visual Studio 2013
1. Högerklicka på något av Windowsprojekten, och klicka sedan på **Set as StartUp Project**.
4. Gå till menyn **Build** och klicka på **Configuration Manager**.
5. I dialogrutan **Configuration Manager** markerar du kryssrutorna **Build** och **Deploy** för Windowsprojektet som du valde.
6. Tryck på **F5** för att skapa projektet och starta appen i en Windowsemulator.

    >[AZURE.NOTE] Om du får problem i bygget kör du NuGet Package Manager och uppdaterar till den senaste versionen av stödpaketen till Xamarin. Ibland dröjer det innan snabbstartsprojekt uppdateras till den senaste versionen.    


Ange en beskrivande text i appen, till exempel  _Läs om Xamarin_, och klicka sedan på **+**.

En POST-begäran skickas till den nya mobilappserverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

![][12]

> [AZURE.NOTE]
> Du hittar koden som ansluter till mobilappsserverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.

##Nästa steg

* [Lägg till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md)  
Läs om hur du autentiserar användare i appen med en identitetsleverantör.

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)  
Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.

* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
Läs om hur du arbetar med den hanterade klient-SDK:n i Xamarin-appen. 


<!-- Anchors. -->
[Kom igång med mobilappsserverdelar]:#getting-started
[Skapa en ny mobilappsserverdel]:#create-new-service
[Nästa steg]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobilapp-SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/




<!--HONumber=Oct16_HO1-->


