---
title: Vad är nytt i Azure Application Gateway
description: Lär dig vad är nytt med Azure Application Gateway som föråldrade funktioner för de senaste versionsanteckningarna, kända problem, felkorrigeringar och kommande ändringar.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 053eac95a81492e79aee824b02383206c6702a84
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806690"
---
# <a name="whats-new-in-azure-application-gateway"></a>Vad är nytt i Azure Application Gateway?

Azure Application Gateway uppdateras kontinuerligt. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion

## <a name="new-features"></a>Nya funktioner

|Funktion  |Beskrivning  |Tilläggsdatum  |
|---------|---------|---------|
|Rubriken CRUD/omskrivningar     |Du kan nu arbeta om HTTP-huvuden. Se [självstudien: Skapa en Programgateway och skriv om HTTP-huvuden](tutorial-http-header-rewrite-powershell.md) för mer information.|December 2018|
|WAF-konfigurationen och undantag lista     |Vi har lagt till fler alternativ för att hjälpa dig att konfigurera din WAF och minska falska positiva identifieringar. Se [storleksgränser för Web application firewall begäran och undantagslistor](application-gateway-waf-configuration.md) för mer information.|December 2018|
|Automatisk skalning och redundans, statiska förhandsversionen av stödet för VIP     |Det finns många förbättringar, till exempel automatisk skalning och förbättrad prestanda med V2-SKU. Se [vad är Azure Application Gateway?](overview.md#autoscaling-public-preview) för mer information.|September 2018|
|Anslutningstömning     |Anslutningstömning kan du ta bort medlemmar från backend-poolen ett smidigt sätt. Mer information finns i [Anslutningstömning](overview.md#connection-draining).|September 2018|
|Anpassade felsidor     |Du kan skapa en felsida i formatet för resten av dina webbplatser med anpassade felsidor. För att möjliggöra detta, se [skapa Programgateway anpassade felsidor](custom-error.md).|September 2018|
|Förbättringar av mått     |Du kan få en bättre överblick över tillståndet för din Programgateway med förbättrad mått. För att aktivera mått på Application Gateway, se [backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Application Gateway, [vad är Azure Application Gateway?](overview.md)