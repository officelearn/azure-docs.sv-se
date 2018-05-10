---
title: Azure Notification Hub bort meddelande diagnos
description: Lär dig att felsöka vanliga problem med borttagna meddelanden i Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bc9ef70560f0485da81c1f54aa955cee76d280ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnostisera utelämnade aviseringar i Notification Hubs

En av de vanligaste frågorna från Azure Notification Hubs kunder är hur du felsöker när meddelanden som skickas från ett program inte visas på klientenheter. De vill veta när och varför meddelanden har tagits bort och hur du löser problemet. Den här artikeln identifierar varför meddelanden kan hämta bort eller inte kan tas emot av enheter. Lär dig hur du analyserar och avgöra den bakomliggande orsaken. 

Det är viktigt att först förstå hur tjänsten Meddelandehubbar skickar meddelanden till en enhet.

![Notification Hubs-arkitektur][0]

I en typisk skicka meddelande flöde meddelandet har skickats från den *programmet serverdel* till Notification Hubs. Notification Hubs stöder vissa bearbetning på alla registreringar. Bearbetningen tar hänsyn till de konfigurerade taggar och Tagguttryck att fastställa ”mål”. Mål är alla registreringar som behöver ta emot push-meddelande. Dessa registreringar kan sträcka sig över några eller alla våra plattformar som stöds: iOS, Google, Windows, Windows Phone, Kindle och Baidu för Kina Android.

Med de mål som upprättats Meddelandehubbar tjänsten skickar meddelanden till den *push notification service* för enhetsplattformen. Exempel inkluderar Apple Push Notification service (APNs) för Apple och Firebase Cloud Messaging (FCM) för Google. Notification Hubs push-meddelanden dela upp på flera batchar av registreringar. Notification Hubs autentiserar med respektive push notification-tjänsten baserat på de autentiseringsuppgifter som du anger i Azure-portalen under **konfigurera Meddelandehubben**. Push-meddelande tjänsten sedan vidarebefordrar aviseringar till respektive *klientenheter*. 

Den sista delen av Meddelandeleveransen äger rum mellan plattform push notification service och enheten. Fyra viktiga komponenter i push notification-processen (klient, serverdelen för programmet, Meddelandehubbar och plattform push notification service) kan medföra att meddelanden tas bort. Mer information om Notification Hubs-arkitekturen finns [översikt över Notification Hubs].

Det gick inte att leverera meddelanden kan uppstå under första/testning fasen. Utelämnade aviseringar i det här skedet kan tyda på ett konfigurationsproblem. Om det uppstår fel att leverera meddelanden i produktion, kan vissa eller alla meddelanden släppas. I det här fallet anges en djupare program eller messaging mönster problemet. 

I nästa avsnitt tittar på scenarier där meddelanden kan tas bort, allt från vanliga till mer ovanliga.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs felaktig konfiguration
Om du vill skicka meddelanden till respektive push notification-tjänsten kan måste tjänsten Meddelandehubbar autentisera sig själv i samband med utvecklarens program. För det här inträffar skapar utvecklare ett utvecklarkonto med respektive plattforms (Google, Apple, Windows och så vidare). Sedan registrerar utvecklaren sina program med plattformens där de kan hämta autentiseringsuppgifter. 

Du måste lägga till plattformen autentiseringsuppgifter till Azure-portalen. Om inga meddelanden ansluter till enheten, vara det första steget att säkerställa att rätt autentiseringsuppgifter har konfigurerats på Notification Hubs. Autentiseringsuppgifterna måste överensstämma med det program som har skapats under en plattformsspecifik utvecklarkonto. 

Stegvisa instruktioner för att slutföra den här processen, se [Kom igång med Azure Notification Hubs].

Här följer några vanliga felinställningar att söka efter:

* **Allmänt**
   
    * Kontrollera att namnet på din meddelandehubb (utan stavfel) är samma i var och en av dessa platser:

        * Registrerar från klienten.
        * Om du skickar meddelanden från serverdelen.
        * Om du har konfigurerat push notification service-autentiseringsuppgifter.
    
    * Kontrollera att du använder rätt delad åtkomst signatur configuration strängar på klienten och serverdelen för programmet. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på programmet serverdel (ger behörighet att skicka meddelanden till Notification Hubs).

* **APN-konfiguration**
   
    Du måste upprätthålla två olika NAV: ett nav för produktion och en annan hubb för testning. Det innebär att du måste överföra det certifikat som du använder i begränsat läge till en separat NAV än certifikat och hubb som du ska använda i produktionen. Försök inte att överföra olika typer av certifikat till samma hubb. Detta kan orsaka fel i meddelande. 
    
    Om du av misstag överföra olika typer av certifikat till samma hubb, rekommenderar vi att du tar bort hubben och börja om från början med en ny hubb. Om du av någon anledning inte ta bort hubben, åtminstone, måste du ta bort alla befintliga registreringar från hubben. 

* **FCM konfiguration** 
   
    1. Se till att den *servernyckel* att du har fått från Firebase matchar servernyckeln som du har registrerat i Azure-portalen.
   
    ![Firebase servernyckel][3]
   
    2. Kontrollera att du har konfigurerat **projekt-ID** på klienten. Du kan hämta värdet för **projekt-ID** från Firebase instrumentpanelen.
   
    ![Firebase projekt-ID][1]

## <a name="application-issues"></a>Programproblem
* **Taggar och Tagguttryck**

    Om du använder taggar eller Tagguttryck för att segmentera målgruppen, är det möjligt att när du skickar meddelandet inget mål hittades baserat på taggar eller Tagguttryck som du anger i send-anrop. 
    
    Granska dina registreringar för att säkerställa att det finns matchande taggar när du skickar ett meddelande. Kontrollera leveranskvitto enbart från klienter som har dessa registreringar. 
    
    Exempelvis om alla registreringar med Notification Hubs har gjorts med hjälp av taggen ”politik” och du kan skicka ett meddelande med taggen ”sport” skickas meddelandet inte till alla enheter. Komplexa fallet kan handla om Tagguttryck där du har registrerats med hjälp av ”tagg A” eller ”taggen B”, men vid sändning av meddelanden du rikta ”taggen A & & taggen B.” I avsnittet självövervakning tips senare i artikeln visar vi hur du granskar din registreringar och deras taggar. 

* **Problem med mallar**

    Om du använder mallar kan du se till att du följer de riktlinjer som beskrivs i [mallar]. 

* **Ogiltig registreringar**

    Ogiltigt mål finns om notification hub har konfigurerats korrekt och om alla taggar eller Tagguttryck användes på rätt sätt. Meddelanden ska skickas till dessa mål. Notification Hubs-tjänst och utlöses av flera bearbetning batchar parallellt. Varje grupp som skickar meddelanden till en uppsättning registreringar. 

    > [!NOTE]
    > Den ordning som meddelanden levereras garanteras inte eftersom bearbetning utförs parallellt. 

    Notification Hubs är optimerad för en ”en gång i de flesta” leveransmodell för meddelandet. Vi försöker deduplicering, så att inga meddelanden levereras mer än en gång till en enhet. För att säkerställa att detta, vi registreringar och kontrollerar att bara en meddelandet skickas per enhetsidentifierare innan meddelandet skickas till tjänsten för push-meddelanden. 

    Varje grupp som skickas till tjänsten push notification, som i sin tur är att acceptera och validera registreringar, är det möjligt att ett fel med en eller flera av registreringarna push notification-tjänsten identifierar i en batch. I det här fallet push notification-tjänsten returnerar ett fel till Notification Hubs och avbryts. Push notification service släpper den batchen helt. Detta gäller särskilt med APNS som använder ett protokoll för TCP-ström. 

    Vi är optimerade för i de flesta en leverans. Men i det här fallet arbetsbördan registreringen tas bort från databasen. Sedan gör vi Meddelandeleveransen för resten av enheter i den gruppen.

    Om du vill ha mer felinformation om försöket inte mot en registrering kan du använda Notification Hub REST-API: er [Per meddelande telemetri: hämta meddelanden meddelande telemetri](https://msdn.microsoft.com/library/azure/mt608135.aspx) och [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Exempelkod finns i [skicka REST exempel](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Push notification service problem
När meddelandet har tagits emot av plattform push notification service, ansvarar push notification service att leverera meddelandet till enheten. Nu tjänsten Meddelandehubbar ligger utanför bilden och har ingen kontroll över när eller om meddelandet levereras till enheten. 

Eftersom platform notification services är stabilt, tenderar meddelanden att nå enheter från push notification-tjänsten inom några sekunder. Om begränsning push notification-tjänsten är gäller Meddelandehubbar exponentiell inte strategi. Push notification-tjänsten är inte kan nås i 30 minuter, har vi en princip för att gälla och släpp meddelandena permanent. 

Om en push-Aviseringstjänst försöker att leverera ett meddelande men enheten är offline, lagras meddelandet av push notification-tjänsten under en begränsad tid. Meddelandet levereras till enheten när enheten blir tillgänglig. 

Endast en senaste meddelande lagras för varje app. Om flera meddelanden skickas när en enhet är offline, gör varje nytt meddelande tidigare meddelandet ignoreras. Att hålla senaste meddelandet kallas *buffertsammanslagning meddelanden* i APNs och *komprimera* i FCM (som använder en komprimera nyckel). Om enheten är offline under en längre tid, ignoreras de meddelanden som lagrats för enheten. Mer information finns i [APN översikt] och [om FCM meddelanden].

Med Azure Notification Hubs skickar du en buffertsammanslagning nyckel via ett HTTP-huvud med allmänna SendNotification API. Till exempel för .NET-SDK använder du **SendNotificationAsync**. SendNotification API tar också HTTP-huvuden som skickas-är till respektive push notification-tjänsten. 

## <a name="self-diagnosis-tips"></a>Självövervakning tips
Här följer sökvägar till diagnostisera orsaken till ignorerade meddelanden i Meddelandehubbar:

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter
* **Push notification service developer-portalen**
   
    Kontrollera autentiseringsuppgifterna i respektive push notification service developer portal (APN, FCM, Windows Notification Service och så vidare). Mer information finns i [Kom igång med Azure Notification Hubs].

* **Azure Portal**
   
    Om du vill granska och överensstämma med autentiseringsuppgifterna med de som du fick från den push notification service developer-portalen i Azure-portalen går du till den **åtkomstprinciper** fliken. 
   
    ![Azure-portalen åtkomstprinciper][4]

### <a name="verify-registrations"></a>Kontrollera registreringar
* **Visual Studio**
   
    Om du använder Visual Studio för utveckling, kan du ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive Notification Hubs. Detta är främst användbart för din miljö för utveckling och testning. 
   
    ![Visual Studio Server Explorer][9]
   
    Du kan visa och hantera alla registreringar i din hubb, kategoriserade efter plattform, interna eller mallen registrering, alla taggar, push notification service identifierare, registrerings-ID och upphör att gälla. Du kan också redigera en registrering på den här sidan. Detta är särskilt användbart för att redigera taggar. 
   
    ![Visual Studio enheten registreringar][8]
   
   > [!NOTE]
   > Redigera registreringar endast under utveckling och testning och med ett begränsat antal registreringar med hjälp av Visual Studio. Om du måste redigera din registreringar gruppvis, Överväg att använda export och importera registrering funktioner som beskrivs i [exportera och ändra registreringar gruppvis](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Service Bus Explorer**
   
    Många kunder använder [Service Bus Explorer] visa och hantera sina meddelandehubben. Service Bus Explorer är ett projekt med öppen källkod. Exempel finns [Service Bus Explorer koden].

### <a name="verify-message-notifications"></a>Kontrollera meddelanden
* **Azure Portal**
   
    Skicka ett testmeddelande till dina klienter utan att behöva en service serverdel igång, under **stöd + felsökning**väljer **prova att skicka**. 
   
    ![Testa skicka funktionerna i Azure][7]
* **Visual Studio**
   
    Du kan också skicka testmeddelanden från Visual Studio.
   
    ![Testa skicka funktioner i Visual Studio][10]
   
    Mer information om hur du använder Notification Hubs med Visual Studio Server Explorer finns i följande artiklar: 
   
   * [Visa enheten registreringar för notification hub]
   * [Ingående: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]
   * [Om versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelanderesultat
**Egenskapen EnableTestSend**

När du skickar en avisering via Notification Hubs från början är meddelandet i kö för bearbetning i Notification Hubs. Notification Hubs anger rätt mål och skickar sedan meddelandet till tjänsten för push-meddelanden. Om du använder REST-API: et eller någon av klient-SDK: er innebär samtalet skicka lyckas returneras endast att meddelandet har placerats har i kö med Notification Hubs. Du har inte någon inblick i vad som hänt när Notification Hubs så småningom skickas meddelandet till push notification service. 

Om ditt meddelande inte når klientenheten, är det möjligt att ett fel uppstod när Meddelandehubbar försök gjordes att leverera meddelandet till tjänsten för push-meddelanden. Exempelvis kan nyttolastens storlek överskrider den maximalt tillåtna av push notification-tjänsten eller de autentiseringsuppgifter som konfigurerats i Notification Hubs kan vara ogiltig. 

Du kan använda för att få insyn i push notification service-fel, den [EnableTestSend] egenskapen. Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen om du vill se detaljerad information för felsökning. Du kan också använda egenskapen via API: er. För närvarande kan du använda den i .NET SDK. Den kommer så småningom kommer att läggas till alla klient-SDK. 

Att använda den **EnableTestSend** egenskap med REST-anrop, lägga till en frågesträngsparameter kallas *testa* till slutet av send-anrop. Exempel: 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Exempel (.NET SDK)**

Här är ett exempel på med .NET SDK för att skicka en avisering om ursprunglig popup-fönster (popup):

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

I slutet av körningen, **resultat. Tillstånd** bara säger **köas**. Resultatet ger inte någon information om vad hände med push-meddelande. 

Därefter kan du använda den **EnableTestSend** boolesk egenskap. Använd den **EnableTestSend** egenskapen medan du initiera **NotificationHubClient** få detaljerad statusinformation om push notification service-fel som uppstår när meddelandet skickas. Skicka anropet tar extra tid att returnera eftersom endast när Notification Hub har lämnat anmälan till push notification service för att ta reda på resultatet returneras. 

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

**Exempel på utdata**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Det här meddelandet anger att antingen ogiltiga autentiseringsuppgifter har konfigurerats i Notification Hubs eller det finns ett problem med registreringen i hubben. Vi rekommenderar att du tar bort denna registrering och låta klienten återskapa registreringen innan du skickar meddelandet. 

> [!NOTE]
> Användning av den **EnableTestSend** egenskapen kraftigt begränsas. Använd det här alternativet endast i en miljö för utveckling och testning och med en begränsad uppsättning registreringar. Vi skicka debug-meddelanden till endast 10 enheter. Vi har också en gräns för bearbetning av debug skickar till 10 per minut. 
> 
> 

### <a name="review-telemetry"></a>Granska telemetri
* **Använda Azure Portal**
   
    Du kan hämta en snabb överblick över alla aktiviteter i din meddelandehubb i portalen. 
   
    1. På den **översikt** fliken visas en samlad vy över registreringar, meddelanden och fel av plattformen. 
   
        ![Instrumentpanel för Notification Hubs-översikt][5]
   
    2. På den **övervakaren** fliken kan du lägga till många andra plattformsspecifika mått för en djupare inblick. Du kan se specifikt fel relaterade till tjänsten för push-meddelanden som returneras när Notification Hub-tjänsten försöker skicka meddelandet till tjänsten för push-meddelanden. 
   
        ![Azure portal aktivitetsloggen][6]
   
    3. Börja genom att granska **inkommande meddelanden**, **registrering Operations**, och **lyckade meddelanden**. Gå sedan till fliken per plattform för att granska felen som är specifika för tjänsten för push-meddelanden. 
   
    4. Om inställningarna för meddelandehubben är felaktiga, meddelandet **PNS-autentiseringsfel** visas. Det här är en bra indikation på att kontrollera push notification service-autentiseringsuppgifter. 

* **Programmeringsåtkomst**

    Mer information om programmässig åtkomst finns i de här artiklarna: 

    * [Programmässiga telemetri åtkomst]  
    * [Telemetri åtkomst via API: er exempel] 

    > [!NOTE]
    > Flera telemetri-relaterade funktioner som exporterar och importerar registreringar och telemetri åtkomst via API: er, är bara tillgängliga på Standard-tjänstnivå. Om du försöker använda dessa funktioner från kostnadsfria eller grundläggande servicenivå, meddelande om ett undantag om du använder SDK och felmeddelandet HTTP 403 (förbjuden) om du använder funktioner direkt från REST-API: er. 
    >
    >Om du vill använda telemetri-relaterade funktioner, först se till i Azure portal att du använder Standard-tjänstnivå.  
> 
> 

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
[översikt över Notification Hubs]: notification-hubs-push-notification-overview.md
[Kom igång med Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[mallar]: https://msdn.microsoft.com/library/dn530748.aspx 
[APN översikt]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[om FCM meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer koden]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visa enheten registreringar för notification hub]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Ingående: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Om versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmässiga telemetri åtkomst]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetri åtkomst via API: er exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

