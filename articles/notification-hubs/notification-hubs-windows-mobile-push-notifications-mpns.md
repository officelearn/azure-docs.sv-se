---
title: Skicka meddelanden till Chrome-appar med Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Silverlight-app på Windows Phone 8 eller Windows Phone 8.1.
services: notification-hubs
documentationcenter: windows
keywords: push-meddelande, pushmeddelande, push för windows phone
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 40d962e2724b36f97923f10a8a462848b137873b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452487"
---
# <a name="tutorial-push-notifications-to-windows-phone-apps-by-using-azure-notification-hubs"></a>Självstudier: Skicka push-meddelanden till Windows Phone-appar med hjälp av Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Silverlight-app på Windows Phone 8 eller Windows Phone 8.1. Om du vill skicka meddelanden till Windows Phone 8.1 (utan Silverlight) så gå till [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)-versionen av den här självstudiekursen.

I denna självstudiekurs skapar du en tom Windows Phone 8-app som tar emot push-meddelanden med hjälp av Microsoft Push Notification Service (MPNS). När du har skapat appen använder du meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

> [!NOTE]
> SDK:erna för Windows Phone på Notification Hubs stöder inte användning av Windows Push Notification Service (WNS) med Silverlight-appar för Windows Phone 8.1. Om du vill använda WNS (istället för MPNS) med Silverlight-appar för Windows Phone 8.1, ska du följa anvisningarna i [Notification Hubs – självstudiekurs för Windows Phone Silverlight]. Där används istället REST-API:er.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en meddelandehubb
> * Skapa ett Windows Phone-program
> * Testskicka ett meddelande

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 Express med mobila utvecklingskomponenter](https://www.visualstudio.com/vs/older-downloads/)

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Windows Phone 8-appar.

## <a name="create-your-notification-hub"></a>Skapa din meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Konfigurera inställningar för Windows Phone (MPNS)

1. Välj **Windows Phone (MPNS)** under **MEDDELANDEINSTÄLLNINGAR**.
2. Välj **Aktivera autentiseringspush**.
3. Välj **Spara** i verktygsfältet.

    ![Azure Portal – Aktivera ej autentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    Hubben har nu skapats och konfigurerats för att skicka ej autentiserade meddelanden till Windows Phone.

    > [!NOTE]
    > I den här kursen används MPNS i ej autentiserat läge. MPNS i ej autentiserat läge har begränsningar för vilka meddelanden du kan skicka till varje kanal. Notification Hubs stöder [MPNS i autentiserat läge](https://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) genom att låta dig överföra ditt certifikat.

## <a name="create-a-windows-phone-application"></a>Skapa ett Windows Phone-program

I det här avsnittet skapar du ett Windows Phone-program som registrerar sig självt med din meddelandehubb.

1. Skapa en ny Windows Phone 8-app i Visual Studio.

    ![Visual Studio – Nytt projekt – Windows Phone-App][13]

    I Visual Studio 2013 Update 2 eller senare kan du istället skapa en Silverlight-app för Windows Phone.

    ![Visual Studio – Nytt projekt – Tom app – Silverlight för Windows Phone][11]
2. Högerklicka på lösningen i Vision Studio och klicka sedan på **Hantera NuGet-paket**.
3. Leta rätt på `WindowsAzure.Messaging.Managed` och klicka på **Installera**. Godkänn sedan användningsvillkoren.

    ![Visual Studio – NuGet Package Manager][20]
4. Öppna filen App.xaml.cs och lägg till följande `using`-uttryck:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Lägg till följande kod längst upp i metoden `Application_Launching` i `App.xaml.cs`:

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > Värdet `MyPushChannel` är ett index som används för att söka efter en befintlig kanal i samlingen [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Om det inte finns någon sådan där, skapar du en ny post med det namnet.

    Infoga namnet på din hubb och den anslutningssträng med namnet `DefaultListenSharedAccessSignature` som du noterade i förra avsnittet.
    Den här koden hämtar URI-kanalen för appen från MPNS och registrerar sedan denna i din meddelandehubb. Det garanterar även att URI-kanalen registreras i meddelandehubben varje gång appen startas.

   > [!NOTE]
   > I denna självstudiekurs visar vi hur du skickar ett popup-meddelande till enheter. Om du vill skicka ett panelmeddelande måste du istället anropa metoden `BindToShellTile` på kanalen. Om du vill använda både popup- och panelmeddelanden anropar du både `BindToShellTile` och `BindToShellToast`.

6. I Solution Explorer expanderar du **Egenskaper**, öppnar `WMAppManifest.xml`-filen, klickar på fliken **Funktioner** och kontrollerar att funktionen **ID_CAP_PUSH_NOTIFICATION** är markerad. Din app kan ta emot push-meddelanden.

    ![Visual Studio – Funktioner för Windows Phone-appar][14]
7. Kör appen genom att trycka på tangenten `F5`. Ett registreringsmeddelande visas i appen.
8. Stäng appen eller växla till hemsidan.

   > [!NOTE]
   > Om du vill få ett push-meddelande av popup-typ får appen inte köras i förgrunden.

## <a name="test-send-a-notification"></a>Testskicka ett meddelande

1. Växla till fliken Översikt i Azure Portal.
2. Välj **Testskicka**.

    ![Knappen Testskicka](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. Utför följande steg i fönstret **Testskicka**:

    1. Välj **Windows Phone** under **Plattformar**.
    2. Välj **Toast** under **Meddelandetyp**.
    3. Välj **Skicka**
    4. Visa **resultatet** i listan längst ned i fönstret.

        ![Fönstret Testskicka](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Bekräfta att du kan se aviseringsmeddelandet i Windows Phone-emulatorn eller på Windows-telefonen.

    ![Meddelanden i Windows Phone](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Nästa steg

I det här enkla exemplet skickade du push-meddelanden till alla dina Windows Phone 8-enheter. Gå vidare till följande självstudiekurs om du vill lära dig hur man skickar meddelanden till specifika enheter:

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs – självstudiekurs för Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp
