---
title: "Kom igång med Azure Notification Hubs för Windows Phone-appar | Microsoft Docs"
description: "I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Silverlight-app på Windows Phone 8 eller Windows Phone 8.1."
services: notification-hubs
documentationcenter: windows
keywords: "push-meddelande, pushmeddelande, push för windows phone"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/06/2018
ms.author: jawh
ms.openlocfilehash: 38d60001293a3bae6eb9f90179abb8af7815fbe7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Kom igång med Azure Notification Hubs för Windows Phone-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Silverlight-app på Windows Phone 8 eller Windows Phone 8.1. Om du vill skicka meddelanden till Windows Phone 8.1 (utan Silverlight), ska du använda versionen för [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
I denna självstudiekurs skapar du en tom Windows Phone 8-app som tar emot push-meddelanden med hjälp av Microsoft Push Notification Service (MPNS). När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

> [!NOTE]
> SDK:erna för Windows Phone på Notification Hubs stöder inte användning av Windows Push Notification Service (WNS) med Silverlight-appar för Windows Phone 8.1. Om du vill använda WNS (istället för MPNS) med Silverlight-appar för Windows Phone 8.1, ska du följa anvisningarna i [Notification Hubs – självstudiekurs för Windows Phone Silverlight]. Där används istället REST-API:er.
> 
> 

I den här självstudiekursen visas ett enkelt scenario för sändning med Notification Hubs.

## <a name="prerequisites"></a>Nödvändiga komponenter
För den här kursen behöver du följande:

* [Visual Studio 2012 Express för Windows Phone], eller en senare version.

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Windows Phone 8-appar.

## <a name="create-your-notification-hub"></a>Skapa din meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Under <b>Notification Services</b> väljer du <b>Windows Phone (MPNS)</b>. Därefter markerar du kryssrutan <b>Aktivera ej autentiserade push-meddelanden</b>.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal – Aktivera ej autentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Hubben har nu skapats och konfigurerats för att skicka ej autentiserade meddelanden till Windows Phone.

> [!NOTE]
> I den här kursen används MPNS i ej autentiserat läge. MPNS i ej autentiserat läge har begränsningar för vilka meddelanden du kan skicka till varje kanal. Notification Hubs stöder [MPNS i autentiserat läge](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) genom att låta dig överföra ditt certifikat.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Ansluta appen till meddelandehubben
1. Skapa en ny Windows Phone 8-app i Visual Studio.
   
    ![Visual Studio – Nytt projekt – Windows Phone-App][13]
   
    I Visual Studio 2013 Update 2 eller senare kan du istället skapa en Silverlight-app för Windows Phone.
   
    ![Visual Studio – Nytt projekt – Tom app – Silverlight för Windows Phone][11]
2. Högerklicka på lösningen i Vision Studio och klicka sedan på **Hantera NuGet-paket**.
   
    Dialogrutan **Hantera NuGet-paket** öppnas.
3. Leta rätt på `WindowsAzure.Messaging.Managed` och klicka på **Installera**. Godkänn sedan användningsvillkoren.
   
    ![Visual Studio – NuGet Package Manager][20]
   
    Den här åtgärden hämtar, installerar och lägger till en referens i Azure Messaging-biblioteket för Windows med hjälp av <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">NuGet-paketet WindowsAzure.Messaging.Managed</a>.
4. Öppna filen App.xaml.cs och lägg till följande `using`-uttryck:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Lägg till följande kod högst upp i metoden **Application_Launching** i App.xaml.cs:
   
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
   
   > [!NOTE]
   > Värdet **MyPushChannel** är ett index som används för att söka efter en befintlig kanal i samlingen [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Om det inte finns någon sådan där, skapar du en ny post med det namnet.
   > 
   > 
   
    Infoga namnet på din hubb och anslutningssträngen med namnet **DefaultListenSharedAccessSignature** som du fick i förra avsnittet.
    Den här koden hämtar URI-kanalen för appen från MPNS och registrerar sedan denna i din meddelandehubb. Det garanterar även att URI-kanalen registreras i meddelandehubben varje gång appen startas.
   
   > [!NOTE]
   > I denna självstudiekurs visar vi hur du skickar ett popup-meddelande till enheter. Om du vill skicka ett panelmeddelande måste du istället anropa metoden **BindToShellTile** på kanalen. Om du vill använda både popup- och panelmeddelanden, anropar du både **BindToShellTile** och **BindToShellToast**.
   > 
   > 
6. I Solution Explorer expanderar du **Egenskaper**, öppnar `WMAppManifest.xml`-filen, klickar på fliken **Funktioner** och kontrollerar att funktionen **ID_CAP_PUSH_NOTIFICATION** är markerad.
   
    ![Visual Studio – Funktioner för Windows Phone-appar][14]
   
    Det här garanterar att din app kan ta emot push-meddelanden. Utan detta kommer alla försök att skicka ett push-meddelande till appen att misslyckas.
7. Kör appen genom att trycka på tangenten `F5`.
   
    Ett registreringsmeddelande visas i appen.
8. Stäng appen.  
   
   > [!NOTE]
   > Om du vill få ett push-meddelande av popup-typ får appen inte köras i förgrunden.
   >

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du push-meddelanden till alla dina Windows Phone 8-enheter. 

Mer information om hur du riktar in dig på specifika användare finns i självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare]. 

Om du vill dela in användarna efter intressegrupper läser du [Använda Notification Hubs för att skicka de senaste nyheterna]. 

Mer information om hur du använder Notification Hubs finns i [Riktlinjer om Notification Hubs].

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
[Visual Studio 2012 Express för Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Riktlinjer om Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Använda Notification Hubs för att skicka push-meddelanden till användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs – självstudiekurs för Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

