---
title: Skicka meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs | Microsoft Docs
description: I de här självstudierna kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Windows Universal-plattformsapp.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Självstudier: Skicka meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs | Microsoft Docs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I den här självstudiekursen kommer du att skapa en meddelandehubb för att skicka push-meddelanden till en Windows Universal Platform-app (UWP). Du skapar en tom Windows Store-app som tar emot push-meddelanden med hjälp av Windows Push Notification Service (WNS). Sedan kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

> [!NOTE]
> Du hittar den färdiga koden för den här självstudiekursen på [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en app i Windows Store
> * Skapa en meddelandehubb
> * Skapa en Windows-exempelapp
> * Skicka testmeddelanden


## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) eller senare.
- [Installerade verktyg för UWP-apputveckling](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Ett aktivt Windows Store-konto

Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för UWP-appar.

## <a name="create-an-app-in-windows-store"></a>Skapa en app i Windows Store
Om du vill skicka push-meddelanden till UWP-appar, associerar du din app med Windows Store. Konfigurera sedan meddelandehubben för att integrera den med WNS.

1. Navigera till [Windows Dev Center](https://dev.windows.com/overview), logga in med ditt Microsoft-konto och välj sedan **Skapa en ny app**.

    ![Knappen Ny app](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Ange ett namn på appen och markera sedan **Reservera produktnamn**. På så sätt skapas en ny Windows Store-registrering för din app.

    ![Lagra appnamnet](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Utöka **Apphantering**, välj först **WNS/MPNS**, sedan **WNS/MPNS** och därefter **Live Services-webbplats**. Logga in på ditt Microsoft-konto. **Programregistreringsportalen** öppnas på en ny flik. Du kan också navigera direkt till [programregistreringsportalen](http://apps.dev.microsoft.com) och gå till den här sidan genom att välja ditt programnamn.

    ![WNS MPNS-sida](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Anteckna lösenordet för **Application Secret** (programhemlighet) och **Package security identifier (SID)**.

        >[!WARNING]
        >Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>Konfigurera WNS-inställningar för hubben

1. Välj **Windows (WNS)** i kategorin **MEDDELANDEINSTÄLLNINGAR**. 
2. Ange de värden för **Package SID** och **Security Key** som du antecknade i föregående avsnitt. 
3. Välj **Spara** i verktygsfältet.

    ![Rutorna Paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Din meddelandehubb har nu konfigurerats för att fungera med WNS. Du har anslutningssträngarna för att registrera din app och skicka meddelanden.

## <a name="create-a-sample-windows-app"></a>Skapa en Windows-exempelapp
1. Välj **Arkiv** i Visual Studio, peka på **Nytt** och välj **Projekt**. 
2. Gör följande steg i dialogrutan **Nytt projekt**: 

    1. Utöka **Visual C#**.
    2. Välj **Windows Universal**. 
    3. Välj **Tom app (Universal Windows)**. 
    4. Ange ett **namn** för projektet. 
    5. Välj **OK**. 

        ![Dialogrutan Nytt projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. Acceptera standardinställningarna för **mål**- och **minimum**plattformsversioner och välj **OK**. 
2. I Solution Explorer högerklickar du på Windows Store-approjektet, väljer **Store** och väljer sedan **Associate App with the Store** (associera app med Store). Guiden **Associera din app med Windows Store** visas.
3. I guiden loggar du in med ditt Microsoft-konto.
4. Välj den app som du registrerade i steg 2, väljer **Nästa** och välj sedan **Associera**. På så sätt läggs den registreringsinformation som krävs för Windows Store till i programmanifestet.
5. Högerklicka på lösningen i Vision Studio och välj sedan **Hantera NuGet-paket**. Fönstret **Hantera NuGet-paket** öppnas.
6. I sökrutan anger du **WindowsAzure.Messaging.Managed**, väljer **Installera** och godkänner användningsvillkoren.
   
    ![Fönstret Hantera NuGet-paket][20]
   
    Den här åtgärden hämtar, installerar och lägger till en referens i Azure Notification Hubs-biblioteket för Windows med hjälp av [Microsoft.Azure.Notification Hubs-NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Öppna projektfilen App.xaml.cs och lägg till följande `using`-uttryck: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. I filen App.xaml.cs ska du även lägga till följande **InitNotificationsAsync**-metoddefinitionen i **App**-klassen:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Den här koden hämtar URI-kanalen för appen från WNS och registrerar sedan denna med din meddelandehubb.
   
    >[!NOTE]
    >* Ersätt platshållaren **hub name** (hubbnamn) med namnet på meddelandehubben som visas i Azure-portalen. 
    >* Byt även ut platshållaren för anslutningssträngen mot anslutningssträngen **DefaultListenSharedAccessSignature** som du fick från sidan **Åtkomstpolicyer** i din meddelandehubb i ett föregående avsnittet.
   > 
   > 
5. Längst upp i händelsehanteraren **OnLaunched** i App.xaml.cs lägger du till följande anrop till den nya **InitNotificationsAsync**-metoden:
   
        InitNotificationsAsync();
   
    Den här åtgärden garanterar även att URI-kanalen registreras i meddelandehubben varje gång appen startas.

6. Kör appen genom att trycka på **F5**-tangenten. En dialogruta som innehåller registreringsnyckeln visas. Stäng dialogrutan genom att välja **OK**. 

    ![Registreringen lyckades](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Appen är nu redo att ta emot popup-meddelanden.

## <a name="send-test-notifications"></a>Skicka testmeddelanden
Du kan snabbt testa att ta emot meddelanden i appen genom att skicka meddelanden från [Azure-portalen](https://portal.azure.com/). 

1. Växla till fliken Översikt i Azure Portal och välj **Testskicka** i verktygsfältet.     

    ![Knappen Testskicka](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Utför följande i fönstret **Testskicka**: 
    1. Välj **Windows** under **Plattformar**.
    2. Välj **Toast** under **Meddelandetyp**. 
    3. Välj **Skicka**. 
    
        ![Fönstret Skicka test](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Se resultatet av Send-åtgärden i listan **Resultat** längst ned i fönstret. Du kan också se ett varningsmeddelande. 

    ![Resultatet av Send-åtgärden](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. Aviseringsmeddelandet **Testmeddelande** visas på skrivbordet. 

    ![Aviseringsmeddelande](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen skickar du broadcast-meddelanden till alla dina Windows-enheter med hjälp av portalen eller ett konsolprogram. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie: 

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
