---
title: Diagnostisera ignorerade meddelanden i Azure Notification Hubs
description: Lär dig hur du diagnostiserar vanliga problem med ignorerade meddelanden i Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657591"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostisera ignorerade meddelanden i Azure Notification Hubs

En vanlig fråga om Azure Notification Hubs är hur du felsöker när meddelanden från ett program inte visas på klientenheter. Kunderna vill veta var och varför meddelanden har tagits bort och hur du åtgärdar problemet. Den här artikeln identifierar varför meddelanden kan tas bort eller inte tas emot av enheter. Det förklarar också hur man bestämmer orsaken.

Det är viktigt att först förstå hur Notification Hubs skickar meddelanden till en enhet.

![Arkitektur för meddelandehubbar][0]

I ett typiskt meddelandeflöde skickas meddelandet från *programmets serverdel* till Meddelandehubbar. Meddelandehubbar bearbetar alla registreringar. Den tar hänsyn till de konfigurerade taggarna och tagguttrycken för att bestämma målen. Mål är de registreringar som behöver ta emot push-meddelandet. Dessa registreringar kan sträcka sig över någon av våra plattformar som stöds: Android, Baidu (Android-enheter i Kina), Fire OS (Amazon) iOS, Windows och Windows Phone.

När målen har fastställts skickar Notification Hubs meddelanden till *push-meddelandetjänsten* för enhetsplattformen. Exempel på detta är Apple Push Notification Service (APNs) för iOS och macOS och Firebase Cloud Messaging (FCM) för Android-enheter. Meddelandehubbar skickar meddelanden som delas mellan flera grupper av registreringar. Den autentiserar med respektive push-meddelandetjänst, baserat på de autentiseringsuppgifter du anger i Azure-portalen, under **Konfigurera meddelandehubb**. Push-meddelandetjänsten vidarebefordrar sedan meddelandena till respektive *klientenheter*.

Den sista delen av meddelandeleveransen är mellan plattformens push-meddelandetjänst och enheten. Meddelandeleverans kan misslyckas i något av de fyra stegen i push-meddelandeprocessen (klient, programbakslut, meddelandehubbar och plattformens push-meddelandetjänst). Mer information om arkitekturen Notification Hubs finns i [översikt över meddelandehubbar].

Ett fel på att leverera meddelanden kan inträffa under den inledande test-/mellanlagringsfasen. Ignorerade meddelanden i det här skedet kan tyda på ett konfigurationsproblem. Om ett fel att leverera meddelanden inträffar i produktionen kan vissa eller alla meddelanden tas bort. Ett djupare problem med program- eller meddelandemönster anges i det här fallet.

I nästa avsnitt visas scenarier där meddelanden kan tas bort, från vanliga till sällsynta.

## <a name="notification-hubs-misconfiguration"></a>Felkonfiguration av meddelandehubbar

Om du vill skicka meddelanden till respektive push-meddelandetjänst måste Notification Hubs autentisera sig själv i samband med ditt program. Du måste skapa ett utvecklarkonto med målplattformens meddelandetjänst (Microsoft, Apple, Google osv.). Sedan måste du registrera ditt program med operativsystemet där du får en token eller nyckel som du använder för att arbeta med målet PNS.

Du måste lägga till plattformsautentiseringsuppgifter i Azure-portalen. Om inga meddelanden når enheten är det första steget att se till att rätt autentiseringsuppgifter konfigureras i meddelandehubbar. Autentiseringsuppgifterna måste matcha programmet som skapas under ett plattformsspecifikt utvecklarkonto.

Stegvisa instruktioner för att slutföra den här processen finns i [Komma igång med Azure Notification Hubs].

Här är några vanliga felkonfigurationer att söka efter:

### <a name="notification-hub-name-location"></a>Plats för meddelandehubbens namn

Kontrollera att ditt meddelandehubbnamn (utan stavfel) är detsamma på var och en av dessa platser:

* Var du registrerar dig från kunden
* Var du skickar meddelanden från den bakre delen
* Där du har konfigurerat autentiseringsuppgifterna för push-meddelandetjänsten

Se till att du använder rätt konfigurationssträngar för delad åtkomstsignatur på klienten och programmets serverdel. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på programmets serverdel. Detta ger behörighet att skicka meddelanden till Meddelandehubbar.

### <a name="apn-configuration"></a>APN-konfiguration

Du måste underhålla två olika hubbar: en för produktion och en annan för testning. Du måste överföra certifikatet som du använder i en sandbox-miljö till ett separat nav än det certifikat/nav som du ska använda i produktionen. Försök inte att överföra olika typer av certifikat till samma nav. Det kommer att orsaka meddelandefel.

Om du av misstag överför olika typer av certifikat till samma hubb bör du ta bort navet och börja om från början med en ny hubb. Om du av någon anledning inte kan ta bort navet måste du åtminstone ta bort alla befintliga registreringar från navet.

### <a name="fcm-configuration"></a>FCM-konfiguration

1. Kontrollera att *servernyckeln* som du har fått från Firebase matchar servernyckeln som du registrerade i Azure-portalen.

   ![Firebase-servernyckel][3]

2. Kontrollera att du har konfigurerat **Project ID** på klienten. Du kan hämta värdet för **Project ID** från Firebase-instrumentpanelen.

   ![Projekt-ID för Firebase][1]

## <a name="application-issues"></a>Problem med program

### <a name="tags-and-tag-expressions"></a>Taggar och tagguttryck

Om du använder taggar eller tagguttryck för att segmentera målgruppen är det möjligt att inget mål hittas när du skickar meddelandet. Det här felet baseras på de angivna taggarna eller tagguttrycken i ditt skicka-samtal.

Granska dina registreringar för att se till att taggarna matchar när du skickar ett meddelande. Verifiera sedan meddelandekvittot från endast de klienter som har dessa registreringar.

Anta till exempel att alla dina registreringar med Meddelandehubbar använder taggen "Politik". Om du sedan skickar ett meddelande med taggen "Sport" skickas inte meddelandet till någon enhet. Ett komplext ärende kan omfatta tagguttryck där du registrerade med hjälp av "Tagg A" *eller* "Tagg B", men du har riktat in dig på Tagg A && tagg B. Avsnittet självdiagnostips senare i artikeln visar hur du granskar dina registreringar och deras taggar.

### <a name="template-issues"></a>Problem med mall

Om du använder mallar kontrollerar du att du följer riktlinjerna i [Mallar].

### <a name="invalid-registrations"></a>Ogiltiga registreringar

Om meddelandehubben har konfigurerats korrekt och taggar eller tagguttryck användes korrekt, hittas giltiga mål. Meddelanden bör skickas till dessa mål. Meddelandehubbar avfyrar sedan flera bearbetningsbatchar parallellt. Varje batch skickar meddelanden till en uppsättning registreringar.

> [!NOTE]
> Eftersom Notification Hubs bearbetar batchar parallellt garanteras inte den ordning i vilken meddelandena levereras.

Meddelandehubbar är optimerade för en "högst en gång"-meddelandeleveransmodell. Vi försöker deduplicering, så att inga meddelanden levereras mer än en gång till en enhet. Registreringar kontrolleras för att säkerställa att endast ett meddelande skickas per enhetsidentifierare innan det skickas till push-meddelandetjänsten.

Varje batch skickas till push-meddelandetjänsten, som i sin tur accepterar och validerar registreringarna. Under den här processen är det möjligt att push-meddelandetjänsten identifierar ett fel med en eller flera registreringar i en batch. Push-meddelandetjänsten returnerar sedan ett fel till Meddelandehubbar och processen stoppas. Push-meddelandetjänsten släpper batchen helt. Detta gäller särskilt med APN, som använder ett TCP-dataflödesprotokoll.

I det här fallet tas felregistreringen bort från databasen. Sedan försöker vi skicka meddelanden igen för resten av enheterna i batchen.

Om du vill ha mer felinformation om det misslyckade leveransförsöket mot en registrering kan du använda REST-API:erna för meddelande-api:er [per meddelandetelemetri: Hämta e-posttelemetri](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) och [PNS-feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Exempelkod finns i [exemplet Skicka REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problem med push-meddelandetjänsten

När push-meddelandetjänsten har fått meddelandet levererar den meddelandet till enheten. Nu har Notification Hubs ingen kontroll över leveransen av meddelandet till enheten.

Eftersom plattformsmeddelandetjänster är robusta tenderar aviseringar att nå enheter på några sekunder. Om push-meddelandetjänsten är begränsning tillämpar Notification Hubs en exponentiell back-off-strategi. Om push-meddelandetjänsten inte kan nås i 30 minuter finns det en princip som ska upphöra att gälla och släppa meddelandena permanent.

Om en push-meddelandetjänst försöker leverera ett meddelande men enheten är offline lagras meddelandet av push-meddelandetjänsten. Den lagras under en begränsad tid. Meddelandet levereras till enheten när enheten blir tillgänglig.

Varje app lagrar bara ett meddelande nyligen. Om flera meddelanden skickas medan en enhet är offline gör varje nytt meddelande att det sista ignoreras. Att bara behålla det senaste meddelandet kallas *sammanslagning* i APN och *kollapsar* i FCM. (FCM använder en komprimerande nyckel.) När enheten är offline under en längre tid ignoreras meddelanden som lagrats för enheten. Mer information finns i [APNs Översikt] och [Om FCM-meddelanden].

Med Meddelandehubbar kan du skicka en sammanslagningsnyckel via ett HTTP-huvud med hjälp av det allmänna SendNotification API:et. För .NET SDK använder du till `SendNotificationAsync`exempel . SendNotification API tar också HTTP-huvuden som skickas som är till respektive push meddelande tjänst.

## <a name="self-diagnosis-tips"></a>Självdiagnos tips

Här är sökvägar för att diagnostisera orsaken till ignorerade meddelanden i meddelandehubbar.

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter

#### <a name="push-notification-service-developer-portal"></a>Utvecklarportal för push-meddelandetjänst

Verifiera autentiseringsuppgifter i respektive push notification-tjänstutvecklare (APN, FCM, Windows Notification Service och så vidare). Mer information finns i [Självstudiekurs: Skicka meddelanden till universella Windows Platform-appar med hjälp av Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Azure Portal

Om du vill granska och matcha autentiseringsuppgifterna med de autentiseringsuppgifter som du har fått från push notification-tjänstutvecklaren går du till fliken **Åtkomstprinciper** i Azure-portalen.

![Principer för åtkomst till Azure Portal][4]

### <a name="verify-registrations"></a>Verifiera registreringar

#### <a name="visual-studio"></a>Visual Studio

I Visual Studio kan du ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive meddelandehubbar. Den här genvägen är främst användbar för din utvecklings-/testmiljö.

![Utforskaren för Visual Studio Server][9]

![Server Explorer](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Du kan visa och hantera alla registreringar i navet. Registreringarna kan kategoriseras efter plattform, inbyggt registrerings- eller mallregistrering, tagg, push notification-tjänstidentifierare, registrerings-ID och utgångsdatum. Du kan också redigera en registrering på den här sidan. Det är särskilt användbart för redigering av taggar.

Högerklicka på meddelandehubben i **Serverutforskaren**och välj **Diagnostisera**. 

![Utforskaren för Visual Studio Server: Diagnostisera-menyn](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Följande sida visas:

![Visual Studio: Diagnostisera sida](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Växla till sidan **Enhetsregistreringar:**

![Visual Studio: Enhetsregistreringar](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Du kan använda sidan **Testa skicka** för att skicka ett testmeddelande:

![Visual Studio: Testa skicka](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Använd Visual Studio för att redigera registreringar endast under utveckling/test och med ett begränsat antal registreringar. Om du behöver redigera dina registreringar i grupp kan du överväga att använda funktionen för export- och importregistrering som beskrivs i [Så här exporterar och ändrar du registreringar i Bulk](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Många kunder använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) för att visa och hantera sina meddelandehubbar. Service Bus Explorer är ett projekt med öppen källkod. 

### <a name="verify-message-notifications"></a>Verifiera meddelandemeddelanden

#### <a name="azure-portal"></a>Azure Portal

Om du vill skicka ett testmeddelande till dina klienter utan att en tjänst tillbaka hamna och köras väljer du **Testa Skicka**under SUPPORT **+ FELSÖKNING**.

![Testa skicka-funktioner i Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Du kan också skicka testmeddelanden från Visual Studio.

![Testa skicka-funktioner i Visual Studio][10]

Mer information om hur du använder Notification Hubs med Visual Studio Server Explorer finns i följande artiklar:

* [Så här visar du enhetsregistreringar för meddelandehubbar](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Djupdykning: Visual Studio 2013 Uppdatering 2 RC och Azure SDK 2.3]
* [Tillkännager lanseringen av Visual Studio 2013 Update 3 och Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelanderesultat

#### <a name="enabletestsend-property"></a>Egenskapen EnableTestSend

När du skickar ett meddelande via Meddelandehubbar ställs meddelandet inledningsvis i kö. Meddelandehubbar bestämmer rätt mål och skickar sedan meddelandet till push-meddelandetjänsten. Om du använder REST API eller någon av klientens SDK:er betyder returen av ditt skickasamtal bara att meddelandet står i kö med meddelandehubbar. Det ger inte insikt i vad som hände när Meddelandehubbar så småningom skickade meddelandet till push-meddelandetjänsten.

Om meddelandet inte kommer fram till klientenheten kan ett fel ha uppstått när Meddelandehubbar försökte leverera det till push-meddelandetjänsten. Nyttolaststorleken kan till exempel överskrida det högsta tillåtna värdet av push-meddelandetjänsten, eller så kan autentiseringsuppgifterna som konfigurerats i Meddelandehubbar vara ogiltiga.

Om du vill få insikt i push-meddelandetjänstfel kan du använda egenskapen [EnableTestSend.] Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen för att se detaljerad felsökningsinformation och även via API:er. För närvarande kan du använda den i .NET SDK. Det kommer att läggas till alla klient-SDK:er så småningom.

Om du `EnableTestSend` vill använda egenskapen med REST-anropet lägger du till en frågesträngparameter som kallas *test* i slutet av ditt skickarop. Ett exempel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK-exempel

Här är ett exempel på hur du använder .NET SDK för att skicka ett inbyggt popup-meddelande (toast):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

I slutet av `result.State` utförandet, `Enqueued`helt enkelt stater . Resultaten ger ingen inblick i vad som hände med din push-meddelande.

Därefter kan du `EnableTestSend` använda egenskapen Boolean. Använd `EnableTestSend` egenskapen medan `NotificationHubClient` du initierar för att få en detaljerad status om push-meddelandetjänstfel som uppstår när meddelandet skickas. Skicka-samtalet tar ytterligare tid att returnera eftersom det först behöver Meddelandehubbar för att leverera meddelandet till push-meddelandetjänsten.

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

#### <a name="sample-output"></a>Exempel på utdata

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Det här meddelandet anger antingen att autentiseringsuppgifterna som konfigurerats i Meddelandehubbar är ogiltiga eller att det finns ett problem med registreringarna i navet. Ta bort den här registreringen och låt klienten återskapa registreringen innan meddelandet skickas.

> [!NOTE]
> Användningen av `EnableTestSend` fastigheten är kraftigt begränsad. Använd det här alternativet endast i en utvecklings-/testmiljö och med en begränsad uppsättning registreringar. Felsökningsmeddelanden skickas till endast 10 enheter. Det finns också en gräns för bearbetning av felsökningsskick, 10 per minut.

### <a name="review-telemetry"></a>Granska telemetri

#### <a name="azure-portal"></a>Azure Portal

I portalen kan du få en snabb översikt över all aktivitet i meddelandehubben.

1. På fliken **Översikt** kan du se en aggregerad vy över registreringar, meddelanden och fel per plattform.

   ![Översiktsinstrumentpanel för meddelandehubbar][5]

2. På fliken **Övervakare** kan du lägga till många andra plattformsspecifika mått för en djupare titt. Du kan titta specifikt på fel som returneras när Meddelandehubbar försöker skicka meddelandet till push-meddelandetjänsten.

   ![Aktivitetslogg för Azure Portal][6]

3. Börja med att granska **inkommande meddelanden,** **registreringsåtgärder**och **lyckade meddelanden**. Gå sedan till fliken per plattform för att granska fel som är specifika för push-meddelandetjänsten.

4. Om autentiseringsinställningarna för meddelandehubben är felaktiga visas meddelandet **PNS Authentication Error.** Det är en bra indikation för att kontrollera autentiseringsuppgifterna för push-meddelandetjänsten.

#### <a name="programmatic-access"></a>Programmässig åtkomst

Mer information om programmatisk åtkomst finns i [Programmatisk åtkomst](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Flera telemetrirelaterade funktioner, som exporterar och importerar registreringar och telemetriåtkomst via API:er, är endast tillgängliga på standardtjänstnivån. Om du försöker använda dessa funktioner från tjänstnivån Kostnadsfri eller Grundläggande visas ett undantagsmeddelande om du använder SDK. Ett HTTP 403-fel (Förbjudet) om du använder funktionerna direkt från REST-API:erna.
>
> Om du vill använda telemetrirelaterade funktioner kontrollerar du först i Azure-portalen att du använder standardtjänstnivån.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Översikt över meddelandehubbar]: notification-hubs-push-notification-overview.md
[Komma igång med Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Mallar]: https://msdn.microsoft.com/library/dn530748.aspx
[Översikt över AP-nätverk]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Om FCM-meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Djupdykning: Visual Studio 2013 Uppdatering 2 RC och Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Tillkännager lanseringen av Visual Studio 2013 Update 3 och Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[AktiveraTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
