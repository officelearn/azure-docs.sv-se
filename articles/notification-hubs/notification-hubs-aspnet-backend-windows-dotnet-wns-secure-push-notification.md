---
title: "Azure Notification Hubs säker Push"
description: "Lär dig mer om att skicka säkra push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API."
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 9c626ec1534c4899588150a58c0da57b9d963f6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs säker Push
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
Stöd för push-meddelanden i Microsoft Azure kan du få en lätt att använda flera plattformar, skalats ut push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar.

På grund av reglerande säkerhetsbegränsningar, ibland ett program kan också innehålla något i meddelandet inte kan överföras via de standard push-infrastrukturen. Den här självstudiekursen beskrivs hur du kan uppnå samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan klientenheten och appens serverdel.

På en hög nivå är flödet:

1. På appens serverdel:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Enheten kontaktar serverdelen begär säker nyttolasten.
   * Appen kan du visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i det föregående flödet (och i den här självstudiekursen) antar vi att enheten lagrar en autentiseringstoken i lokal lagring när en användare loggar in. Detta garanterar en helt integrerad upplevelse som enheten kan hämta den anmälan säker nyttolast med denna token. Om ditt program inte kan lagra autentiseringstoken på enheten, eller om dessa token kan ha gått, visas vid mottagning av meddelanden i appen enhet ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar nyttolasten för meddelanden.

Den här säkra Push-kursen visar hur du skickar push-meddelanden på ett säkert sätt. Kursen bygger på den [meddela användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) självstudier, så du måste slutföra stegen i den här självstudiekursen först.

> [!NOTE]
> Den här kursen förutsätter att du har skapat och konfigurerat din meddelandehubb enligt beskrivningen i [komma igång med Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Observera också att Windows Phone 8.1 kräver autentiseringsuppgifter för Windows (inte Windows Phone) och bakgrundsaktiviteter inte fungerar på Windows Phone 8.0 eller Silverlight 8.1. För Windows Store-program, du kan ta emot meddelanden via en bakgrundsaktivitet endast om appen är aktiverad för låsskärm (klickar du på kryssrutan i Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Ändra Windows Phone-projektet
1. I den **NotifyUserWindowsPhone** projekt, Lägg till följande kod i App.xaml.cs att registrera push-bakgrundsaktivitet. Lägg till följande kodrad i slutet av den `OnLaunched()` metoden:
   
        RegisterBackgroundTask();
2. Fortfarande i App.xaml.cs lägger du till följande kod direkt efter den `OnLaunched()` metoden:
   
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
3. Lägg till följande `using` instruktioner överst i filen App.xaml.cs:
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. Klicka på **Spara alla** från menyn **Arkiv** i Visual Studio.

## <a name="create-the-push-background-component"></a>Skapa Push bakgrund komponent
Nästa steg är att skapa komponenten push bakgrund.

1. Högerklicka på den översta noden i lösningen i Solution Explorer (**lösning SecurePush** i det här fallet), klicka sedan på **Lägg till**, klicka sedan på **nytt projekt**.
2. Expandera **Store-appar**, klicka på **Windows Phone appar**, klicka på **Windows Runtime-komponent (Windows Phone)**. Namnge projektet **PushBackgroundComponent**, och klicka sedan på **OK** att skapa projektet.
   
    ![][12]
3. I Solution Explorer högerklickar du på den **PushBackgroundComponent (Windows Phone 8.1)** projektet och klicka sedan på **Lägg till**, klicka på **klassen**. Kalla den nya klassen **PushBackgroundTask.cs**. Klicka på **Lägg till** att skapa klassen.
4. Ersätt hela innehållet i den **PushBackgroundComponent** definitionen för namnområdet med följande kod, ersätter platshållaren `{back-end endpoint}` med backend-slutpunkten som erhålls vid distribution av din serverdel:
   
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
5. I Solution Explorer högerklickar du på den **PushBackgroundComponent (Windows Phone 8.1)** projektet och klicka sedan på **hantera NuGet-paket**.
6. Klicka på den vänstra sidan **Online**.
7. I den **Sök** skriver **http-klienten**.
8. Klicka i resultatlistan **klientbibliotek för Microsoft HTTP**, och klicka sedan på **installera**. Slutför installationen.
9. Tillbaka i NuGet **Sök** skriver **Json.net**. Installera den **Json.NET** paketet och sedan stänga fönstret NuGet Package Manager.
10. Lägg till följande `using` instruktioner överst i den **PushBackgroundTask.cs** fil:
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. I Solution Explorer i den **NotifyUserWindowsPhone (Windows Phone 8.1)** projekt, högerklicka på **referenser**, klicka på **Lägg till referens...** . I dialogrutan Reference Manager du kryssrutan bredvid **PushBackgroundComponent**, och klicka sedan på **OK**.
12. I Solution Explorer dubbelklickar du på **Package.appxmanifest** i den **NotifyUserWindowsPhone (Windows Phone 8.1)** projekt. Under **meddelanden**, ange **popup-kompatibel** till **Ja**.
    
    ![][3]
13. Fortfarande i **Package.appxmanifest**, klicka på den **deklarationer** menyn längst upp. I den **tillgängliga deklarationer** listrutan, klickar du på **bakgrundsaktiviteter**, och klicka sedan på **Lägg till**.
14. I **Package.appxmanifest**under **egenskaper**, kontrollera **Push notification**.
15. I **Package.appxmanifest**under **Appinställningar**, typen **PushBackgroundComponent.PushBackgroundTask** i den **startpunkten** fält.
    
    ![][13]
16. Från **Arkiv**-menyn klickar du på **Spara alla**.

## <a name="run-the-application"></a>Köra programmet
Om du vill köra programmet, gör du följande:

1. I Visual Studio kör den **AppBackend** Web API-program. En ASP.NET-webbsida visas.
2. I Visual Studio kör den **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone-app. Windows Phone-emulatorn kör och appen läses in automatiskt.
3. I den **NotifyUserWindowsPhone** app UI, ange ett användarnamn och lösenord. Det kan vara valfri sträng, men de måste ha samma värde.
4. I den **NotifyUserWindowsPhone** app-Gränssnittet klickar du på **logga in och registrera**. Klicka på **skicka push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
