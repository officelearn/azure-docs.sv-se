---
title: Översikt över Azure Mobile Engagement Web SDK | Microsoft Docs
description: De senaste uppdateringarna och procedurer för webbtjänst-SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: ad0a83e05c6a60953c2f8a0036f823e7ed497f45
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile Engagement Web SDK
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Börja här detaljerad information om hur du integrerar Azure Mobile Engagement i en webbapp. Om du vill prova för att komma igång med din egen webbprogram, finns våra [15 minuter kursen](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Integration procedurer
1. Läs [hur du integrerar Mobile Engagement i ditt webbprogram](mobile-engagement-web-integrate-engagement.md).
2. Taggen planera implementeringen Läs [hur du använder avancerade Mobile Engagement API-märkning i webbappen](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Viktig information
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* Fast krascher om privata bläddring (Safari).
* Fast krascher i webbläsare med cookies inaktiverade.

Alla versioner, finns det [slutföra viktig information](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Uppgraderingsprocesser
### <a name="upgrade-from-121-to-200"></a>Uppgradera från 1.2.1 till 2.0.0
I följande avsnitt beskrivs hur du migrerar en Mobile Engagement Web SDK-integration från tjänsten Capptain som erbjuds av Capptain SAS för en Azure Mobile Engagement-app. Om du migrerar från en version tidigare än 1.2.1 Kontakta webbplatsen Capptain om du vill migrera till 1.2.1 först och sedan använda följande procedurer.

Den här versionen av Mobile Engagement Web SDK stöder inte Samsung Smart TV, Opera TV, webOS eller Reach-funktionen.

> [!IMPORTANT]
> Capptain och Azure Mobile Engagement är inte samma tjänst och följande Markera endast hur du migrerar klientappen. Migrera Mobile Engagement Web SDK i appen kommer inte migrera dina data från en Capptain server till en Mobile Engagement-server.
> 
> 

#### <a name="javascript-files"></a>JavaScript-filer
Ersätt filen capptain-sdk.js med filen azure-engagement.js och uppdatera din skript importerar på samma sätt.

#### <a name="remove-capptain-reach"></a>Ta bort Capptain Reach
Den här versionen av Mobile Engagement Web SDK stöder inte Reach-funktionen. Om du har integrerat Capptain Reach i ditt program, måste du ta bort den.

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

#### <a name="remove-deprecated-apis"></a>Ta bort föråldrade API: er
Vissa API: er från Capptain är föråldrade i Mobile Engagement Web SDK.

Ta bort alla anrop till följande API: er: `agent.connect`, `agent.disconnect`, `agent.pause`, och `agent.sendMessageToDevice`.

Ta bort någon av följande återanrop från konfigurationen Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, och `onPushMessageReceived`.

#### <a name="configuration"></a>Konfiguration
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

Anslutningssträngen för ditt program visas i Azure-portalen.

#### <a name="javascript-apis"></a>JavaScript API: er
Objektet global JavaScript `window.capptain` har bytt `window.azureEngagement`, men du kan använda den `window.engagement` alias för API-anrop. Du kan inte använda detta alias för att definiera SDK-konfiguration.

Till exempel `capptain.deviceId` blir `engagement.deviceId`, `capptain.agent.startActivity` blir `engagement.agent.startActivity`och så vidare.

Om en tidigare version av Azure Mobile Engagement Web SDK har redan integrerats i programmet, Läs om [uppgradera procedurer](mobile-engagement-web-upgrade-procedure.md).

