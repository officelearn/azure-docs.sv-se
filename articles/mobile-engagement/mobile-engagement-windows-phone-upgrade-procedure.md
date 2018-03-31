---
title: Windows Phone Silverlight SDK Uppgraderingsprocesser
description: Windows Phone Silverlight SDK Uppgraderingsprocesser för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 489b005c37ddb842a2501e89c07fb34b091346e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK Uppgraderingsprocesser
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Om du redan har integrerat en äldre version av våra SDK i programmet, måste du Tänk på följande när du uppgraderar SDK.

Du kan behöva utföra flera procedurer om du har missat flera versioner av SDK. Till exempel om du migrerar från 0.10.1 till 0.11.0 måste du först Följ proceduren ”från 0.9.0 till 0.10.1” sedan proceduren ”från 0.10.1 till 0.11.0”.

## <a name="from-200-to-330"></a>Från 2.0.0 till 3.3.0
### <a name="test-logs"></a>Testa loggar
Konsolen loggar som genereras av SDK kan nu vara aktiverat/inaktiverat/filtreras. Anpassa detta genom att uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till ett värde som är tillgängliga från den `EngagementTestLogLevel` uppräkning, till exempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

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

Efter det att installera nya Microsoft Azure Engagement nuget-paketet på ditt projekt. Du kan hitta direkt på [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Den här åtgärden ersätter alla filer för resurser som används av Engagement och lägger till nya Engagement DLL projektreferenserna.

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
4. Andra resurser som Capptain bilder, Observera att de även har döpts om du vill använda ”Engagement”.

### <a name="application-id--sdk-key"></a>Program-ID / SDK-nyckeln
Engagement använder en anslutningssträng. Du inte behöver ange ett program-ID och SDK-nyckeln med Mobile Engagement, behöver du bara ange en anslutningssträng. Du kan konfigurera det på EngagementConfiguration-fil.

Konfigurationen av Engagement kan anges i din `Resources\EngagementConfiguration.xml` -filen för ditt projekt.

Redigera den här filen för att ange:

* Anslutningssträngen program mellan taggarna `<connectionString>` och `<\connectionString>`.

Om du vill ange vid körning i stället kan du anropa metoden följande innan Engagement agentinitieringen:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

Anslutningssträngen för ditt program visas i Azure Portal.

### <a name="items-name-change"></a>Ändring av objekt
Alla objekt med namnet *capptain* namngetts *engagement*. På liknande sätt för *Capptain* till *Engagement*.

Exempel på vanliga Capptain objekt:

* CapptainConfiguration nu namnet EngagementConfiguration
* CapptainAgent nu namnet EngagementAgent
* CapptainReach nu namnet EngagementReach
* CapptainHttpConfig nu namnet EngagementHttpConfig
* GetCapptainPageName now named GetEngagementPageName

Observera att byta namn på också påverkar åsidosätts metoder.

