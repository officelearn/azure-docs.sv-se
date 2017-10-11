---
title: Windows Universal-appar Engagement SDK-Integration
description: Integrera Azure Mobile Engagement med universella Windows-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 898160814304fa8ec65622056a77ca9d4caf2c99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows Universal-appar Engagement SDK-Integration
> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Den här proceduren beskriver det enklaste sättet att aktivera Engagement analyser och övervakningsfunktionerna i ditt universella Windows-program.

Följande steg är tillräckligt för att aktivera rapporten i loggar som behövs för att beräkna all statistik om användare, sessioner, aktiviteter, krascher och Technicals. Rapporten loggar som behövs för att beräkna andra statistik som händelser, fel och jobb måste göras manuellt med hjälp av Engagement-API (se [hur du använder avancerade Mobile Engagement API-märkning i appen Windows Universal](mobile-engagement-windows-store-use-engagement-api.md) eftersom dessa statistik är programberoende.

## <a name="supported-versions"></a>Versioner som stöds
Mobile Engagement SDK för Windows universella appar kan endast integreras i Windows Runtime och Uwp-tillämpningar för:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (familjer stationär dator och mobil)

> [!NOTE]
> Om du utvecklar för Windows Phone Silverlight sedan referera till den [Windows Phone Silverlight-integrering proceduren](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installera Mobile Engagement-SDK för universella appar
### <a name="all-platforms"></a>Alla plattformar
Mobile Engagement SDK för Windows Universal appen är tillgänglig som ett Nuget-paket som kallas *MicrosoftAzure.MobileEngagement*. Du kan installera den från Visual Studio Nuget Package Manager.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x och Windows Phone 8.1
NuGet automatiskt distribuerar SDK-resurser i den `Resources` mapp i roten av projektet program.

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 Universal Windows Platform program
NuGet distribuerar inte automatiskt SDK-resurser i UWP-appen ännu. Du behöver göra det manuellt tills resursdistribution sätts in i NuGet:

1. Öppna i Utforskaren.
2. Navigera till följande plats (**x.x.x** är version av Engagement som du installerar): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\*  *x.x.x**\\content\win81*
3. Dra och släpp den **resurser** mappen från Utforskaren till roten för ditt projekt i Visual Studio.
4. Välj ditt projekt i Visual Studio och aktivera den **visa alla filer** ikonen ovanpå det **Solution Explorer**.
5. Vissa filer inte längre ingår i projektet. Importera dem på samma gång högerklickar du på den **resurser** mappen **undantas från projektet** och sedan en annan högerklickar du på den **resurser** mappen **inkludera i projektet** till nytt omfattar hela mappen. Alla filer från den **resurser** mappen ingår nu i ditt projekt.

## <a name="add-the-capabilities"></a>Lägg till funktioner
Engagement-SDK måste vissa funktioner i Windows SDK för att fungera korrekt.

Öppna din `Package.appxmanifest` filen och se till att följande funktioner har deklarerats:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Initiera Engagement SDK
### <a name="engagement-configuration"></a>Engagement konfiguration
Konfigurationen av Engagement centraliserad i den `Resources\EngagementConfiguration.xml` filen i projektet.

Redigera den här filen för att ange:

* Anslutningssträngen program mellan taggarna `<connectionString>` och `<\connectionString>`.

Om du vill ange vid körning i stället kan du anropa metoden följande innan Engagement agentinitieringen:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Anslutningssträngen för ditt program visas på den klassiska Azure-portalen.

### <a name="engagement-initialization"></a>Initieringen av engagement
När du skapar ett nytt projekt, en `App.xaml.cs` -filen har genererats. Den här klassen som ärver från `Application` och innehåller många viktiga metoder. Den också används för att initiera Engagement SDK.

Ändra den `App.xaml.cs`:

* Lägg till din `using` instruktioner:
  
      using Microsoft.Azure.Engagement;
* Definiera en metod för att dela Engagement initieringen en gång för alla samtal:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Anropa `InitEngagement` i den `OnLaunched` metoden:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* När programmet startas med ett anpassat schema, ett annat program eller kommandoraden sedan `OnActivated` metoden anropas. Du måste också starta Engagement SDK när appen har aktiverats. Det gör du genom att åsidosätta `OnActivated` metoden:
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Vi avråder dig för att lägga till initiering av Engagement på en annan plats för ditt program.
> 
> 

## <a name="basic-reporting"></a>Grundläggande reporting
### <a name="recommended-method-overload-your-page-classes"></a>Rekommenderad metod: överlagra din `Page` klasser
För att aktivera rapporten i alla loggar som krävs av Engagement att beräkna användare, sessioner, aktiviteter, krascher och tekniska statistik, kan du bara göra alla dina `Page` underordnade klasser ärver från den `EngagementPage` klasser.

Här är ett exempel på hur du gör detta för en sida i ditt program. Du kan göra samma sak för alla sidor i ditt program.

#### <a name="c-source-file"></a>C# källfilen
Ändra sidan `.xaml.cs` fil:

* Lägg till din `using` instruktioner:
  
      using Microsoft.Azure.Engagement;
* Ersätt `Page` med `EngagementPage`:

**Utan Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Med Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!IMPORTANT]
> Om sidan åsidosätter metoden `OnNavigatedTo` ska du anropa `base.OnNavigatedTo(e)`. Annars rapporteras inte aktiviteten (`EngagementPage` anropar `StartActivity` i tillhörande `OnNavigatedTo`-metod).
> 
> 

#### <a name="xaml-file"></a>XAML-fil
Ändra sidan `.xaml` fil:

* Lägg till följande i namnområdesdeklarationerna:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Ersätt `Page` med `engagement:EngagementPage`:

**Utan Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Med Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Åsidosätta beteenden som standard
Klassnamnet för sidan har rapporterats som aktivitetsnamn med utan extra som standard. Om klassen använder suffixet ”Page”, tas Engagement också bort.

Om du vill åsidosätta standard beteendet för namn, lägger du till detta i koden:

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
Om du inte kan eller inte vill överlagra din `Page` klasser, i stället kan du starta dina aktiviteter genom att anropa `EngagementAgent` metoder direkt.

Vi rekommenderar för att anropa `StartActivity` i din `OnNavigatedTo` metod på sidan.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Se till att du kan avsluta sessionen på rätt sätt.
> 
> Windows Universal SDK automatiskt anropar den `EndActivity` metoden när programmet stängs. Därför är det **hög** rekommenderas att anropa den `StartActivity` metod när aktiviteten användaren ändrar och **aldrig** anropa den `EndActivity` metoden den här metoden skickar till servern för Engagement som aktuell användare har lämnar programmet, då påverkar alla programloggar.
> 
> 

## <a name="advanced-reporting"></a>Avancerad rapportering
Du kanske vill rapporten programmet specifika händelser, fel och jobb, gör du genom använda andra metoder hittades i den `EngagementAgent` klass. Engagement API kan använda alla Engagement avancerade funktioner.

Mer information finns i [hur du använder avancerade Mobile Engagement API-märkning i appen Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

## <a name="advanced-configuration"></a>Avancerad konfiguration
### <a name="disable-automatic-crash-reporting"></a>Inaktivera automatisk rapportering om krasch
Du kan inaktivera automatisk kraschen reporting-funktionen i Engagement. Sedan, när ett ohanterat undantag inträffar, Engagement inte göra något.

> [!WARNING]
> Om du planerar att inaktivera den här funktionen måste vara medveten om att när en ohanterad krasch sker i din app Engagement inte kommer att skicka kraschen **och** kommer inte att stänga sessionen och jobb.
> 
> 

Om du vill inaktivera automatisk rapportering om krasch du bara anpassa konfigurationen beroende på hur du har deklarerats:

#### <a name="from-engagementconfigurationxml-file"></a>Från `EngagementConfiguration.xml` fil
Ange rapport-krascher till `false` mellan `<reportCrash>` och `</reportCrash>` taggar.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Från `EngagementConfiguration` objekt vid körning.
Ange rapport-krascher till false med EngagementConfiguration-objektet.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Burst-läge
Som standard loggas Engagement service-rapporter i realtid. Om ditt program rapporterar loggar väldigt ofta, är det bättre att buffra loggarna och rapportera dem samtidigt på en vanlig tiden som bas (Detta kallas ”burst-läge”).

Det gör du genom att anropa metoden:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet är ett värde i **millisekunder**. När som helst om du vill återaktivera realtid loggning bara att anropa metoden utan någon parameter eller med värdet 0.

Burst-läge något öka för batterilivslängd men påverkar Engagement-Övervakare: varaktighet för alla sessioner och jobb ska avrundas till burst tröskelvärdet (alltså sessioner och jobb som är kortare än tröskelvärdet burst inte kanske visas). Det rekommenderas att använda ett tröskelvärde i burst 30000 (30s). Du måste vara medveten om sparade loggarna är begränsade till 300 objekt. Om det är för lång tid att skicka förlora du vissa loggar.

> [!WARNING]
> Tröskelvärdet burst kan inte konfigureras för en period som är mindre än 1s. Om du försöker göra det, visas en spårning med fel SDK och återställer automatiskt till standardvärdet, dvs 0s. Detta utlöser SDK om du vill rapportera loggar i realtid.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

