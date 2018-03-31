---
title: Windows Phone Silverlight Engagement SDK-Integration
description: Integrera Azure Mobile Engagement med Windows Phone Silverlight-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f041f19ceff366a34b1761bfdca12242f4bdf9b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone Silverlight Engagement SDK-Integration
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Den här proceduren beskriver det enklaste sättet att aktivera Azure Mobile Engagement analyser och övervakningsfunktionerna i Windows Phone Silverlight-program.

Följande steg är tillräckligt för att aktivera rapporten i loggar som behövs för att beräkna all statistik om användare, sessioner, aktiviteter, krascher och Technicals. Rapporten loggar som behövs för att beräkna andra statistik som händelser, fel och jobb måste göras manuellt med hjälp av Engagement-API (se [hur du använder avancerade Mobile Engagement API-märkning i Windows Phone Silverlight-app](mobile-engagement-windows-phone-use-engagement-api.md) nedan) eftersom statistiken är programmet beroende.

## <a name="supported-versions"></a>Versioner som stöds
Mobile Engagement-SDK för Windows Silverlight kan endast integreras i program som mål:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Om du utvecklar för Windows Phone 8.1 (utan Silverlight) avser den [universella Windows-integration proceduren](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Installera Silverlight Mobile Engagement-SDK
Mobile Engagement-SDK för Windows Silverlight är tillgänglig som ett Nuget-paket som kallas *MicrosoftAzure.MobileEngagement*. Du kan installera den från Visual Studio Nuget Package Manager. 

## <a name="add-the-capabilities"></a>Lägg till funktioner
Engagement-SDK måste vissa funktioner i Windows Phone Silverlight SDK för att fungera korrekt.

Öppna din `WMAppManifest.xml` filen och se till att följande funktioner är deklarerad i den `Capabilities` panelen:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Initiera Engagement SDK
### <a name="engagement-configuration"></a>Engagement konfiguration
Konfigurationen av Engagement centraliserad i den `Resources\EngagementConfiguration.xml` filen i projektet.

Redigera den här filen för att ange:

* Anslutningssträngen program mellan taggarna `<connectionString>` och `<\connectionString>`.

Om du vill ange vid körning i stället kan du anropa metoden följande innan Engagement agentinitieringen:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

Anslutningssträngen för ditt program visas på Azure Portal.

### <a name="engagement-initialization"></a>Initieringen av engagement
När du skapar ett nytt projekt, en `App.xaml.cs` -filen har genererats. Den här klassen som ärver från `Application` och innehåller många viktiga metoder. Den också används för att initiera Engagement SDK.

Ändra den `App.xaml.cs`:

* Lägg till din `using` instruktioner:
  
      using Microsoft.Azure.Engagement;
* Infoga `EngagementAgent.Instance.Init` i den `Application_Launching` metoden:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Infoga `EngagementAgent.Instance.OnActivated` i den `Application_Activated` metoden:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Vi avråder dig för att lägga till initiering av Engagement på en annan plats för ditt program. Men tänk som den `EngagementAgent.Instance.Init` metoden körs på en dedikerad tråd och inte i UI-tråden.
> 
> 

## <a name="basic-reporting"></a>Grundläggande reporting
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Rekommenderad metod: överlagra din `PhoneApplicationPage` klasser
För att aktivera rapporten i alla loggar som krävs av Engagement att beräkna användare, sessioner, aktiviteter, krascher och tekniska statistik, kan du bara göra alla dina `PhoneApplicationPage` underordnade klasser ärver från den `EngagementPage` klasser.

Här är ett exempel på hur du gör detta för en sida i ditt program. Du kan göra samma sak för alla sidor i ditt program.

#### <a name="c-source-file"></a>C# källfilen
Ändra sidan `.xaml.cs` fil:

* Lägg till din `using` instruktioner:
  
      using Microsoft.Azure.Engagement;
* Ersätt `PhoneApplicationPage` med `EngagementPage` :

**Utan Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Med Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Om sidan som ärver från den `OnNavigatedTo` metod, var noga med att låta den `base.OnNavigatedTo(e)` anropa. Annars rapporteras aktiviteten inte. Faktiskt den `EngagementPage` anropar `StartActivity` inuti den `OnNavigatedTo` metoden.
> 
> 

#### <a name="xaml-file"></a>XAML-fil
Ändra sidan `.xaml` fil:

* Lägg till i namnområdesdeklarationerna:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Ersätt `phone:PhoneApplicationPage` med `engagement:EngagementPage` :

**Utan Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Med Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Åsidosätta standardbeteendet
Klassnamnet för sidan har rapporterats som aktivitetsnamn med utan extra som standard. Om klassen använder suffixet ”Page”, tas Engagement också bort.

Om du vill åsidosätta standardbeteendet för namnet lägger du till detta i koden:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Om du vill rapportera vissa extra information med dina aktiviteter kan du lägga till detta koden:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

De här metoderna anropas inifrån den `OnNavigatedTo` metoden på sidan.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativ metod: anropa `StartActivity()` manuellt
Om du inte kan eller inte vill överlagra din `PhoneApplicationPage` klasser, i stället kan du starta dina aktiviteter genom att anropa `EngagementAgent` metoder direkt.

Vi rekommenderar att anropa `StartActivity` i din `OnNavigatedTo` metod för din PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Se till att du kan avsluta sessionen på rätt sätt.
> 
> SDK-anrop automatiskt den `EndActivity` metoden när programmet stängs. Därför är det **hög** rekommenderas att anropa den `StartActivity` metod när aktiviteten användaren ändrar och **aldrig** anropa den `EndActivity` metoden. Den här metoden skickar ett meddelande till Engagement-servern att den aktuella användaren har lämnat programmet detta påverkar alla programloggar.
> 
> 

## <a name="advanced-reporting"></a>Avancerad rapportering
Du kanske vill rapporten programmet specifika händelser, fel och jobb, gör du genom använda andra metoder hittades i den `EngagementAgent` klass. Engagement API kan använda alla Engagement avancerade funktioner.

Mer information finns i [hur du använder avancerade Mobile Engagement API-märkning i Windows Phone Silverlight-app](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Avancerad konfiguration
### <a name="disable-automatic-crash-reporting"></a>Inaktivera automatisk rapportering om krasch
Du kan inaktivera automatisk kraschen reporting-funktionen i Engagement. Sedan, när ett ohanterat undantag inträffar, Engagement inte göra något.

> [!WARNING]
> Om du planerar att inaktivera den här funktionen måste vara medveten om att när en ohanterad krasch sker i din app Engagement inte kommer att skicka kraschen **och** stängs inte sessionen och jobb.
> 
> 

Om du vill inaktivera automatisk rapportering om krasch du bara anpassa konfigurationen beroende på hur du har deklarerats:

#### <a name="from-engagementconfigurationxml-file"></a>Från `EngagementConfiguration.xml` fil
Ange rapport-krascher till `false` mellan `<reportCrash>` och `</reportCrash>` taggar.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Från `EngagementConfiguration` objekt vid körning.
Ange rapport-krascher till false med EngagementConfiguration-objektet.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Burst-läge
Som standard loggas Engagement service-rapporter i realtid. Om ditt program rapporterar loggar väldigt ofta, är det bättre att buffra loggarna och rapportera dem samtidigt på en vanlig tiden som bas (Detta kallas ”burst-läge”).

Det gör du genom att anropa metoden:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet är ett värde i **millisekunder**. När som helst om du vill återaktivera realtid loggning bara att anropa metoden utan någon parameter eller med värdet 0.

Burst-läge något öka för batterilivslängd men påverkar Engagement-Övervakare: varaktighet för alla sessioner och jobb ska avrundas till burst tröskelvärdet (alltså sessioner och jobb som är kortare än tröskelvärdet burst inte kanske visas). Det rekommenderas att använda ett tröskelvärde i burst 30000 (30s). Du måste vara medveten om sparade loggarna är begränsade till 300 objekt. Om det är för lång tid att skicka förlora du vissa loggar.

> [!WARNING]
> Tröskelvärdet för burst kan inte konfigureras till en period som är mindre än en sekund. Om du försöker göra så SDK visas en spårning med fel och kommer återställs automatiskt till standardvärdet, det vill säga noll sekunder. Detta utlöser SDK om du vill rapportera loggar i realtid.
> 
> 

