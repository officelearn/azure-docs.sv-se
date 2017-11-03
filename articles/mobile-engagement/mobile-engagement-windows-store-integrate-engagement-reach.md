---
title: "Universella Windows-appar nå SDK-Integration"
description: Integrera Azure Mobile Engagement Reach med universella Windows-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Universella Windows-appar nå SDK-Integration
Du måste följa proceduren integrering i den [Windows Universal SDK-Integration av Engagement](mobile-engagement-windows-store-integrate-engagement.md) innan du följer den här guiden.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Bädda in Engagement nå SDK i ditt universella Windows-projekt
Du har inte något att lägga till. `EngagementReach`referenser och resurser finns redan i projektet.

> [!TIP]
> Du kan anpassa avbildningar som finns i den `Resources` mappen i ditt projekt, speciellt märke ikonen (som standard till ikonen Engagement). På universella appar du kan också flytta den `Resources` mapp på din delade projektet att dela innehåll mellan appar, men du måste behålla den `Resources\EngagementConfiguration.xml` fil på dess ursprungliga plats eftersom den är beroende plattform.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Aktivera Windows Notification Service
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x och Windows Phone 8.1
För att kunna använda den **Windows Notification Service** (kallas WNS) i din `Package.appxmanifest` filen på `Application UI` klickar du på `All Image Assets` i rutan vänstra bot. Längst till höger om rutan i `Notifications`, ändra `toast capable` från `(not set)` till `(Yes)`.

### <a name="all-platforms"></a>Alla plattformar
Du behöver synkronisera din app till ditt Microsoft-konto och engagement-plattformen. För detta måste du skapa ett konto eller logga in [windows dev center](https://dev.windows.com). Efter det att skapa ett nytt program och hitta SID och hemlig nyckel. På engagement-klientdel går på din appinställningen i `native push` och klistra in dina autentiseringsuppgifter. Efter det, högerklickar du på projektet, Välj `store` och `Associate App with the Store...`. Du behöver bara markera programmet som du har skapar innan för att synkronisera den.

## <a name="initialize-the-engagement-reach-sdk"></a>Initiera Engagement Reach SDK
Ändra den `App.xaml.cs`:

* Infoga `EngagementReach.Instance.Init` precis efter `EngagementAgent.Instance.Init` i din `InitEngagement` metoden:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  Den `EngagementReach.Instance.Init` körs i en dedikerad tråd. Du behöver inte göra det själv.

> [!NOTE]
> Om du använder push-meddelanden någon annanstans i ditt program så att du behöver [delar push-kanal](#push-channel-sharing) med Engagement nå.
> 
> 

## <a name="integration"></a>Integrering
Engagement tillhandahåller två sätt att lägga till Reach i appen sidhuvud och mellan enskilda muskler vyer för meddelanden och avsökningar i ditt program: överläggsintegration och den manuella integreringen av vyer. Du bör inte kombinera båda metod på samma sida.

Välja mellan två integrationen kan sammanfattas det här sättet:

* Du kan välja överläggsintegration om sidorna ärver redan från agenten `EngagementPage`, är det bara en fråga för att ersätta `EngagementPage` av `EngagementPageOverlay` och `xmlns:engagement="using:Microsoft.Azure.Engagement"` av `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` på sidorna.
* Du kan välja Webbvyer manuell integration om du vill att exakt nå Användargränssnittet i sidorna eller om du inte vill lägga till en annan nivå på sidorna. 

### <a name="overlay-integration"></a>Överläggsintegration
Engagement överlägget dynamiskt lägger till de UI-element som används för att visa Reach-kampanjer på sidan. Om överlägget inte passar din layout bör du Webbvyer manuell integrering i stället.

I XAML-filen ändringen `EngagementPage` hänvisar till`EngagementPageOverlay`

* Lägg till följande i namnområdesdeklarationerna:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Ersätt `engagement:EngagementPage` med `engagement:EngagementPageOverlay`:

**Med EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Med EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Tagga sedan sidan i i filen .cs `EngagementPageOverlay` i stället för `EngagementPage` och importera `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Ersätt `EngagementPage` med `EngagementPageOverlay`:

**Med EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Med EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Engagement överlägget lägger till en `Grid` element på sidan består av layouten och två `WebView` delar en för banderollen och det andra för mellan enskilda muskler vyn.

Du kan anpassa överlägget element i direkt i den `EngagementPageOverlay.cs` filen.

### <a name="web-views-manual-integration"></a>Web vyer manuell integrering
Reach söker efter sidorna för två `WebView` element som ansvarar för att visa popup-meddelandet och mellan enskilda muskler vyn. Det enda du behöver göra är att lägga till de två `WebView` element någonstans i sidorna, här är ett exempel:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


I det här exemplet i `WebView` element utsträckt så att de passar deras behållare som automatiskt storleken dem vid skärmen rotation fönster eller storlek ändras.

> [!WARNING]
> Det är viktigt att behålla samma naming `engagement_notification_content` och `engagement_announcement_content` för den `WebView` element. Reach identifierar dem med deras namn. 
> 
> 

## <a name="handle-datapush-optional"></a>Hantera datapush (valfritt)
Om du vill att programmet ska kunna ta emot Reach data push-meddelanden måste du implementera två händelser i klassen EngagementReach:

Lägg till i App.xaml.cs i konstruktorn App():

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

Ställ sedan innehållet i den `EngagementReach.Instance.Handler` med din anpassade objekt i din `App.xaml.cs` klassen inom den `App()` metoden.

**Exempelkod:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Som standard använder Engagement implementeringen av `EngagementReachHandler`.
> Du behöver skapa en egen och om du gör det kan du inte ska åsidosätta alla metoder. Standardinställningen är att välja Engagement basobjektet.
> 
> 

### <a name="web-view"></a>Webbvy
Som standard använder Reach inbäddade resurser för DLL-filen för att visa meddelanden och sidor.

Om du vill ange en fullständig anpassning möjligheter använda vi bara webbvy. Om du vill anpassa layouter åsidosätta direkt resurser filerna `EngagementAnnouncement.html` och `EngagementNotification.html`. Engagement måste all kod i `<body></body>` ska kunna köras korrekt. Men du kan lägga till tagg yttre `engagement_webview_area`.

Du kan dock välja att använda egna resurser.

Du kan åsidosätta `EngagementReachHandler` metoder i din underklass ska berätta för Engagement att använda en layout, men var noga med att inbäddade mekanismen för engagement:

**Exempelkod:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Som standard är AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Den representerar html-fil som utforma innehållet i ett push-meddelande (Text meddelandet, Web anoucement och avsökning meddelande). AnnouncementName är `engagement_announcement_content`. Det är namnet på webbvy designen i xaml-sida.

NotfificationHTML är `ms-appx-web:///Resources/EngagementNotification.html`. Den representerar html-fil som utforma meddelandet för push-meddelande. NotfificationName är `engagement_notification_content`. Det är namnet på webbvy designen i xaml-sida.

### <a name="customization"></a>Anpassning
Du kan anpassa meddelandet och meddelandet webbvy har du vill om du bevara Engagement objekt. Var försiktig så att webbvy objekt beskrivs tre gånger - första gången i din xaml, andra gången i filen .cs i metoden ”setwebview()” och tredje tid i html-fil.

* Beskriv den aktuella grafiska layout webbvy komponenten i din xaml.
* I filen .cs kan du definiera ”setwebview()” där du anger dimension två webbvy (meddelande, meddelande). Det är mycket effektivt när storleksändring av programmet.
* Html-fil Engagement beskrivs webbvyinnehåll, design och element positioner mellan varandra.

### <a name="launch-message"></a>Starta meddelande
När en användare klickar på en Systemmeddelande (ett popup-meddelande), Engagement startar programmet, läsa in innehållet i push-meddelanden och visa sidan för motsvarande kampanjen.

Det finns en fördröjning mellan starten av programmet och visas på sidan (beroende på nätverkets hastighet).

Om du vill visa användaren att något läses in, bör du ange en visuell information, t.ex. en förloppsindikator eller en förloppsindikator. Engagement kan inte hantera sig själv, men innehåller några hanterare för dig.

Om du vill implementera återanropet App.xaml.cs i ”gemensamma App() {}” Lägg till:

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

Du kan ange återanropet i ”gemensamma App() {}”-metoden i din `App.xaml.cs` fil, helst innan det `EngagementReach.Instance.Init()` anropa.

> [!TIP]
> Varje hanterare anropas av UI-tråden. Du behöver inte oroa dig när du använder en MessageBox eller något UI-relaterade.
> 
> 

## <a id="push-channel-sharing"></a>Push-kanal delning
Om du använder push-meddelanden för ett annat ändamål i ditt program har att använda push-kanal funktionen av Engagement SDK för delning. Detta är att undvika missade push.

* Du kan ange egna push-kanal till Engagement nå initieringen. SDK: N används den i stället för att begära en ny.

Uppdatera initiering av Engagement nå med din push-kanal i den `InitEngagement` metod från den `App.xaml.cs` filen:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Om du vill använda push-kanalen när Reach-initieringen och du kan ange ett återanrop för Engagement kommer att hämta push-kanalen en gång skapats som också av SDK.

Ange din återanrop var som helst **när** Reach-initieringen:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Anpassat schema-tips
Vi ger användning av anpassade schemat. Du kan skicka annan typ av URI från klientdel av engagement som ska användas i din engagement-programmet. Standardschema som `http, ftp, ...` är hantera med Windows, kommer ett fönster fråga om de är inget installerat standardprogram på enheten. Du kan också skapa egna anpassade schemat för ditt program.

Enkelt sätt att ange ett eget schema i ditt program är att öppna din `Package.appxmanifest` gå i `Declarations` panelen. Välj `Protocol` Bläddra rutan i tillgängliga deklarationer och lägga till den. Redigera den `Name` fält med din nya protokollet önskat namn.

Nu om du vill använda det här protokollet redigera din `App.xaml.cs` med den `OnActivated` metoden och Glöm inte att initiera engagement här också:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

