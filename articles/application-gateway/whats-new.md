---
title: Vad är nytt i Azure Application Gateway
description: Lär dig mer om vad som är nytt med Azure Application Gateway, till exempel senaste versions information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: dd6ec2057ede076511b567c013fc1b6fa12d8281
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669740"
---
# <a name="whats-new-in-azure-application-gateway"></a>Vad är nytt i Azure Application Gateway?

Azure Application Gateway uppdateras regelbundet. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner

## <a name="new-features"></a>Nya funktioner

|Funktion  |Description  |Tillagt den  |
|---------|---------|---------|
| Application Gateway ingångs kontroll (AGIC) AKS-tillägg (för hands version) |Application Gateway ingress-kontrollanten kan nu distribueras som ett inbyggt AKS-tillägg på en rad via Azure CLI. Att vara ett AKS-tillägg gör det möjligt för AGIC att bli en fullständigt hanterad tjänst, samtidigt som den fortfarande körs i kundens AKS-kluster. Mer information finns i [AGIC-tilläggs skillnader](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |2020 juni |
| Användardefinierade vägar (UDR) på v2 (för hands version) |Användardefinierade vägar stöds nu i vissa scenarier på Application Gateway v2-SKU: er. Mer information finns i [Application Gateway konfigurations översikt](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Mars 2020 |
|Tillhörighets-cookie-ändringar |När cookie-baserad tillhörighet är aktive rad, infogar Application Gateway en annan identisk cookie med namnet *ApplicationGatewayAffinityCORS* förutom den befintliga ApplicationGatewayAffinity-cookien. *ApplicationGatewayAffinityCORS* har två fler attribut som läggs till i den (*SameSite = none; Säker*) så att dina tröga sessioner upprätthålls även för frågor över andra ursprung. Mer information finns i [Application Gateway cookie-baserad tillhörighet](configuration-overview.md#cookie-based-affinity) . |Februari 2020 |
|Avsöknings förbättringar |Med de anpassade avsöknings förbättringarna i Application Gateway v2 SKU: n, har vi förenklad [avsöknings konfiguration](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), under lätta [Server dels hälsotest på begäran](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) och ytterligare [diagnostikinformation](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) som hjälper dig att felsöka Server dels hälso problem.  |Oktober 2019 |
|Fler mått |Vi har lagt till följande nya mått som hjälper dig att övervaka din Application Gateway v2-SKU: [tidsrelaterade mått](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), status för Server del svar, mottagna byte, skickade byte, client TLS-protokoll och aktuella beräknings enheter. Se [mått som stöds av Application Gateway v2 SKU: n](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augusti 2019 |
|Anpassade WAF-regler |Application Gateway WAF_v2 har nu stöd för att skapa anpassade regler. Se [Application Gateway anpassade regler](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisk skalning, zon redundans, statisk VIP-support GA |Allmän tillgänglighet för v2-SKU: n, som stöder automatisk skalning, zon redundans, bättre prestanda, statisk VIP, Key Vault, överskrivning av huvuden. Se [Application Gateway autoskalning-dokumentationen](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Key Vault-integrering |Application Gateway stöder nu integrering med Key Vault (i offentlig för hands version) för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Se [TLS-avslutning med Key Vault certifikat](key-vault-certs.md). |April 2019 |
|Rubrik CRUD/omskrivning     |Du kan nu skriva om HTTP-huvuden. Se [Självstudier: skapa en Application Gateway och skriv om HTTP-huvuden](tutorial-http-header-rewrite-powershell.md) för mer information.|December 2018|
|WAF-konfiguration och exkluderings lista     |Vi har lagt till fler alternativ som hjälper dig att konfigurera din WAF och minska antalet falska positiva identifieringar. Mer information finns i [begäran om storleks gränser och undantags listor för WebApplication-brandvägg](application-gateway-waf-configuration.md).|December 2018|
|Automatisk skalning, zon redundans, statisk VIP-support      |Med v2-SKU: n finns det många förbättringar som automatisk skalning, bättre prestanda och mycket annat. Se [Vad är Azure Application Gateway?](overview.md) för mer information.|September 2018|
|Anslutningstömning     |Med anslutnings tömning kan du på ett smidigt sätt ta bort medlemmar från dina backend-pooler. Mer information finns i [anslutnings tömning](features.md#connection-draining).|September 2018|
|Anpassade felsidor     |Med anpassade felsidor kan du skapa en felsida i formatet för resten av dina webbplatser. Information om hur du aktiverar detta finns i [skapa Application Gateway anpassade fel sidor](custom-error.md).|September 2018|
|Mått förbättringar     |Du kan få en bättre översikt över statusen för din Application Gateway med förbättrade mått. Om du vill aktivera mått på Application Gateway, se [backend-hälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Application Gateway finns i [Vad är Azure Application Gateway?](overview.md)
