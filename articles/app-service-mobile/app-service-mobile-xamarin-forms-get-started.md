---
title: "Kom igång med Mobile Apps med hjälp av Xamarin.Forms"
description: "I den här kursen får du hjälp att komma igång med Mobile Apps för Xamarin.Forms-utveckling"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 12c7eb78b5049b385ee34c7ac8e3574b064d7ecf
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-xamarinforms-app"></a>Skapa en Xamarin.Forms-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Forms-mobilapp med hjälp av funktionen Mobile Apps i Azure App Service som serverdel. Du skapar både en ny Mobile Apps-serverdel och en enkel ”todo list”-app med Xamarin.Forms där appdata lagras i Azure.

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobilappar för Xamarin.Forms-appar.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Mer information finns på sidan för [installation och konfiguration av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* En Mac med Xcode v7.0 eller senare och Xamarin Studio Community installerat. Information finns på sidan för [installation och konfiguration av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) och på sidan för [konfiguration, installation och verifiering för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Skapa en ny Mobile Apps-serverdel

Så här skapar du en ny Mobile Apps-serverdel:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Nu har du konfigurerat en Mobile Apps-serverdel som du kan använda för dina mobilklientprogram. I nästa steg får du ladda ned ett serverprojekt för en enkel ”todo list”-serverdel och sedan publicera den på Azure.

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet

Så här konfigurerar du serverprojektet för att använda antingen Node.js- eller .NET-serverdelen:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Hämta och kör Xamarin.Forms-lösningen

Du kan ladda ned lösningen på två sätt. Du kan ladda ned lösningen till en Mac och öppna den i Xamarin Studio eller så kan du ladda ned den till en dator med Windows och öppna den i Visual Studio genom en nätverksansluten Mac och sedan bygga iOS-appen där. Mer information finns på sidan för att [installera och konfigurera Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

Gör följande på en Mac- eller Windows-dator:

1. Gå till [Azure-portalen].

2. På inställningsbladet för mobilappen klickar du på **Snabbstart** (under Distribution) > **Xamarin.Forms**. I steg 3 klickar du på **Skapa ny app** om det alternativet inte redan är förvalt.  Klicka sedan på **Hämta**.

   Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

3. Extrahera projektet som du laddade ned och öppna det i Xamarin Studio (Mac) eller Visual Studio (Windows).

   ![Extraherat projekt i Xamarin Studio][9]

   ![Extraherat projekt i Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Valfritt) Kör iOS-projektet
I det här avsnittet kör du Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

#### <a name="in-xamarin-studio"></a>I Xamarin Studio
1. Högerklicka på iOS-projektet och klicka sedan på **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Run** (Kör) och välj **Start Debugging** (Starta felsökning) för att bygga projektet och starta appen i iPhone-emulatorn.

#### <a name="in-visual-studio"></a>I Visual Studio 2013
1. Högerklicka på iOS-projektet och välj sedan **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid iOS-projektet.

4. Välj **F5** för att skapa projektet och starta appen i iPhone-emulatorn.

   > [!NOTE]
   > Om du får problem med att skapa projektet kan du köra NuGet Package Manager och uppdatera till den senaste versionen av stödpaketen till Xamarin. Snabbstartsprojekt kan vara långsamma att uppdatera till de senaste versionerna.    
   >
   >

5. Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

    ![][10]

    En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.

    > [!NOTE]
    > Du hittar koden som ansluter till Mobile Apps-serverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.
    >
    >

## <a name="optional-run-the-android-project"></a>(Valfritt) Kör Androidprojektet
I det här avsnittet kör du Xamarin droid-projektet för Android. Du kan hoppa över det här avsnittet om du inte arbetar med Androidenheter.

#### <a name="in-xamarin-studio"></a>I Xamarin Studio

1. Högerklicka på Android-projektet och välj sedan **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Run** (Kör) och välj **Start Debugging** (Starta felsökning) för att skapa projektet och starta appen i en Android-emulator.

#### <a name="in-visual-studio"></a>I Visual Studio 2013

1. Högerklicka på Android-projektet (Droid) och välj sedan **Set as StartUp Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid Android-projektet.

4. Välj **F5** för att skapa projektet och starta appen i en Android-emulator.

   > [!NOTE]
   > Om du får problem med att skapa projektet kan du köra NuGet Package Manager och uppdatera till den senaste versionen av stödpaketen till Xamarin. Snabbstartsprojekt kan vara långsamma att uppdatera till de senaste versionerna.    
   >
   >

5. Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

    ![][11]
    
    En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.
    
    > [!NOTE]
    > Du hittar koden som ansluter till Mobile Apps-serverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Valfritt) Kör Windowsprojektet

I det här avsnittet kör du Xamarin WinApp-projektet för Windowsenheter. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

#### <a name="in-visual-studio"></a>I Visual Studio 2013

1. Högerklicka på något av Windows-projekten och välj sedan **Set as StartUp Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid Windows-projektet du valde.

4. Välj **F5** för att skapa projektet och starta appen i en Windows-emulator.

   > [!NOTE]
   > Om du får problem med att skapa projektet kan du köra NuGet Package Manager och uppdatera till den senaste versionen av stödpaketen till Xamarin. Snabbstartsprojekt kan vara långsamma att uppdatera till de senaste versionerna.    
   >
   >

5. Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

    En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.
    
    ![][12]
    
    > [!NOTE]
    > Du hittar koden som ansluter till Mobile Apps-serverdelen i C#-filen TodoItemManager.cs i det portabla klassbiblioteksprojektet i lösningen.
    >
    >

## <a name="next-steps"></a>Nästa steg

* [Lägg till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)  
  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar Mobile Apps-serverdelen så att Azure Notification Hubs används för att skicka push-meddelanden.

* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en Mobile Apps-serverdel. Med offlinesynkronisering kan du visa, lägga till eller ändra mobilappens data, även när det inte finns någon nätverksanslutning.

* [Använda den hanterade klienten för Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Läs om hur du arbetar med den hanterade klient-SDK:n i Xamarin-appen.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-portalen]: https://portal.azure.com/
