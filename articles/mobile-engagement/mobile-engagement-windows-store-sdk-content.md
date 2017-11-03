---
title: "Windows Universal SDK för appar innehåll"
description: "Läs mer om innehållet i Windows Universal-appar SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b28d525ab16487b963772e23fdecd11f94dcabd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-content"></a>Windows Universal SDK för appar innehåll
Det här dokumentet listar och beskriver det innehåll som distribueras av SDK i ditt program.

## <a name="the-resources-folder"></a>Den `/Resources` mapp
Den här mappen innehåller alla resurser som krävs för Mobile Engagement. Du kan också anpassa dem så att de passar din app.

* `EngagementConfiguration.xml`: Mobile Engagement konfigurationsfil, det är där du kan anpassa Mobile Engagement-inställningar (Mobile Engagement anslutningssträngen, rapporten krascher...).

### <a name="html-folder"></a>/ HTML-mappen
* `EngagementNotification.html`: `Notification` Webbdesign visa html för sidhuvud i appen.
* `EngagementAnnouncement.html`: `Announcement` Webbdesign visa html för app mellan enskilda muskler vyer.

### <a name="images-folder"></a>/ Images mapp
* `EngagementIconNotification.png`: Varumärken ikonen visas till vänster på ett meddelande ersätter den här med ditt varumärkesikon.
* `EngagementIconOk.png`: `Ok` Ikon för reach innehållssidor för knappen åtgärd eller validering.
* `EngagementIconNOK.png`: `NOK` Ikonen som används när knappen verifiering för reach innehållssidor är inaktiverad.
* `EngagementIconClose.png`: `Close` Ikonen för reach-meddelanden och innehåll för knappen Stäng.

### <a name="overlay-folder"></a>/overlay mapp
* `EngagementPageOverlay.cs`: Överlägget sidan ansvarar för att lägga till Engagement nå i appen användargränssnitt till dess underordnade.

