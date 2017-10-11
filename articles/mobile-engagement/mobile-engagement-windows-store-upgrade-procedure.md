---
title: "Uppgradera procedurer för universella Windows-appar SDK"
description: "Universella Windows-appar SDK uppgradera procedurer för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fe85a99a92fb39082cafe7422b356de1f20f14bd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Uppgradera procedurer för universella Windows-appar SDK
Om du redan har integrerat en äldre version av Engagement till programmet, måste du Tänk på följande när du uppgraderar SDK.

Du kan behöva utföra flera procedurer om du har missat flera versioner av SDK. Till exempel om du migrerar från 0.10.1 till 0.11.0 måste du först Följ proceduren ”från 0.9.0 till 0.10.1” sedan proceduren ”från 0.10.1 till 0.11.0”.

## <a name="from-330-to-340"></a>Från 3.3.0 till 3.4.0
### <a name="test-logs"></a>Testa loggar
Konsolen loggar som genereras av SDK kan nu vara aktiverat/inaktiverat/filtreras. Anpassa detta genom att uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till ett värde som är tillgängliga från den `EngagementTestLogLevel` uppräkning, till exempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Resurser
Reach-överlägget har förbättrats. Det är en del av resurserna som SDK NuGet-paketet.

När du uppgraderar till den nya versionen av SDK kan du välja om du vill behålla dina befintliga filer från mappen överlägget för dina resurser eller inte:

* Om det tidigare överlägget fungerar för dig eller om du integrerar de `WebView` element manuellt sedan kan du välja att behålla din befintliga filer, den fortfarande fungerar. 
* Om du vill uppdatera till det nya överlägget bara ersätta hela `overlay` mapp från dina resurser med den nya från SDK-paketet (UWP-appar: efter uppgraderingen måste du hämta mappen överlägget från % USERPROFILE %\\.nuget\packages\ MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Med det nya överlägget skrivs alla anpassningar som gjorts på den tidigare versionen.
> 
> 

## <a name="from-320-to-330"></a>Från 3.2.0 till 3.3.0
### <a name="resources"></a>Resurser
Det här steget gäller anpassade resurser. Om du har anpassat de resurser som tillhandahålls av SDK (html, bilder, överlägget) ha säkerhetskopiera dem innan du uppgraderar och tillämpa anpassningen på uppgraderade resurser.

## <a name="from-310-to-320"></a>Från 3.1.0 till 3.2.0
### <a name="resources"></a>Resurser
Det här steget gäller anpassade resurser. Om du har anpassat de resurser som tillhandahålls av SDK (html, bilder, överlägget) ha säkerhetskopiera dem innan du uppgraderar och tillämpa anpassningen på uppgraderade resurser.

### <a name="webview-integration"></a>Webbvy integrering
Vissa förbättringar så att den matchar en annan enhet formfaktorer introducerades i den här versionen. Se till att din integrering av webbvyn matchar följande:

I din XAML sida ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Och tillhörande .cs-fil:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>Från 2.0.0 till 3.0.0
### <a name="resources"></a>Resurser
Det här steget gäller anpassade resurser. Om du har anpassat de resurser som tillhandahålls av SDK (html, bilder, överlägget) ha säkerhetskopiera dem innan du uppgraderar och tillämpa anpassningen på uppgraderade resurser.

## <a name="from-111-to-200"></a>Från 1.1.1 till 2.0.0
Nedan beskrivs hur du migrerar en SDK-integration från tjänsten Capptain som erbjuds av Capptain SAS i en app med Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain och Mobile Engagement är inte samma tjänster och proceduren nedan visar hur du migrerar klientappen endast. Migrera SDK i appen kommer inte migrera dina data från servrar som Capptain till Mobile Engagement-servrar
> 
> 

Kontakta Capptain webbplats om du vill migrera till 1.1.1 först och sedan använda följande procedur om du migrerar från en tidigare version

### <a name="nuget-package"></a>Nuget-paketet
Ersätt **Capptain.WindowsPhone** av **MicrosoftAzure.MobileEngagement** Nuget-paketet.

### <a name="applying-mobile-engagement"></a>Tillämpa Mobile Engagement
SDK använder termen `Engagement`. Du behöver uppdatera ditt projekt för att matcha den här ändringen.

Du måste avinstallera det aktuella Capptain nuget-paketet. Överväg att alla dina ändringar i mappen Capptain resurser tas bort. Om du vill behålla dessa filer och sedan göra en kopia av dem.

Efter det att installera nya Microsoft Azure Engagement nuget-paketet på ditt projekt. Du hittar den direkt på [nuget webbplats]. eller här index. Den här åtgärden ersätter alla filer för resurser som används av Engagement och lägger till nya Engagement DLL projektreferenserna.

Du måste rensa projektreferenserna genom att ta bort Capptain DLL-referenser. Om du inte göra detta, versionen av Capptain kommer i konflikt och fel inträffar.

Om du har anpassat Capptain resurser, kopiera ditt gamla filer innehåll och klistra in dem i de nya Engagement-filerna. Observera att xaml- och cs-filer som behöver uppdateras.

När de steg behöver du bara ersätta gamla Capptain referenser från de nya Engagement-referenserna.

1. Alla Capptain namnområden måste uppdateras.
   
    Före migrering:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Efter migreringen:
   
        using Microsoft.Azure.Engagement;
2. Alla Capptain klasser som innehåller ”Capptain” ska innehålla ”Engagement”.
   
    Före migrering:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Efter migreringen:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. För xaml-filer Capptain namnområde och attribut även ändras.
   
    Före migrering:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Efter migreringen:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Överlägget sidan ändringar
   
   > [!IMPORTANT]
   > Överlägget ändras. Det nya namnområdet är `Microsoft.Azure.Engagement.Overlay`. Den måste användas i xaml- och cs-filer. Dessutom `CapptainGrid` är att namnges `EngagementGrid`, `capptain_notification_content` och `capptain_announcement_content` namnges `engagement_notification_content` och `engagement_announcement_content`.
   > 
   > 
   
    För överlägget:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Blir den:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Andra resurser som Capptain bilder och HTML-filer, Observera att de även har döpts om du vill använda ”Engagement”.

### <a name="project-declaration"></a>Deklarationen för projektet
På Package.appxmanifest `File Type Associations` har uppdaterats från:

* capptain\_nå\_innehåll engagement\_nå\_innehåll
* capptain\_loggen\_filen engagement\_loggen\_fil

### <a name="application-id--sdk-key"></a>Program-ID / SDK-nyckeln
Engagement använder en anslutningssträng. Du inte behöver ange ett program-ID och SDK-nyckeln med Mobile Engagement, behöver du bara ange en anslutningssträng. Du kan konfigurera det på EngagementConfiguration-fil.

Konfigurationen av Engagement kan anges i din `Resources\EngagementConfiguration.xml` -filen för ditt projekt.

Redigera den här filen för att ange:

* Anslutningssträngen program mellan taggarna `<connectionString>` och `<\connectionString>`.

Om du vill ange vid körning i stället kan du anropa metoden följande innan Engagement agentinitieringen:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

Anslutningssträngen för ditt program visas på den klassiska Azure-portalen.

### <a name="items-name-change"></a>Ändring av objekt
Alla objekt med namnet *capptain* namngetts *engagement*. På liknande sätt för *Capptain* till *Engagement*.

Exempel på vanliga Capptain objekt:

* CapptainConfiguration nu namnet EngagementConfiguration
* CapptainAgent nu namnet EngagementAgent
* CapptainReach nu namnet EngagementReach
* CapptainHttpConfig nu namnet EngagementHttpConfig
* GetCapptainPageName nu namnet GetEngagementPageName

Observera att byta namn på också påverkar åsidosätts metoder.

