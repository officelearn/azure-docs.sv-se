---
title: Azure Meddelandehubbar bort meddelandet diagnos
description: Lär dig mer om att felsöka vanliga problem med utelämnade meddelanden i Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 9dd6a66ea416ad61682b8e33c6163db3ac345d92
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447725"
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnostisera utelämnade meddelanden i Notification Hubs

En av de vanligaste frågorna från Azure Notification Hubs-kunder är så här felsöker när meddelanden som skickas från ett program inte visas på klientenheter. De vill veta var och varför meddelanden har tagits bort och hur du löser problemet. Den här artikeln beskrivs varför meddelanden kan tas bort eller inte tas emot av enheter. Lär dig hur du analyserar och avgöra den bakomliggande orsaken.

Det är viktigt att först förstå hur tjänsten Meddelandehubbar skickar meddelanden till en enhet.

![Notification Hubs-arkitektur][0]

I en typisk skicka meddelande flow meddelandet har skickats från den *serverdelen program* till Notification Hubs. Meddelandehubbar utför viss bearbetning på alla registreringar. Bearbetningen tar hänsyn till de konfigurerade taggar och Tagguttryck att fastställa ”mål”. Mål är alla registreringar som behöver ta emot push-meddelande. Dessa registreringar kan spänna över några eller alla våra plattformar som stöds: iOS, Google, Windows, Windows Phone, Kindle och Baidu för Android Kina.

Med de mål som upprättats tjänsten Meddelandehubbar skickar meddelanden till den *push-meddelandetjänst* för enhetsplattformen. Exempel är Apple Push Notification service (APNs) för Apple och Firebase Cloud Messaging (FCM) för Google. Notification Hubs push-meddelanden dela upp på flera batchar av registreringar. Meddelandehubbar autentiserar med respektive push meddelandetjänsten baserat på de autentiseringsuppgifter som du anger i Azure-portalen under **konfigurera Meddelandehubben**. Push-meddelande tjänsten sedan vidarebefordrar meddelanden till respektive *klientenheter*.

Den sista delen av leverans av meddelanden äger rum mellan push-meddelandetjänst för plattformen och enheten. Någon av de fyra viktiga komponenterna i processen för push-meddelande (klient, serverdelen program, Notification Hubs och plattformen push notification service) kan orsaka meddelanden tas bort. Mer information om Notification Hubs-arkitekturen finns i [översikt över Notification Hubs].

Det gick inte att leverera meddelanden kan uppstå under inledande testning/mellanlagring fas. Utelämnade meddelanden i det här skedet kan tyda på ett konfigurationsproblem. Om det uppstår fel att leverera meddelanden i produktion, kan vissa eller alla meddelanden släppas. I det här fallet anges ett djupare program eller messaging mönstret problemet.

Nästa avsnitt tittar på scenarier där meddelanden kan släppas, sträcker sig från vanliga till mer sällsynta.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs misconfiguration

För att kunna skicka meddelanden till respektive push-meddelandetjänst, behöver Notification Hubs-tjänsten autentisera sig själv i samband med utvecklarens program. För detta ska ske måste skapar utvecklaren ett utvecklarkonto med respektive plattforms (Google, Apple, Windows och så vidare). Sedan registrerar utvecklare sina program med plattformen där de får autentiseringsuppgifter.

Du måste lägga till plattformen autentiseringsuppgifter till Azure-portalen. Om inga meddelanden ansluter till enheten, vara det första steget för att säkerställa att rätt autentiseringsuppgifter har konfigurerats i Notification Hubs. Autentiseringsuppgifterna måste matcha det program som har skapats under ett plattformsspecifika developer-konto.

Stegvisa instruktioner för att slutföra den här processen, se [Kom igång med Azure Notification Hubs].

Här följer några vanliga felkonfigureringar att söka efter:

**Allmänt:**

    * Se till att ditt meddelandehubbsnamn (utan skrivfel) är samma i var och en av de här platserna:
        * Registrerar från klienten.
        * Där kan du skicka meddelanden från backend-servern.
        * Där du konfigurerade push notification service-autentiseringsuppgifter.
    * Kontrollera att du använder strängar för signaturer konfiguration för rätt delad åtkomst på klienten och serverdelen program. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på programmet serverdel (ger behörighet att skicka meddelanden till Meddelandehubbar).

**APNs-konfiguration:**

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**FCM-konfiguration:**

    1. Se till att den *servernyckel* som du har skaffat från Firebase matchar den servernyckel som du registrerade i Azure-portalen.

    ![Firebase servernyckel][3]

    2. Kontrollera att du har konfigurerat **projekt-ID** på klienten. Du kan hämta värdet för **projekt-ID** från Firebase-instrumentpanelen.

    ![Firebase-projekt-ID][1]

## <a name="application-issues"></a>Programfel

**Taggar och Tagguttryck:**

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

** Problem med mallar: **

    If you use templates, ensure that you follow the guidelines described in [Templates].

**Ogiltig registreringar:**

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Push-meddelande tjänstproblem

När meddelandet har tagits emot av push-meddelandetjänst för plattformen, är det ansvar för push-meddelandetjänst för att leverera meddelandet till enheten. Nu kan tjänsten Meddelandehubbar ligger utanför bilden och har ingen kontroll över när eller om meddelandet skickas till enheten.

Eftersom plattformarnas meddelandetjänster är robust, brukar meddelanden att nå enheter från push-meddelandetjänst på några sekunder. Om push-meddelandetjänst begränsningar, gäller Meddelandehubbar en exponentiell backoff-strategi. Om push-meddelandetjänst är inte kan nås under 30 minuter, har vi en princip för att upphöra att gälla och permanent ta bort meddelandena.

Om en push-meddelandetjänst försöker leverera ett meddelande, men enheten är offline, lagras meddelandet av push-meddelandetjänst för en begränsad tidsperiod. Meddelandet skickas till enheten när enheten blir tillgänglig.

Endast en senaste meddelande lagras för varje app. Om flera meddelanden skickas när en enhet är offline, gör varje nytt meddelande tidigare meddelandet om du vill att tas bort. Att hålla endast senaste meddelandet kallas *buffertsammanslagning meddelanden* i APNs och *komprimera* i FCM (som använder en komprimera nyckel). Om enheten är offline under en längre tid, ignoreras alla meddelanden som sparades för enheten. Mer information finns i [APNs-översikt] och [Om FCM-meddelanden].

Med Azure Notification Hubs skickar du en buffertsammanslagning nyckel via ett HTTP-huvud med hjälp av generiska SendNotification-API. Till exempel för .NET-SDK använder du `SendNotificationAsync`. API: et SendNotification tar också HTTP-huvuden som skickas som – är att respektive push-meddelandetjänst.

## <a name="self-diagnosis-tips"></a>Tips för självdiagnos

Här följer sökvägar för att diagnostisera grundorsaken till utelämnade meddelanden i Meddelandehubbar:

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter

**Push notification service developer-portalen:**

Kontrollera autentiseringsuppgifterna i respektive push notification service developer-portalen (APNs, FCM, Windows-meddelandetjänsten och så vidare). Mer information finns i [Kom igång med Azure Notification Hubs].

**Azure-portalen:**

Om du vill granska och överensstämma med autentiseringsuppgifterna med de som du fick från push notification service developer-portalen, Azure-portalen går du till den **åtkomstprinciper** fliken.

![Azure-portalen åtkomstprinciper][4]

### <a name="verify-registrations"></a>Kontrollera registreringarna

**Visual Studio:**

Om du använder Visual Studio för utveckling, kan du ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive Notification Hubs. Detta är främst användbart för din miljö för utveckling och testning.

![Visual Studio Server Explorer][9]

Du kan visa och hantera alla registreringar i din hubb, kategoriserade efter plattform, intern eller registrering av mallen, några taggar, push notification service identifierare, registrerings-ID och upphör att gälla. Du kan också redigera en registrering på den här sidan. Detta är särskilt användbart för att redigera taggar.

![Visual Studio Device Registrations][8]

> [!NOTE]
> Använd Visual Studio om du vill redigera registreringar endast under utveckling och testning, och med ett begränsat antal registreringar. Om du vill redigera din många registreringar samtidigt kan du överväga att använda export och importera registrering-funktioner som beskrivs i [exportera och ändra många registreringar samtidigt](https://msdn.microsoft.com/library/dn790624.aspx).

**Service Bus Explorer:**

Många kunder använder [Service Bus Explorer] att visa och hantera sina meddelandehubben. Service Bus Explorer är ett projekt med öppen källkod. Exempel finns [Service Bus Explorer kod].

### <a name="verify-message-notifications"></a>Kontrollera meddelanden

 **Azure-portalen:**

Att skicka ett testmeddelande till dina klienter utan att ha en tjänst serverdel och drift, under **stöd + felsökning**väljer **Testsänd**.

![Testa funktionen för Skicka i Azure][7]

**Visual Studio:**

Du kan också skicka testmeddelanden från Visual Studio.

![Testa funktionen för Skicka i Visual Studio][10]

Mer information om att använda Meddelandehubbar med Visual Studio Server Explorer finns i följande artiklar:

* [Visa enhetsregistreringar för meddelandehubbar]
* [Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]
* [Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelanderesultat

**`EnableTestSend` egenskap:**

När du skickar ett meddelande via Meddelandehubbar, inledningsvis är meddelandet i kö för bearbetning i Notification Hubs. Meddelandehubbar avgör rätt mål och skickar meddelandet till push-meddelandetjänst. Om du använder REST API eller någon av klient-SDK: er innebär skicka anropet lyckas returneras endast att meddelandet har placerats i kö med Notification Hubs. Du har inte någon inblick i vad hände med Notification Hubs så småningom skickas meddelandet till push-meddelandetjänst.

Om din meddelandet inte kommer fram på klientenheten, är det möjligt att ett fel uppstod vid ett försök gjordes att leverera meddelandet till push-meddelandetjänst Meddelandehubbar. Till exempel nyttolast-storleken kanske överskrider den maximalt tillåtna av push-meddelandetjänsten eller de autentiseringsuppgifter som konfigurerats i Notification Hubs kan vara ogiltig.

Om du vill få information om push notification service fel, kan du använda den [EnableTestSend] egenskapen. Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen om du vill se detaljerad information för felsökning. Du kan också använda egenskapen via API: er. För närvarande kan du använda den i .NET SDK. Slutligen läggs den till alla klient-SDK.

Du använder den `EnableTestSend` egenskap med ett REST-anrop, lägga till en frågesträngsparameter som kallas *testa* i slutet av skicka anrop. Exempel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Exempel (.NET SDK):**

Här är ett exempel på hur du använder .NET SDK kan skicka ett meddelande med inbyggda popup-fönster (popup):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

I slutet av körningen `result.State` bara säger `Enqueued`. Resultatet ger inte någon information om vad hände med push-meddelande.

Därefter kan du använda den `EnableTestSend` boolesk egenskap. Använd den `EnableTestSend` egenskapen när du initiera `NotificationHubClient` att hämta detaljerad statusinformation om push notification service fel som uppstår när meddelandet skickas. Skicka anropet tar extra tid att returnera eftersom förrän Meddelandehubbar har levereras meddelandet till push-meddelandetjänst för att fastställa resultatet returneras.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Exempel på utdata:**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Det här meddelandet innebär att ogiltiga autentiseringsuppgifter har konfigurerats i Notification Hubs eller att det finns ett problem med registreringen i hubben. Vi rekommenderar att du tar bort denna registrering och låta klienten återskapa registreringen innan du skickar meddelandet.

> [!NOTE]
> Användning av den `EnableTestSend` egenskapen är kraftigt begränsad. Använd det här alternativet endast i en miljö för utveckling/testning, och med en begränsad uppsättning registreringar. Vi kan skicka felsökningsaviseringar till endast 10 enheter. Vi har också en gräns för bearbetning av debug skickar till 10 per minut.

### <a name="review-telemetry"></a>Granska telemetri

**Använd Azure-portalen:**

I portalen får du en snabb överblick över alla aktiviteter i din meddelandehubb.

1. På den **översikt** fliken visas en sammansatt vy av registreringar, meddelanden och fel efter plattform.

    ![Instrumentpanel med Notification Hubs][5]

2. På den **övervakaren** fliken kan du lägga till många andra plattformsspecifika mått för en djupare inblick. Du kan titta närmare på eventuella fel som rör push-meddelandetjänst som returneras när Notification Hubs-tjänsten försöker skicka meddelandet till push-meddelandetjänst.

    ![Azure-portalen aktivitetsloggen][6]

3. Börja genom att granska **inkommande meddelanden**, **registreringsåtgärder**, och **lyckade meddelanden**. Gå sedan till fliken per plattform för att granska fel som är specifika för push-meddelandetjänst.

4. Om autentiseringsinställningarna för din meddelandehubb är felaktiga, meddelandet **PNS-autentiseringsfel** visas. Det här är en bra indikation på att kontrollera autentiseringsuppgifterna för push notification-tjänsten.

* **Programmässig åtkomst**

Mer information om programmässig åtkomst finns i följande artiklar:

* [Telemetri för programmässig åtkomst]  
* [Telemetri åtkomst via API: er-exempel]

> [!NOTE]
> Flera telemetri-relaterade funktioner, som exporterar och importerar registreringar och telemetri åtkomst via API: er, är endast tillgängliga på Standard-tjänstnivå. Om du försöker använda dessa funktioner från kostnadsfritt eller Basic-tjänstnivån, meddelande om ett undantag om du använder SDK: N och felmeddelandet HTTP 403 (förbjudet) om du använder funktionerna direkt från REST-API: er.
>
> Om du vill använda telemetri-relaterade funktioner, kontrollera först i Azure portal att du använder Standard-tjänstnivå.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Översikt över Notification Hubs]: notification-hubs-push-notification-overview.md
[Kom igång med Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs-översikt]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Om FCM-meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer kod]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visa enhetsregistreringar för meddelandehubbar]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetri för programmässig åtkomst]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetri åtkomst via API: er-exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
