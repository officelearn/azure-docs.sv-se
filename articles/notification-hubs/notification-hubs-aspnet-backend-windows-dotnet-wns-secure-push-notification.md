---
title: Azure Notification Hubs Secure Push för Windows
description: Läs om hur du skickar säkra push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530739"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Push-meddelanden från Azure Notification Hubs på ett säkert sätt

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

Med push-meddelandestöd i Microsoft Azure kan du komma åt en lättanvänd push-infrastruktur med flera plattformar, vilket förenklar implementeringen av push-meddelanden för både konsument- och företagsprogram för mobila Plattformar.

På grund av regel- eller säkerhetsbegränsningar kan ibland ett program vilja inkludera något i meddelandet som inte kan överföras via standardinfrastrukturen för push-meddelanden. Den här självstudien beskriver hur du uppnår samma upplevelse genom att skicka känslig information via en säker, autentiserat anslutning mellan klientenheten och appens backend.

På en hög nivå är flödet följande:

1. Appens baksida:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID:t för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten, när du tar emot meddelandet:
   * Enheten kontaktar backend-begäran om säker nyttolast.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att notera att i föregående flöde (och i den här självstudien) antar vi att enheten lagrar en autentiseringstoken i lokal lagring, när användaren loggar in. Detta garanterar en helt sömlös upplevelse, eftersom enheten kan hämta meddelandets säkra nyttolast med den här token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan komma att gälla, bör enhetsappen när du tar emot meddelandet visa ett allmänt meddelande som uppmanar användaren att starta appen. Appen autentiserar sedan användaren och visar aviseringsnyttolasten.

Den här självstudien för säker push visar hur du skickar ett push-meddelande på ett säkert sätt. Självstudien bygger på instruktionen [Meddela användare,](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) så du bör slutföra stegen i den självstudien först.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat meddelandehubben enligt beskrivningen i [Komma igång med Meddelandehubbar (Windows Store).](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
> Observera också att Windows Phone 8.1 kräver Windows-autentiseringsuppgifter (inte Windows Phone) och att bakgrundsuppgifter inte fungerar på Windows Phone 8.0 eller Silverlight 8.1. För Windows Store-program kan du bara ta emot aviseringar via en bakgrundsuppgift om appen är låsskärmsaktiverad (klicka på kryssrutan i Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Ändra Windows Phone-projektet

1. I projektet **NotifyUserWindowsPhone** lägger du till följande kod i App.xaml.cs för att registrera push-bakgrundsaktiviteten. Lägg till följande kodrad i slutet av metoden `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Lägg fortfarande till i App.xaml.cs följande kod `OnLaunched()` direkt efter metoden:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. Lägg till `using` följande satser högst upp i App.xaml.cs-filen:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Klicka på **Spara alla** från menyn **Arkiv** i Visual Studio.

## <a name="create-the-push-background-component"></a>Skapa push-bakgrundskomponenten

Nästa steg är att skapa push-bakgrundskomponenten.

1. Högerklicka på den översta noden i lösningen**i** Solution Explorer och klicka sedan på **Lägg till**och klicka sedan på **Nytt projekt**.
2. Expandera **Store-appar**och klicka sedan på **Windows Phone Apps**och klicka sedan på Windows **Runtime Component (Windows Phone).** Namnge projektet **PushBackgroundComponent**och klicka sedan på **OK** för att skapa projektet.

    ![][12]
3. Högerklicka på projektet **PushBackgroundComponent (Windows Phone 8.1)** i Solution Explorer och klicka sedan på **Lägg till**och klicka sedan på **Klass**. Ge den `PushBackgroundTask.cs`nya klassen ett namn . Klicka på **Lägg till** om du vill generera klassen.
4. Ersätt hela innehållet `PushBackgroundComponent` i namnområdesdefinitionen med följande kod `{back-end endpoint}` och ersätt platshållaren med den backend-endpoint som erhålls när du distribuerar backend:Replace the whole contents of the namespace definition with the following code, replaceing the placeholder with the back-endpoint obtained while deploying your back-end:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. Högerklicka på projektet **PushBackgroundComponent (Windows Phone 8.1) i** Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
6. Klicka på **Online** till vänster.
7. Skriv **Http-klient** i rutan **Sök**.
8. Klicka på Microsoft **HTTP-klientbibliotek**i resultatlistan och klicka sedan på **Installera**. Slutför installationen.
9. Gå tillbaka till rutan **Sök** i NuGet och skriv **Json.net**. Installera **Json.NET-paketet** och stäng sedan nuget-pakethanterarens fönster.
10. Lägg till `using` följande satser `PushBackgroundTask.cs` högst upp i filen:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. Högerklicka på **Referenser**i projektet **NotifyUserWindowsPhone (Windows Phone 8.1)** i Solution Explorer och klicka sedan på **Lägg till referens...**. Markera kryssrutan bredvid **PushBackgroundComponent**i dialogrutan Referenshanteraren och klicka sedan på **OK**.
12. I Solution Explorer dubbelklickar du på **Package.appxmanifest** i projektet **NotifyUserWindowsPhone (Windows Phone 8.1).** Under **Meddelanden**anger du **Toast-kompatibel** till **Ja**.

    ![][3]
13. Fortfarande i **Package.appxmanifest,** klicka på **menyn Deklarationer** nära toppen. Klicka på **Bakgrundsaktiviteter**i listrutan **Tillgängliga deklarationer** och klicka sedan på **Lägg till**.
14. Kontrollera **Package.appxmanifest** **Push-meddelande**under **Egenskaper.**
15. Skriv **Package.appxmanifest** **PushBackgroundComponent.PushBackgroundTask** under Appinställningar i fältet **Startpunkt** under **Appinställningar.**

    ![][13]
16. Från **Arkiv**-menyn klickar du på **Spara alla**.

## <a name="run-the-application"></a>Kör programmet

Så här kör du programmet:

1. I Visual Studio kör du **AppBackend** Web API-programmet. En ASP.NET webbsida visas.
2. I Visual Studio kör du **Appen NotifyUserWindowsPhone (Windows Phone 8.1).** Windows Phone-emulatorn körs och läser in appen automatiskt.
3. Ange ett användarnamn och lösenord i appgränssnittet **NotifyUserWindowsPhone.** Dessa kan vara vilken sträng som helst, men de måste vara samma värde.
4. Klicka på **Logga in och registrera**i appgränssnittet **NotifyUserWindowsPhone.** Klicka sedan på **Skicka push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
