---
title: Vad är nytt i Azure Application Gateway
description: Lär dig mer om vad som är nytt med Azure Application Gateway, till exempel senaste versions information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 5ae7aa2dfbab5ff62f9ce56bc3a790294e369529
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686825"
---
# <a name="whats-new-in-azure-application-gateway"></a>Vad är nytt i Azure Application Gateway?

Azure Application Gateway uppdateras regelbundet. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion

## <a name="new-features"></a>Nya funktioner

|Funktion  |Beskrivning  |Tilläggsdatum  |
|---------|---------|---------|
|Fler mått |Vi har lagt till följande nya mått som hjälper dig att övervaka din program Gateway v2-SKU: [Tidsrelaterade mått](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), status för Server del svar, mottagna byte, skickade byte, klient-TLS-protokoll och aktuella beräknings enheter. Se [mått som stöds av Application Gateway v2 SKU: n](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |2019 augusti |
|Anpassade WAF-regler |Application Gateway WAF_v2 har nu stöd för att skapa anpassade regler. Se [Application Gateway anpassade regler](custom-waf-rules-overview.md). |2019 juni |
|Automatisk skalning, zon redundans, statisk VIP-support GA |Allmän tillgänglighet för v2-SKU: n som stöder autoskalning, zon redundans, bättre prestanda, statisk VIP, Key Vault, överskrivning av huvuden. Se [Application Gateway autoskalning-dokumentationen](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Key Vault-integrering |Application Gateway stöder nu integrering med Key Vault (i offentlig för hands version) för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Se [SSL-avslutning med Key Vault certifikat](key-vault-certs.md). |April 2019 |
|Rubrik CRUD/omskrivning     |Du kan nu skriva om HTTP-huvuden. Se [Självstudier: Skapa en Application Gateway och skriv om HTTP-huvudena @ no__t-0 för mer information.|December 2018|
|WAF-konfiguration och exkluderings lista     |Vi har lagt till fler alternativ som hjälper dig att konfigurera din WAF och minska antalet falska positiva identifieringar. Mer information finns i [brand väggens storleks gränser och undantags listor för webb program](application-gateway-waf-configuration.md) .|December 2018|
|Automatisk skalning, zon redundans, statisk VIP-support      |Med v2-SKU: n finns det många förbättringar som automatisk skalning, bättre prestanda och mycket annat. Se [Vad är Azure Application Gateway?](overview.md) för mer information.|September 2018|
|Anslutningstömning     |Med anslutnings tömning kan du på ett smidigt sätt ta bort medlemmar från dina backend-pooler. Mer information finns i [anslutnings tömning](overview.md#connection-draining).|September 2018|
|Anpassade felsidor     |Med anpassade felsidor kan du skapa en felsida i formatet för resten av dina webbplatser. Information om hur du aktiverar detta finns i [skapa Application Gateway anpassade fel sidor](custom-error.md).|September 2018|
|Mått förbättringar     |Du kan få en bättre översikt över statusen för din Application Gateway med förbättrade mått. Om du vill aktivera mått på Application Gateway, se [backend-hälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Application Gateway finns i [Vad är Azure Application Gateway?](overview.md)
