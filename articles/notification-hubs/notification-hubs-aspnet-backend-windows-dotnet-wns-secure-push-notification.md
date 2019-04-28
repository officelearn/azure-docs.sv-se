---
title: Azure Notification Hubs Secure Push
description: Lär dig hur du skickar säker push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459254"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Push-meddelanden på ett säkert sätt från Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

Stöd för push-meddelande i Microsoft Azure kan du komma åt en enkel att använda och utskalad push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila enheter plattformar.

På grund av föreskrifter säkerhetsbegränsningar, ibland ett program kan också vilja inkludera något i meddelandet som inte kan tillhandahållas via standard push-meddelandeinfrastruktur. Den här självstudien beskrivs hur du uppnår samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan klientenheten och appserverdelen.

På en hög nivå är flödet på följande sätt:

1. App backend-server:
   * Butiker säker nyttolast i backend-databasen.
   * Skickar ID för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Enheten kontaktar serverdelen begär säker nyttolasten.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i föregående flödet (och i den här självstudien), förutsätter vi att enheten lagrar en autentiseringstoken i lokal lagring, när användaren loggar in. Detta garanterar en helt sömlös upplevelse som enheten kan hämta den meddelandets säker nyttolast använder denna token. Om ditt program lagrar inte autentiseringstoken på enheten eller om dessa token kan ha upphört att gälla, enhetsapp när tas emot meddelandet ska visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar meddelandets nyttolast.

Den här säker Push-självstudien visar hur du skickar push-meddelanden på ett säkert sätt. Självstudiekursen bygger vidare på den [meddela användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) kursen, så du måste slutföra stegen i självstudien först.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat din meddelandehubb, enligt beskrivningen i [komma igång med Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Observera också att Windows Phone 8.1 kräver autentiseringsuppgifter för Windows (inte Windows Phone) och att bakgrundsaktiviteter inte fungerar på Windows Phone 8.0 eller 8.1 Silverlight. Windows Store-program, du kan ta emot aviseringar via en bakgrundsaktivitet endast om appen är aktiverat för låsskärm (klicka på kryssrutan i Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Ändra Windows Phone-projektet

1. I den **NotifyUserWindowsPhone** projektet, Lägg till följande kod i App.xaml.cs att registrera push bakgrundsaktiviteten. Lägg till följande kodrad i slutet av metoden `OnLaunched()`:

    ```c#
    RegisterBackgroundTask();
    ```
2. Fortfarande i App.xaml.cs lägger du till följande kod direkt efter den `OnLaunched()` metoden:

    ```c#
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
3. Lägg till följande `using` uttryck högst upp i filen App.xaml.cs:

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Klicka på **Spara alla** från menyn **Arkiv** i Visual Studio.

## <a name="create-the-push-background-component"></a>Skapa komponenten Push bakgrund

Nästa steg är att skapa komponenten push bakgrund.

1. Högerklicka på den översta noden i lösningen i Solution Explorer (**lösning SecurePush** i det här fallet), klicka sedan på **Lägg till**, klicka sedan på **nytt projekt**.
2. Expandera **Store-appar**, klicka sedan på **Windows Phone appar**, klicka sedan på **Windows Runtime-komponent (Windows Phone)**. Ge projektet namnet **PushBackgroundComponent**, och klicka sedan på **OK** att skapa projektet.

    ![][12]
3. I Solution Explorer högerklickar du på den **PushBackgroundComponent (Windows Phone 8.1)** projektet och klicka sedan på **Lägg till**, klicka sedan på **klass**. Ge den nya klassen namnet `PushBackgroundTask.cs`. Klicka på **Lägg till** att generera klassen.
4. Ersätt hela innehållet i den `PushBackgroundComponent` definitionen för namnområdet med följande kod och Ersätt platshållaren `{back-end endpoint}` med backend-slutpunkten fick när du distribuerar din serverdel:

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
5. I Solution Explorer högerklickar du på den **PushBackgroundComponent (Windows Phone 8.1)** projektet och klicka sedan på **hantera NuGet-paket**.
6. Klicka på **Online** till vänster.
7. Skriv **Http-klient** i rutan **Sök**.
8. I resultatlistan klickar du på **klientbibliotek för Microsoft HTTP**, och klicka sedan på **installera**. Slutför installationen.
9. Gå tillbaka till rutan **Sök** i NuGet och skriv **Json.net**. Installera den **Json.NET** paketera och Stäng fönstret för NuGet-Pakethanteraren.
10. Lägg till följande `using` uttryck överst i den `PushBackgroundTask.cs` fil:

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. I Solution Explorer i den **NotifyUserWindowsPhone (Windows Phone 8.1)** projektet genom att högerklicka på **referenser**, klicka sedan på **Lägg till referens...** . I dialogrutan Reference Manager markerar du kryssrutan bredvid **PushBackgroundComponent**, och klicka sedan på **OK**.
12. I Solution Explorer dubbelklickar du på **Package.appxmanifest** i den **NotifyUserWindowsPhone (Windows Phone 8.1)** projekt. Under **meddelanden**anger **popup-kompatibel** till **Ja**.

    ![][3]
13. Fortfarande är i **Package.appxmanifest**, klickar du på den **deklarationer** menyn längst upp. I den **tillgängliga deklarationer** listrutan klickar du på **bakgrundsaktiviteter**, och klicka sedan på **Lägg till**.
14. I **Package.appxmanifest**under **egenskaper**, kontrollera **Push-meddelande**.
15. I **Package.appxmanifest**under **Appinställningar**, typ **PushBackgroundComponent.PushBackgroundTask** i den **startpunkt** fältet.

    ![][13]
16. Från **Arkiv**-menyn klickar du på **Spara alla**.

## <a name="run-the-application"></a>Kör programmet

Om du vill köra programmet måste du göra följande:

1. I Visual Studio kör den **AppBackend** webb-API-program. En ASP.NET-webbsida visas.
2. I Visual Studio kör den **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone-app. Windows Phone-emulatorn körs och appen läses in automatiskt.
3. I den **NotifyUserWindowsPhone** appens användargränssnitt, ange ett användarnamn och lösenord. Det kan vara valfri sträng, men de måste vara samma värde.
4. I den **NotifyUserWindowsPhone** appens användargränssnitt, klickar du på **logga in och registrera**. Klicka sedan på **skicka push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
