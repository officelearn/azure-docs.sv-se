---
title: "Azure Mobile Engagement felsökning guider"
description: "Felsökningsguide för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement, felsökningsguide
## <a name="introduction"></a>Introduktion
Följande felsökningsguiden hjälper dig att förstå rotorsaker till vissa ofta upptäckta problem och kommer att du kan felsöka på egen hand. 

## <a name="general"></a>Allmänt
I allmänhet bör du alltid kontrollera följande:

1. Se till att du har gått igenom alla steg som krävs för integration enligt beskrivningen i vår [komma igång Självstudier](mobile-engagement-windows-store-dotnet-get-started.md)
2. Du använder den senaste versionen av plattform SDK. 
3. Testa både på en enhet och en emulator eftersom vissa problem som är specifika för endast emulatorn. 
4. Du inte träffa alla gränser/begränsas från Mobile Engagement som finns dokumenterade [här](../azure-subscription-service-limits.md)
5. Om du inte kan ansluta till tjänsten Mobile Engagement-serverdelen eller se data inte läses kontinuerligt sedan se till att det finns ingen pågående service incidenter genom att kontrollera [här](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problem med '-Övervakaren
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Jag får inte min enhet visas på fliken övervakning
Övervakaren visar de enheter som är anslutna till din Mobile Engagement-plattformen i realtid. Om du felsöker på en emulator och enheter, bör du se här minst en session. Om appen har distribuerats, ska du se aktiva sessioner mätaren återspeglar de enheter som är anslutna till plattformen i realtid. 

Om du inte kan se din enhet på fliken övervakning är det förmodligen ett problem för SDK-integration. Några vanliga åtgärder du kan åtgärda är följande:

1. Kontrollera att du använder rätt anslutningssträngen i mobilappen och det är från avsnittet SDK nycklar och inte i avsnittet för API-nycklar. Anslutningssträngen ansluter din mobila app till instansen av Mobile Engagement-app som du kommer att se din enhet på fliken övervakning. 
2. För Windows-plattformen - om sidan åsidosätter den `OnNavigatedTo` metod, måste du anropa `base.OnNavigatedTo(e)`.
3. Om du integrerar Mobile Engagement i en befintlig mobila app så att du kan också se till att det inte saknas några steg genom att titta på avancerade integreringen [här](mobile-engagement-windows-store-integrate-engagement.md)
4. Se till att minst en skärmaktivitet skickas genom att åsidosätta sidan med EngagementActivity beroende på plattform som du arbetar enligt beskrivningen i den [Kom igång-självstudierna](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Jag får fliken Övervakare visar en session även när jag har kopplats från eller stängd min app / emulatorn.
Om det bara är nu ansluten till plattformen och du använder en emulator för att öppna appen är detta troligen på grund av emulatorn egenskaper. I allmänhet måste du se till att du kommer tillbaka till startsidan på emulatorn för app-sessionen ska kopplas från har. Dessutom på Windows-plattformen, när du felsöker med Visual Studio, du kan behöva kontrollera att i Visual Studio, gå till den **Livscykelhändelser** menyraden och klicka på **pausa** verkligen avslutas sessionen. Se [Windows kursen](mobile-engagement-windows-store-dotnet-get-started.md) mer information. 

## <a name="analytics-issues"></a>'Analytics-problem
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Jag ser inte några data / uppdateras data på fliken Analytics
Analysdata beräknas om regelbundet och det kan ta upp till 24 timmar för den här uppdateringen. Dessa data är inte standarddatakällan i realtid och du ser den uppdateras i den här 24-timmarsformat tidsperiod.
Kontrollera att dock att du skickar minst en skärm eller en aktivitet till serverdelen plattform med antingen övergripande minst en sida med `EngagementActivity` eller anropa `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Jag får felaktigt avbildade datum/tid för en enhet på fliken Analytics
Hur lång tid för Analytics baseras på användarnas Enhetsinställningar datum. Kontrollera så att enheten har datumet korrekt inställd. 

## <a name="segment-issues"></a>'Segment-problem
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Jag har skapat ett segment och den visas nedtonat eller alla data visas inte
Skapa en segment är inte realtid för tillfället. Den beräknas samtidigt som analysdata sammanställs och så att det kan ta upp till 24 timmar. Du bör Kom tillbaka senare men samtidigt bör du också kontrollera att dina mobilappar verkligen skickar data som du som utgör segment. T.ex. Om en händelse säger ”foo” är inte som skickas av alla mobila enheter och det inte finnas några segmentdata för ett segment som skapats med EventName = foo som kriteriet. Du bör kontrollera att SDK-integration för din mobila app skickar data på rätt sätt. 

## <a name="reach-or-push-notifications-issues"></a>Problem med 'Nå' eller Push-meddelanden
### <a name="my-push-messages-are-not-being-delivered"></a>Push-meddelanden levereras inte
1. Försök att skicka meddelanden till en testenhet först för att säkerställa att alla komponenter - mobilappar, SDK och tjänsten är korrekt ansluten och kunna leverera push-meddelanden. 
2. Skicka alltid enklaste 'out app meddelandet ”först via en kampanj som inte har schemalagts och inte heller har alla målgruppskriterium som angetts. Detta är igen för att bevisa att meddelande nätverksanslutningen fungerar korrekt. 
3. Om du har problem med att leverera meddelanden i appen sedan är också det steg försöka skicka en avisering via out av app först. 
4. Se till att den 'Native Push' är korrekt konfigurerad för din mobila app. Beroende på plattform kommer den antingen inkluderar nycklar (Android, Windows) eller certifikat (iOS). Se [användargränssnittet - inställningar](mobile-engagement-user-interface-settings.md)
5. Utanför appen meddelanden kan också blockeras av användaren via mobila operativsystem så se till att detta inte är fallet. 
6. Se till att du inte anger den *Ignorera målgrupp, push skickas till användarna via API: et* alternativet i den **kampanj** avsnitt i en Reach kampanj eftersom det garanterar att kunde endast push-meddelanden skickas via API: er. 
7. Se till att du testar din push-kampanj med både en enhet som är anslutna via Wi-Fi och phone operatorn nätverk till att eliminera nätverksanslutning som en möjlig problem.
8. Kontrollera att systemet tidsvärdet på din emulatorn är korrekt eftersom alla synkroniserad enheter även stör Push Notification tjänstens förmåga att leverera meddelanden. 

Flera plattformsspecifika felsökning instruktionerna nedan:

1. **iOS** 
   
   * Kontrollera att certifikaten är giltiga och återstående för iOS Push-meddelanden. 
   * Se till att du korrekt konfigurerar en *produktion* certifikat i Mobile Engagement-appen. 
   * Se till att du testar på en *verkliga fysiska enheten.* IOS-simulatorn kan inte bearbeta push-meddelanden.
   * Se till att paket-ID är korrekt konfigurerad i mobilappen. Se anvisningarna [här](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * När du testar, kan du använda ”Ad Hoc” distribution i din mobila etableringsprofil. Du kommer inte att kunna ta emot aviseringar om din app kompileras med ”Debug”
2. **Android**
   
   * Se till att du har angett rätt projektnumret i din mobila app AndroidManifest.xml-filen som följs av \n tecken. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Se till att du inte är saknas eller felaktigt konfigurerad alla behörigheter i Android-manifestfilen 
   * Kontrollera att antalet projekt som du lägger till din klientapp är från samma konto där du har fått GCM-servernyckeln. En felaktig matchning mellan två förhindras din push-meddelanden från att gå ut. 
   * Om du tar emot system meddelanden, men inte i appen granska den [ange en ikon för meddelanden](mobile-engagement-android-get-started.md) som sannolikt du inte anger rätt ikon i Android-manifestfilen. 
   * Om du skickar ett meddelande om BigPicture: Kontrollera att om du har externa bilden servrar och sedan de behöver för att kunna stöd HTTP ”GET” och ”chef”.
3. **Windows**
   
   * Se till att du har associerat appen med en giltig Windows Store-app. I Visual Studio - behöver du högerklicka på projektet och väljer alternativet ”associera App med Store” och välj den app som du skapade i Windows Store. Windows Store-app ska vara samma från där du har fått interna push-autentiseringsuppgifter för att konfigurera i Mobile Engagement-portalen.
   * Om du får ut app push-meddelanden, men inte i appen meddelanden med `EngagementOverlay` integrering sedan se till att det finns ett rotelement i rutnätet på sidan. EngagementOverlay använder det första ”rutnät”-elementet som påträffas i xaml-fil du lägger till två vyer på sidan. Om du vill hitta där Webbvyer ställs in, kan du definiera ett rutnät med namnet ”EngagementGrid” som den men du måste se till att det finns tillräcklig höjd och bredd för två efterföljande vyer som visar meddelandet och följande meddelande som meddelande i appen:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Jag har skapat en push-meddelanden/meddelande/kampanj och även när det skickas mig meddelandet, det visas som ”aktiv”. Vad innebär det?
Den **kampanj** som du skapade i Mobile Engagement kallas så eftersom det är en tidskrävande push notification betydelse när nya enheter ansluter till din mobil plattform, de skickas automatiskt av meddelanden som du konfigurerar här, förutsatt att de uppfyller kriteriet som du anger i kampanjen. Detta är inte en en som enda Meddelandeinställningar. Du måste manuellt klickar du på den **Slutför** för att avsluta kampanjen så att det inte att skicka fler meddelanden. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Jag har skapat en push-kampanj och jag kan ta emot meddelanden har dock när jag öppnar appen visas samma meddelande även när jag hade åtgärdade det förut?
Detta beror troligtvis ske under testningen och om du använder emulatorerna eller några testa framework som TestFlight. Vad som händer här är att på varje app kör instans, den förvärva nya DeviceID och skicka det till vår backend som orsakar Mobile Engagement-plattformen ska behandlas som en ny enhet och skicka meddelandet. 

## <a name="getting-support"></a>Få Support
Om det inte går att lösa problemet kan själv och du:

1. Sök efter problemet i befintliga trådar på StackOverflow forum och [MSDN-forum](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) och om inte sedan ställa en fråga det. 
2. Om du hittar en funktion som saknas sedan lägga till/rösten för begäran på vår [UserVoice-forum](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Om du har Microsoft stöder öppna en supportincident genom att ange följande information: 
   * Azure prenumerations-ID
   * Plattform (t.ex. iOS, Android osv)
   * App-ID
   * Kampanj-ID (för problem med push notification)
   * Enhets-ID
   * Mobile Engagement SDK-version (t.ex. Android SDK v2.1.0)
   * Information om fel med felmeddelandet och scenario

