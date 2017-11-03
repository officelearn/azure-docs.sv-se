---
title: Windows Phone Silverlight Reach SDK-Integration
description: Integrera Azure Mobile Engagement Reach med Windows Phone Silverlight-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0738f33df94d14fbb393bfaaf09e94c6560213cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight Reach SDK-Integration
Du måste följa proceduren integrering i den [Windows Phone Silverlight Engagement SDK Integration](mobile-engagement-windows-phone-integrate-engagement.md) innan du följer den här guiden.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Bädda in Engagement nå SDK i din Windows Phone Silverlight-projekt
Du har inte något att lägga till. `EngagementReach`referenser och resurser finns redan i projektet.

> [!TIP]
> Du kan anpassa avbildningar som finns i den `Resources` mappen i ditt projekt, speciellt märke ikonen (som standard till ikonen Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Lägg till funktioner
Engagement nå SDK måste vissa ytterligare funktioner.

Öppna din `WMAppManifest.xml` filen och se till att följande funktioner har deklarerats:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

Den första som används av tjänsten MPNS att visningen av popup-meddelande. Den andra som används för att bädda in en webbläsare aktivitet i SDK.

Redigera den `WMAppManifest.xml` och Lägg till i den `<Capabilities />` tagg:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Aktivera Microsoft Push Notification Service
För att kunna använda den **Microsoft Push Notification Service** (kallas MPNS) din `WMAppManifest.xml` filen måste ha en `<App />` taggen med en `Publisher` -attributet inställt på namnet på ditt projekt.

## <a name="initialize-the-engagement-reach-sdk"></a>Initiera Engagement Reach SDK
### <a name="engagement-configuration"></a>Engagement konfiguration
Konfigurationen av Engagement centraliserad i den `Resources\EngagementConfiguration.xml` filen i projektet.

Redigera den här filen om du vill ange reach-konfiguration:

* *Valfria*, ange om den interna push (MPNS) är aktiverad eller inte mellan `<enableNativePush>` och `</enableNativePush>` taggar (`true` som standard).
* *Valfria*, ange namnet på push-kanal mellan `<channelName>` och `</channelName>` taggar, ger samma att ditt program kan använda eller lämna det tomt.

Om du vill ange vid körning i stället kan du anropa metoden följande innan Engagement agentinitieringen:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Du kan ange namnet på den MPNS push-kanalen för ditt program. Som standard skapar Engagement ett namn baserat på appId. Du behöver inte ange namnet själv, utom om du planerar att använda push-kanal utanför Engagement.
> 
> 

### <a name="engagement-initialization"></a>Initieringen av engagement
Ändra den `App.xaml.cs`:

* Lägg till din `using` instruktioner:
  
      using Microsoft.Azure.Engagement;
* Infoga `EngagementReach.Instance.Init` precis efter `EngagementAgent.Instance.Init` i `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Infoga `EngagementReach.Instance.OnActivated` i den `Application_Activated` metoden:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> Den `EngagementReach.Instance.Init` körs i en dedikerad tråd. Du behöver inte göra det själv.
> 
> 

## <a name="app-store-submission-considerations"></a>App store skicka överväganden
Microsoft medför vissa regler när du använder push-meddelanden:

Från Microsoft [användningsprinciper] dokumentation, avsnittet 2.9:

1) Du måste be användaren att acceptera för att ta emot push-meddelanden. Lägg sedan till ett sätt att inaktivera push-meddelanden i dina inställningar.

Objektet EngagementReach erbjuder två metoder för att hantera den opt-i eller gå ur, `EnableNativePush()` och `DisableNativePush()`. Du kan till exempel skapa ett alternativ i inställningarna med en växlingsknapp för att inaktivera eller aktivera MPNS.

Du kan också välja att inaktivera MPNS genom konfigurationen av Engagement\<windows phone-sdk-reach-konfiguration\>.

> 2.9.1) programmet måste först beskriva meddelanden ska tillhandahållas och **hämta användarens uttryckligt tillstånd (opt-in)**, och **måste ange en mekanism via vilken användaren kan välja bort tar emot push meddelanden**. Alla meddelanden som tillhandahålls med Microsoft Push Notification Service måste stämma överens med den beskrivning som angetts för användaren och måste följa alla tillämpliga [användningsprinciper] [ Content Policies] och [Ytterligare krav för specifika programtyper].
> 
> 

2) Du bör inte använda för många push-meddelanden. Engagement kommer att hantera meddelanden du.

> 2.9.2) programmet och dess användning av Microsoft Push Notification Service måste inte överdrivet använder nätverkskapaciteten eller bandbredden för Microsoft Push Notification Service, eller på annat sätt otillbörligt belastningen en Windows Phone-eller andra Microsoft-enhet eller tjänst med långa push-meddelanden, enligt Microsofts gottfinnande, och måste inte skada eller störa någon Microsoft-nätverk eller servrar eller servrar från tredje part eller nätverk som är anslutna till tjänsten Microsoft Push Notification Service.
> 
> 

3) Förlita dig inte på MPNS skickar criticals information. Engagement använder MPNS, så den här regeln gäller också för de kampanjer som skapats i hur engagerade frontend.

> 2.9.3) i Microsoft Push Notification Service kanske inte används för att skicka meddelanden som är verksamhetskritiska kritiska eller på annat sätt kan påverka frågor för liv eller död, inklusive utan begränsning viktiga meddelanden relaterade till en medicinska enhet eller villkor. MICROSOFT UTTRYCKLIGEN FRISKRIVER SIG HÄRMED FRÅN ALLA GARANTIER ATT ANVÄNDNINGEN AV MICROSOFT PUSH NOTIFICATION SERVICE ELLER LEVERANS AV MICROSOFT PUSH NOTIFICATION SERVICE MEDDELANDEN KOMMER ATT AVBROTT, FELFRI, ELLER PÅ ANNAT SÄTT GARANTERAS SÅ ATT DET SKER EN I REALTID.
> 
> 

**Vi kan inte garantera att programmet skickar valideringsprocessen om du inte tar hänsyn till dessa rekommendationer.**

## <a name="handle-data-push-optional"></a>Hantera data-push (valfritt)
Om du vill att programmet ska kunna ta emot Reach data push-meddelanden måste du implementera två händelser i klassen EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Du kan se att återanropet för varje metod returnerar ett booleskt värde. Engagement skickar feedback till dess backend när data-push. Om återanropet returnerar värdet false, den `exit` feedback kommer att skicka. Annars blir `action`. Om ingen motringning är inställd för händelser, den `drop` feedback återgår till Engagement.

> [!WARNING]
> Engagement går inte att ta emot multiplar feedback för en data-push. Om du vill ange flera hanterare på en händelse, Tänk på att din feedback motsvarar den senast skickade en. Vi rekommenderar i det här fallet returnerar alltid samma värde för att undvika att förvirrande feedback på klientdelen.
> 
> 

## <a name="customize-ui-optional"></a>Anpassa Användargränssnittet (valfritt)
### <a name="first-step"></a>Första steget
Vi kan du anpassa räckvidden Användargränssnittet.

Om du vill göra det, måste du skapa en underklass till den `EngagementReachHandler` klass.

**Exempelkod:**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Ställ sedan innehållet i den `EngagementReach.Instance.Handler` med din anpassade objekt i din `App.xaml.cs` klassen inom den `Application_Launching` metoden.

**Exempelkod:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Som standard använder Engagement implementeringen av `EngagementReachHandler`. Du behöver skapa en egen och om du gör det kan du inte ska åsidosätta alla metoder. Standardinställningen är att välja Engagement basobjektet.
> 
> 

### <a name="layouts"></a>Layouter
Som standard använder Reach inbäddade resurser för DLL-filen för att visa meddelanden och sidor.

Du kan dock välja att använda egna resurser för att återspegla ditt varumärke för dessa komponenter.

Du kan åsidosätta `EngagementReachHandler` metoder i din underklass ska berätta för Engagement att använda en layout:

**Exempelkod:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> Den `CreateNotification` metoden kan returnera null. Meddelandet visas inte och kommer att släppas reach-kampanj.
> 
> 

För att förenkla implementeringen layout, erbjuder vi även våra egna xaml som kan fungera som bas för din kod. De finns i arkivet Engagement SDK (/ src/reach /).

> [!WARNING]
> Källor som vi tillhandahåller är exakt samma de som vi använder. Så om du vill ändra dem direkt Glöm inte att ändra namnområdet och namn.
> 
> 

### <a name="notification-position"></a>Meddelande-position
Som standard visas ett meddelande i appen längst ned till vänster sida av programmet. Du kan ändra detta genom att åsidosätta den `GetNotificationPosition` metod för den `EngagementReachHandler` objekt.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

För närvarande kan du välja mellan de `BOTTOM` (standard) och `TOP` positioner.

### <a name="launch-message"></a>Starta meddelande
När en användare klickar på en Systemmeddelande (ett popup-meddelande), Engagement startar appen, läsa in innehållet i push-meddelanden och visa sidan för motsvarande kampanjen.

Det finns en fördröjning mellan starten av programmet och visas på sidan (beroende på nätverkets hastighet).

Om du vill visa användaren att något läses in, bör du ange en visuell information, t.ex. en förloppsindikator eller en förloppsindikator. Engagement kan inte hantera sig själv, men innehåller några hanterare för dig.

Om du vill implementera återanropet gör du:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Du kan ange återanropet i din `Application_Launching` metod för din `App.xaml.cs` fil, helst innan det `EngagementReach.Instance.Init()` anropa.

> [!TIP]
> Varje hanterare anropas av UI-tråden. Du behöver inte oroa dig när du använder en MessageBox eller något UI-relaterade.
> 
> 

[användningsprinciper]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Ytterligare krav för specifika programtyper]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

