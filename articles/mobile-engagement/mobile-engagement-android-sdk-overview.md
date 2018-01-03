---
title: "Android SDK-Integration för Azure Mobile Engagement"
description: Beskriver hur du integrerar Azure Mobile Engagement SDK i Android-appar
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Android SDK-Integration för Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Det här dokumentet beskriver alla integrering och konfiguration tillgängliga alternativ för Azure Mobile Engagement Android SDK.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Avancerade funktioner
### <a name="reporting-features"></a>Funktioner för rapportering
Du kan lägga till dessa funktioner:

1. [Avancerade alternativ för rapportering](mobile-engagement-android-advanced-reporting.md)
2. [Alternativ för rapportering](mobile-engagement-android-location-reporting.md)
3. [Avancerade konfigurationsalternativ](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Meddelanden:
[Hur du integrerar räckvidden (meddelanden) i din Android-app](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [hur du integrerar GCM med Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [hur du integrerar ADM med Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Taggen planera implementering:
[Hur du använder avancerade Mobile Engagement API-märkning i din Android-app](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Viktig information

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Åtgärda en krasch sällan kan inträffa när du anropar `EngagementAgentUtils.isInDedicatedEngagementProcess`, som också används av den `EngagementApplication` klass.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* 8 stöd för Android (tidigare versioner av SDK inte fungerar på Android 8).
* Inget mer beroende stödbibliotek.
* Ta bort `EngagementFragmentActivity` klass.
* På grund av att [bakgrund körning gränser](https://developer.android.com/preview/features/background.html) på Android 8 loggar i bakgrunden kan fördröjas tills användaren interagerar med enheten, detta kan påverka Push-kampanj **levererade** och **Systemmeddelande visas** statistik att försenas om enheten är i viloläge (meddelandet fortfarande visas, kommer ring och vibrerar i realtid utan problem).
* På grund av att [bakgrund plats gränser](https://developer.android.com/preview/features/background-location-limits.html)verkliga tidpunkten platsen i bakgrunden inte kommer att uppdateras ofta på Android 8.

Alla versioner, finns det [slutföra viktig information](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Uppgraderingsprocesser
Om du redan har integrerat en äldre version av våra SDK i programmet, bör du konsultera [uppgradera procedurer](mobile-engagement-android-upgrade-procedure.md).

