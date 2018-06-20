---
title: Kom igång med Mobile Apps med hjälp av Xamarin.Forms
description: I den här kursen får du hjälp att komma igång med Mobile Apps för Xamarin.Forms-utveckling
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/25/2018
ms.author: crdun
ms.openlocfilehash: b2dafbcf0e41e7387157590e145f74430686321c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597679"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Skapa en Xamarin.Forms-app med Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Forms-mobilapp med hjälp av funktionen Mobile Apps i Azure App Service som serverdel. Du skapar både en ny Mobile Apps-serverdel och en enkel ”todo list”-app med Xamarin.Forms där appdata lagras i Azure.

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobilappar för Xamarin.Forms-appar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio Tools för Xamarin i Visual Studio 2017 eller Visual Studio för Mac. Anvisningar finns på [installationssidan för Xamarin][Install Xamarin].

* (valfritt) En Mac med Xcode 9.0 eller senare krävs för att skapa en iOS-app. Visual Studio för Mac kan användas för att utveckla iOS-appar: Du kan även använda Visual Studio 2017 (så länge Mac-enheten är tillgänglig i nätverket).

## <a name="create-a-new-mobile-apps-back-end"></a>Skapa en ny Mobile Apps-serverdel

Så här skapar du en ny Mobile Apps-serverdel:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Nu har du konfigurerat en Mobile Apps-serverdel som dina mobilklientprogram kan använda. I nästa steg får du ladda ned ett serverprojekt för en enkel ”todo list”-serverdel och sedan publicera den på Azure.

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet

Så här konfigurerar du serverprojektet för att använda antingen Node.js- eller .NET-serverdelen:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Hämta och kör Xamarin.Forms-lösningen

Visual Studio Tools för Xamarin behövs för att öppna lösningen. Läs mer i [installationsinstruktionerna för Xamarin][Install Xamarin]. Om verktygen redan är installerade följer du dessa anvisningar för att hämta och öppna lösningen:

### <a name="visual-studio"></a>Visual Studio

1. Gå till [Azure Portal].

2. På inställningsbladet för mobilappen klickar du på **Snabbstart** (under Distribution) > **Xamarin.Forms**. I steg 3 klickar du på **Skapa ny app** om det alternativet inte redan är förvalt.  Klicka sedan på **Hämta**.

   Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

3. Extrahera projektet som du laddade ned och öppna det i Visual Studio 2017.

   ![Extraherat projekt i Visual Studio][8]

4. Följ anvisningarna nedan för att köra Android- eller Windows-projekt, och, om en nätverksansluten Mac-dator finns tillgänglig, även iOS-projektet.

### <a name="visual-studio-for-mac"></a>Visual Studio för Mac

1. Gå till [Azure Portal].

2. På inställningsbladet för mobilappen klickar du på **Snabbstart** (under Distribution) > **Xamarin.Forms**. I steg 3 klickar du på **Skapa ny app** om det alternativet inte redan är förvalt.  Klicka sedan på **Hämta**.

   Ett projekt laddas ned med ett klientprogram som är kopplat till din mobilapp. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

3. Extrahera projektet som du laddade ned och öppna det i Visual Studio för Mac.

   ![Extraherat projekt i Visual Studio för Mac][9]

4. Följ anvisningarna nedan för att köra Android- eller iOS-projekten.



## <a name="optional-run-the-android-project"></a>(Valfritt) Kör Androidprojektet

I det här avsnittet kör du Xamarin.Android-projektet. Du kan hoppa över det här avsnittet om du inte arbetar med Androidenheter.

### <a name="visual-studio"></a>Visual Studio

1. Högerklicka på Android-projektet (Droid) och välj sedan **Set as StartUp Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid Android-projektet. Kontrollera att kryssrutan **Version** är markerad för det delade kodprojektet.

4. För att skapa projektet och starta appen i en Android-emulator trycker du på **F5** eller klickar på **Start**.

### <a name="visual-studio-for-mac"></a>Visual Studio för Mac

1. Högerklicka på Android-projektet och välj sedan **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Run** (Kör) och välj **Start Debugging** (Starta felsökning) för att skapa projektet och starta appen i en Android-emulator.



Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

![Att göra-app för Android][11]

En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.

> [!NOTE]
> Koden som ansluter till Mobile Apps-serverdelen finns i C#-filen **TodoItemManager.cs** i det delade kodprojektet i lösningen.
>

## <a name="optional-run-the-ios-project"></a>(Valfritt) Kör iOS-projektet

I det här avsnittet kör du Xamarin.iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

### <a name="visual-studio"></a>Visual Studio

1. Högerklicka på iOS-projektet och välj sedan **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid iOS-projektet. Kontrollera att kryssrutan **Version** är markerad för det delade kodprojektet.

4. Välj **F5** för att skapa projektet och starta appen i iPhone-emulatorn.

### <a name="visual-studio-for-mac"></a>Visual Studio för Mac

1. Högerklicka på iOS-projektet och klicka sedan på **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Run** (Kör) och välj **Start Debugging** (Starta felsökning) för att bygga projektet och starta appen i iPhone-emulatorn.



Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

![Att göra-app för iOS][10]

En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.

> [!NOTE]
> Du hittar koden som ansluter till Mobile Apps-serverdelen i C#-filen **TodoItemManager.cs** i det delade kodprojektet i lösningen.
>

## <a name="optional-run-the-windows-project"></a>(Valfritt) Kör Windowsprojektet

I det här avsnittet kan du köra Xamarin.Forms UWP-projekt (Universell Windows-plattform) för Windows-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

### <a name="visual-studio"></a>Visual Studio

1. Högerklicka på ett iOS-projekt och välj **Set As Startup Project** (Ange som startprojekt).

2. Gå till menyn **Build** (Skapa) och välj **Configuration Manager** (Konfigurationshanteraren).

3. I dialogrutan **Configuration Manager** (Konfigurationshanteraren) markerar du kryssrutorna **Build** (Skapa) och **Deploy** (Distribuera) bredvid Windows-projektet du väljer. Kontrollera att kryssrutan **Version** är markerad för det delade kodprojektet.

4. För att skapa projektet och starta appen i en Windows-emulator trycker du på **F5** eller klickar på knappen **Start** (som bör läsa den **lokala datorn**).

> [!NOTE]
> Windows-projektet kan inte köras på macOS.



Ange en beskrivande text i appen, till exempel *Läs om Xamarin* och välj sedan plustecknet (**+**).

En post-begäran skickas till den nya Mobile Apps-serverdelen som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av Mobile Apps-serverdelen och data visas i listan.

![Att göra-app för UWP][12]

> [!NOTE]
> Du hittar koden som ansluter till Mobile Apps-serverdelen i C#-filen **TodoItemManager.cs** i det portabla klassbiblioteksprojektet i lösningen.
>

## <a name="troubleshooting"></a>Felsökning

Om du får problem med att skapa lösningen kan du köra pakethanteraren för NuGet och uppdatera till den senaste versionen av Xamarin.Forms. I Android-projektet uppdaterar du stödpaketen för Xamarin.Android. Snabbstartsprojekt innehåller inte alltid de senaste versionerna.

## <a name="next-steps"></a>Nästa steg

* [Lägga till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md) Läs om hur du autentiserar användare i din app med en identitetsprovider.

* [Lägga till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md) Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar Mobile Apps-serverdelen så att Azure Notification Hubs används för att skicka push-meddelanden.

* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md) Läs om hur du lägger till offlinestöd i appen genom att använda en Mobile Apps-serverdel. Med offlinesynkronisering kan du visa, lägga till eller ändra mobilappens data, även när det inte finns någon nätverksanslutning.

* [Använda den hanterade klienten för Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) Läs om hur du arbetar med den hanterade klient-SDK:n i Xamarin-appen.

* [Använda andra Azure-tjänster med Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Lägg till ytterligare Azurew-funktioner som sök-, lagrings- och kognitiva tjänster till Xamarin.Forms-appar.

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
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
