---
title: Nyheter i Azure Application Gateway
description: Lär dig vad som är nytt med Azure Application Gateway, till exempel de senaste versionsanteckningarna, kända problem, buggfixar, inaktuella funktioner och kommande ändringar.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 1cfa2ab1d8bf096b1821f7473d9808801b47c547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371169"
---
# <a name="whats-new-in-azure-application-gateway"></a>Vad är nytt i Azure Application Gateway?

Azure Application Gateway uppdateras löpande. Om du vill hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner

## <a name="new-features"></a>Nya funktioner

|Funktion  |Beskrivning  |Datum tillagt  |
|---------|---------|---------|
| Användardefinierade rutter (UDR) på v2 (förhandsgranskning) |Användardefinierade vägar stöds nu i vissa scenarier på Application Gateway v2 SKU:er. Mer information finns i [konfigurationsöversikt för Programgateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Mars 2020 |
|Affinitetscookie ändras |När cookie-baserad tillhörighet är aktiverad, använder Application Gateway en annan identisk cookie som kallas *ApplicationGatewayAffinityCORS* utöver den befintliga ApplicationGatewayAffinity cookien. *ApplicationGatewayAffinityCORS* har ytterligare två attribut tillagda i den (*SameSite=None; Säker*) så att klibbiga sessioner underhålls även för begäranden med flera ursprung. Mer information [finns i Application Gateway Cookie-baserad tillhörighet.](configuration-overview.md#cookie-based-affinity) |Februari 2020 |
|Avsökningsförbättringar |Med anpassade avsökningsförbättringar i Application Gateway v2 SKU har vi förenklad [avsökningskonfiguration,](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)underlättat [hälsotester för serverd-serverning på begäran](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) och lagt till [mer diagnostikinformation](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) som hjälper dig att felsöka problem med serverda.  |Oktober 2019 |
|Fler mätvärden |Vi har lagt till följande nya mått som hjälper dig att övervaka din Application Gateway v2 SKU: [Timing-relaterade mått,](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)Serverd svarsstatus, mottagna byte, antal byte, skickade klient-TLS-protokoll och aktuella beräkningsenheter. Se [Mått som stöds av Application Gateway V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augusti 2019 |
|Anpassade WAF-regler |Application Gateway WAF_v2 stöder nu att skapa anpassade regler. Se [anpassade regler för Programgateway](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisk skalning, zonredundans, statisk VIP-stöd GA |Allmän tillgänglighet för v2 SKU, som stöder automatisk skalning, zonredundans, förbättra prestanda, statiska VIP-adresser, Nyckelvalv, Huvudredigerar. Se [dokumentation för automatisk skalning av Application Gateway](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Integrering av Nyckelvalv |Application Gateway stöder nu integrering med Key Vault (i offentlig förhandsversion) för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Se [SSL-avslutning med Key Vault-certifikat](key-vault-certs.md). |April 2019 |
|Crud/skriva om rubrik     |Du kan nu skriva om HTTP-huvuden. Se [självstudiekurs: Skapa en programgateway och skriv om HTTP-huvuden](tutorial-http-header-rewrite-powershell.md) för mer information.|December 2018|
|WAF-konfigurations- och undantagslista     |Vi har lagt till fler alternativ som hjälper dig att konfigurera din WAF och minska falska positiva identifieringar. Mer information finns i [storleksbegränsningar och undantagslistor för webbprogram.](application-gateway-waf-configuration.md)|December 2018|
|Automatisk skalning, zonredundans, statisk VIP-stöd      |Med v2 SKU finns det många förbättringar som automatisk skalning, förbättrad prestanda med mera. Se [Vad är Azure Application Gateway?](overview.md)|September 2018|
|Anslutningstömning     |Anslutningsdränning gör att du kan ta bort medlemmar från backendpoolerna. Mer information finns i [Anslutningsdränning](features.md#connection-draining).|September 2018|
|Anpassade felsidor     |Med anpassade felsidor kan du skapa en felsida i formatet för resten av dina webbplatser. Aktivera detta finns i [Skapa anpassade felsidor för programgateway](custom-error.md).|September 2018|
|Förbättringar av mätvärden     |Du kan få en bättre bild av tillståndet för din Application Gateway med förbättrade mått. Om du vill aktivera mått på programgatewayen finns i [Backend-hälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Application Gateway finns i [Vad är Azure Application Gateway?](overview.md)
