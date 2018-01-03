---
title: Windows Universal avancerade rapportering med MobileApps Engagement
description: Integrera Azure Mobile Engagement med universella Windows-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Avancerade rapportering med Windows Universal-appar Engagement SDK
> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Det här avsnittet beskrivs ytterligare rapporteringsscenarier i ditt universella Windows-program. Dessa scenarier innehåller alternativ som du kan välja att tillämpa på appen som skapats i den [komma igång](mobile-engagement-windows-store-dotnet-get-started.md) kursen.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Innan du påbörjar de här självstudierna måste du först slutföra den [komma igång](mobile-engagement-windows-store-dotnet-get-started.md) självstudier, vilket är avsiktligt direkt och enkel. Den här kursen ingår ytterligare alternativ som du kan välja bland.

## <a name="specifying-engagement-configuration-at-runtime"></a>Ange engagement konfiguration vid körning
Konfigurationen av Engagement centraliserad i den `Resources\EngagementConfiguration.xml` filen i projektet, vilket är där det har angetts i den [komma igång](mobile-engagement-windows-store-dotnet-get-started.md) avsnittet.

Men du kan också ange det vid körning: du kan anropa metoden följande innan Engagement agentinitieringen:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Rekommenderad metod: överlagra din `Page` klasser
Om du vill aktivera rapportering av alla loggar som krävs av Engagement att beräkna användare, sessioner, aktiviteter, krascher och tekniska statistik, se alla dina `Page` underordnade klasser ärver från den `EngagementPage` klasser.

Här är ett exempel på en sida av ditt program. Du kan göra samma sak för alla sidor i ditt program.

### <a name="c-source-file"></a>C# källfilen
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
> Om sidan åsidosätter metoden `OnNavigatedTo` ska du anropa `base.OnNavigatedTo(e)`. Aktiviteten är annars inte rapporteras (den `EngagementPage` anrop `StartActivity` i dess `OnNavigatedTo` metod).
> 
> 

### <a name="xaml-file"></a>XAML-fil
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

### <a name="override-the-default-behaviour"></a>Åsidosätta beteenden som standard
Klassnamnet för sidan har rapporterats som aktivitetsnamn med utan extra som standard. Om klassen använder suffixet ”Page”, Engagement tar bort den.

Om du vill åsidosätta standardbeteendet för namnet, lägger du till den här koden:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Rapportera extra information med dina aktiviteter, lägga till den här koden:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

De här metoderna anropas inifrån den `OnNavigatedTo` metoden på sidan.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativ metod: anropa `StartActivity()` manuellt
Om du inte kan eller inte vill överlagra din `Page` klasser, i stället kan du starta dina aktiviteter genom att anropa `EngagementAgent` metoder direkt.

Vi rekommenderar att anropa `StartActivity` i din `OnNavigatedTo` metod på sidan.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Se till att du kan avsluta sessionen på rätt sätt.
> 
> Windows Universal SDK automatiskt anropar den `EndActivity` metoden när programmet stängs. Därför är det **hög** rekommenderas att anropa den `StartActivity` metod när aktiviteten användaren ändrar och **aldrig** anropa den `EndActivity` metoden. Den här metoden meddelar Engagement-servern att den aktuella användaren har lämnat programmet, vilket påverkar alla programloggar.
> 
> 

## <a name="advanced-reporting"></a>Avancerad rapportering
Alternativt kan du kanske vill rapportera programspecifika händelser, fel och jobb för att göra det, kan du använda andra metoder hittades i den `EngagementAgent` klass. Engagement API kan användningen av alla Engagement avancerade funktioner.

Mer information finns i [hur du använder avancerade Mobile Engagement API-märkning i appen Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

