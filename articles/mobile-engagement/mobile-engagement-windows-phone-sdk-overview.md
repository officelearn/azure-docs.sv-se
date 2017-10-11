---
title: "Azure Mobile Engagement Windows Phone Silverlight SDK översikt | Microsoft Docs"
description: "Översikt över Windows Phone Silverlight SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Silverlight SDK översikt för Azure Mobile Engagement
Börja här om du vill ha information om hur du kan integrera Azure Mobile Engagement i en Windows Phone Silverlight-App. Om du vill prova först kontrollera att du uppfyller våra [15 minuter att slutföra kursen](mobile-engagement-windows-phone-get-started.md).

Klicka för att visa den [SDK innehåll](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Integration procedurer
1. Börja här: [hur du integrerar Mobile Engagement i din Windows Phone Silverlight-app](mobile-engagement-windows-phone-integrate-engagement.md)
2. För aviseringar: [hur du integrerar räckvidden (meddelanden) i Windows Phone Silverlight-app](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Tagga planera implementeringen: [hur du använder avancerade Mobile Engagement API-märkning i Windows Phone Silverlight-app](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Viktig information
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
En del av den *MicrosoftAzure.MobileEngagement* Nuget-paketet **v3.4.1**

* Stabilitetsförbättringar.

Tidigare version finns i [slutföra viktig information](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Uppgraderingsprocesser
Om du redan har integrerat en äldre version av våra SDK i programmet, måste du Tänk på följande när du uppgraderar SDK.

Du kan behöva utföra flera procedurer om du har missat flera versioner av SDK. Se hela [uppgradera procedurer](mobile-engagement-windows-phone-upgrade-procedure.md). Till exempel om du migrerar från 0.10.1 till 0.11.0 måste du först Följ proceduren ”från 0.9.0 till 0.10.1” sedan proceduren ”från 0.10.1 till 0.11.0”.

### <a name="from-200-to-330"></a>Från 2.0.0 till 3.3.0
#### <a name="test-logs"></a>Testa loggar
Konsolen loggar som genereras av SDK kan nu vara aktiverat/inaktiverat/filtreras. Anpassa detta genom att uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till ett värde som är tillgängliga från den `EngagementTestLogLevel` uppräkning, till exempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Uppgradera från tidigare versioner
Se [uppgradera procedurer](mobile-engagement-windows-phone-upgrade-procedure.md)

