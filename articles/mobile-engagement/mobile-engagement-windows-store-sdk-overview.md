---
title: Azure Mobile Engagement Windows Universal SDK Integration | Microsoft Docs
description: Windows Universal-integrering för SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: 8c513d5b05afaee4c49d0d2e96a0dcda78761421
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Windows Universal SDK-Integration för Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Det här dokumentet beskriver alla integrering och konfiguration tillgängliga alternativ för Azure Mobile Engagement Windows Universal SDK.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du först slutföra vår [15 minuter kursen](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Avancerade funktioner
### <a name="reporting-features"></a>Funktioner för rapportering
Du kan lägga till dessa funktioner:

1. [Avancerade alternativ för rapportering](mobile-engagement-windows-store-advanced-reporting.md)
2. [Avancerade konfigurationsalternativ](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Meddelanden
[Hur du integrerar räckvidden (meddelanden) i din universella Windows-app](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Taggen planera implementering:
[Hur du använder avancerade Mobile Engagement API-märkning i din universella Windows-app](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Viktig information
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Stabilitetsförbättringar.

Tidigare versioner finns i [slutföra viktig information](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Uppgraderingsprocesser
Om du redan har integrerat en äldre version av Engagement till programmet, måste du Tänk på följande när du uppgraderar SDK.

Om du har missat flera versioner av SDK kanske att följa flera procedurer. Se hela [uppgradera procedurer](mobile-engagement-windows-store-upgrade-procedure.md). Till exempel om du migrerar från 0.10.1 till 0.11.0 måste du först Följ proceduren ”från 0.9.0 till 0.10.1” sedan proceduren ”från 0.10.1 till 0.11.0”.

### <a name="from-330-to-340"></a>Från 3.3.0 till 3.4.0
#### <a name="test-logs"></a>Testa loggar
Konsolen loggar som genereras av SDK kan nu vara aktiverat/inaktiverat/filtreras. Om du vill anpassa, uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till någon av värdena som är tillgängliga från den `EngagementTestLogLevel` uppräkning, exempelvis:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Resurser
Reach-överlägget har förbättrats. Det är en del av resurserna som SDK NuGet-paketet.

Du kan välja om du vill behålla dina befintliga filer från mappen överlägget för dina resurser eller inte när du uppgraderar till den nya versionen av SDK:

* Om det tidigare överlägget fungerar för dig, eller du integrerar de `WebView` element manuellt, sedan kan du välja att behålla din avslutas filer, den kommer fortfarande att fungera.
* Om du vill uppdatera till det nya överlägget ersätta hela `overlay` mapp från dina resurser med den nya från SDK-paketet (UWP-appar: efter uppgraderingen måste du hämta mappen överlägget från % USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Med det nya överlägget skriver över alla anpassningar som gjorts på den tidigare versionen.
> 
> 

### <a name="upgrade-from-older-versions"></a>Uppgradera från tidigare versioner
Se [uppgradera procedurer](mobile-engagement-windows-store-upgrade-procedure.md)

