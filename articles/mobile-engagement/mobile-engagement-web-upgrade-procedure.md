---
title: Azure Mobile Engagement Web SDK uppgraderingsprocesser | Microsoft Docs
description: "De senaste uppdateringarna och procedurer för webbtjänst-SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure Mobile Engagement Web SDK uppgraderingsprocesser
Om du redan har integrerat en tidigare version av Azure Mobile Engagement Web SDK i ditt webbprogram som du behöver Tänk på följande när du uppgraderar SDK.

Om du hoppade över flera versioner av Mobile Engagement Web SDK, kanske du måste utföra flera procedurer under uppgraderingen. Till exempel om du migrerar från 1.4.0 till 1.6.0 först följer du procedurerna för att uppgradera från 1.4.0 till 1.5.0. Följ procedurerna för att uppgradera från 1.5.0 till 1.6.0.

Oavsett vilken version som du uppgraderar från, ersätta en tidigare version av filen azure-engagement.js med den senaste versionen av filen.

## <a name="upgrade-from-121-to-200"></a>Uppgradera från 1.2.1 till 2.0.0
Det här avsnittet beskrivs hur du migrerar en Mobile Engagement Web SDK-integration från tjänsten Capptain som erbjuds av Capptain SAS för en Azure Mobile Engagement-app. Om du migrerar från en tidigare version, kontakta webbplatsen Capptain om du vill migrera först till 1.2.1 och tillämpa sedan följande procedurer.

Den här versionen av Mobile Engagement Web SDK stöder inte Samsung Smart TV, Opera TV, webOS eller Reach-funktionen.

> [!IMPORTANT]
> Capptain och Azure Mobile Engagement är inte samma tjänst. Följande procedur visar endast hur du migrerar klientappen. Migrera Mobile Engagement Web SDK i appen kommer inte migrera dina data från en Capptain server till en Mobile Engagement-server.
> 
> 

### <a name="javascript-files"></a>JavaScript-filer
Ersätt filen capptain-sdk.js med filen azure-engagement.js och uppdatera din skript importerar på samma sätt.

### <a name="remove-capptain-reach"></a>Ta bort Capptain Reach
Den här versionen av Mobile Engagement Web SDK stöder inte Reach-funktionen. Om du har integrerat Capptain Reach i ditt program måste du ta bort den.

Ta bort nå CSS-import från din sida och ta bort relaterade CSS-filen (capptain-reach.css, som standard).

Ta bort följande Reach-resurser: Stäng avbildningen (capptain-close.png, som standard) och ikonen varumärken (capptain-meddelande-ikonen, som standard).

Ta bort nå Gränssnittet för aviseringar via appen. Standard ser ut så här:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Ta bort nå Gränssnittet för text och web meddelanden och avsökningar. Standard ser ut så här:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Ta bort den `reach` objekt från konfigurationen, om den finns. Det ser ut så här:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Ta bort alla andra Reach anpassning, till exempel.

### <a name="remove-deprecated-apis"></a>Ta bort föråldrade API: er
Vissa API: er från Capptain är föråldrade i Mobile Engagement Web SDK.

Ta bort alla anrop till följande API: er: `agent.connect`, `agent.disconnect`, `agent.pause`, och `agent.sendMessageToDevice`.

Ta bort alla instanser av följande återanrop från konfigurationen Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, och `onPushMessageReceived`.

### <a name="configuration"></a>Konfiguration
Mobile Engagement använder en anslutningssträng för att konfigurera SDK identifierare, t.ex, program-ID.

Ersätt det program-ID med anslutningssträngen. Observera att det globala objektet för SDK-konfiguration ändras från `capptain` till `azureEngagement`.

Före migrering:

    window.capptain = {
      appId: ...,
      [...]
    };

Efter migreringen:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

Anslutningssträngen för ditt program visas i Azure Portal.

### <a name="javascript-apis"></a>JavaScript API: er
Objektet global JavaScript `window.capptain` har bytt `window.azureEngagement` men du kan använda den `window.engagement` alias för API-anrop. Du kan inte använda aliaset för att definiera SDK-konfiguration.

Till exempel `capptain.deviceId` blir `engagement.deviceId`, `capptain.agent.startActivity` blir `engagement.agent.startActivity`och så vidare.

