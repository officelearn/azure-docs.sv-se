---
title: "Avancerad konfiguration för Windows Universal-appar Engagement SDK"
description: "Avancerade konfigurationsalternativ för Azure Mobile Engagement med universella Windows-appar"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Avancerad konfiguration för Windows Universal-appar Engagement SDK
> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

Den här proceduren beskriver hur du konfigurerar olika konfigurationsalternativ för Azure Mobile Engagement Android-appar.

## <a name="prerequisites"></a>Krav
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Avancerad konfiguration
### <a name="disable-automatic-crash-reporting"></a>Inaktivera automatisk rapportering om krasch
Du kan inaktivera automatisk kraschen reporting-funktionen i Engagement. Sedan, när ett ohanterat undantag inträffar Engagement ingenting.

> [!WARNING]
> Om du inaktiverar den här funktionen, så när en ohanterad kraschar i din app Engagement inte skicka kraschen **och** inte stänga sessionen och jobb.
> 
> 

Om du vill inaktivera automatisk rapportering om krasch anpassa konfigurationen beroende på hur du har deklarerats:

#### <a name="from-engagementconfigurationxml-file"></a>Från `EngagementConfiguration.xml` fil
Ange rapport-krascher till `false` mellan `<reportCrash>` och `</reportCrash>` taggar.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Från `EngagementConfiguration` objekt vid körning.
Ange rapport-krascher till false med EngagementConfiguration-objektet.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Inaktivera rapportering i realtid
Som standard loggas Engagement service-rapporter i realtid. Om ditt program rapporterar loggar ofta, är det bättre att buffra loggarna och rapportera dem på en gång på vanlig tid. Detta kallas ”burst läge”.

Det gör du genom att anropa metoden:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argumentet är ett värde i **millisekunder**. När du vill återaktivera realtid loggning kan du anropa metoden utan någon parameter eller med värdet 0.

Burst läge något ökar för batterilivslängd men påverkar Engagement-Övervakare: alla sessioner och jobb varaktighet avrundas till burst tröskelvärdet (alltså sessioner och jobb som är kortare än tröskelvärdet burst inte kanske visas). Vi rekommenderar att du använder ett tröskelvärde i burst 30000 (30s). Sparade loggarna är begränsade till 300 objekt. Om skickar är för lång kan förlora du vissa loggar.

> [!WARNING]
> Kan inte konfigureras burst tröskelvärdet till en period som är mindre än en sekund. Om du gör det visas en spårning med fel SDK och återställer automatiskt till standardvärdet noll sekunder. Detta utlöser SDK om du vill rapportera loggar i realtid.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
