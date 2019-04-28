---
title: Azure waf – vanliga frågor och svar
description: Den här sidan innehåller svar på vanliga frågor och svar om Azure ytterdörren Service
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461977"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Vanliga frågor för Azure web application firewall

Den här artikeln innehåller vanliga frågor och svar om Azure web application firewall (WAF) och funktioner. 

## <a name="what-is-azure-waf"></a>Vad är Azure WAF?

Azure WAF är en brandvägg för webbaserade program som hjälper dig skydda dina webbprogram mot vanliga hot som SQL injection, cross site scripting och andra sårbarheter på webben. Du kan definiera en WAF-princip som består av en kombination av anpassade och hanterade regler för att styra åtkomsten till dina webbprogram.

En Azure-WAF-princip kan tillämpas på webbprogram på Application Gateway eller ytterdörren för Azure-tjänster.

## <a name="what-is-waf-for-azure-front-door-service"></a>Vad är WAF for Azure ytterdörren Service? 

Azure ytterdörren är en mycket skalbar, globalt distribuerad och nätverk för innehållsleverans. Azure WAF, när du har integrerat med ytterdörren, slutar denial of service och riktade attacker program på Azure-nätverkskanten, nära attack källor innan de kommer in ditt virtuella nätverk ger skydd utan att offra prestanda.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Hur kommer jag att debiteras för Azure WAF för åtkomsten?
Allmänt tillgängliga förhandsversionen WAF vid ytterdörren är kostnadsfritt. Observera att ytterdörren kostnad är extra. Se priser för ytterdörren [här](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Stöder Azure WAF HTTPS?

Ytterdörren Service erbjuder SSL-avlastning. WAF är inbyggt med ytterdörren och kan gå igenom en begäran när dekrypteras.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF som har stöd för IPv6?

Ja. Du kan konfigurera IP-begränsningar för IPv4 och IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hur uppdaterad är hanterade regeluppsättning?

Vi gör vårt bästa för att hålla jämna steg med att ändra hotlandskapet. När en ny regel har uppdaterats, läggs den till standard regeluppsättning med ett nytt versionsnummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Vad är spridning tiden om jag gör en ändring i min WAF-principen?

Distribuera en princip för WAF globalt vanligtvis tar cirka 5 minuter och slutför ofta snabbare.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kan WAF principer vara olika för olika regioner?

När du har integrerat med ytterdörren tjänsten är WAF en global resurs. Samma konfiguration gäller för alla ytterdörren platser.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hur jag för att begränsa åtkomst till min serverdel komma från ytterdörren endast?

Du kan konfigurera IP-åtkomstkontrollistan i din serverdel för endast ytterdörren utgående IP-adressintervall och neka alla direkt åtkomst från Internet. Tjänsttaggar stöds som du kan använda i ditt virtuella nätverk. Dessutom kan du kontrollera att fältet X-vidarebefordrade-värd HTTP-huvud är giltig för ditt webbprogram.




## <a name="which-azure-waf-options-should-i-choose"></a>Vilka Azure-WAF-alternativ ska jag välja?

Det finns två alternativ när du använder WAF-principer i Azure. WAF med Azure ytterdörren är en globalt distribuerad, edge-säkerhetslösning. WAF med Application Gateway är en regional, dedikerad lösning. Vi rekommenderar att du väljer en lösning utifrån din övergripande prestanda och säkerhetskrav. Mer information finns i [belastningsutjämning med Azure application delivery suite](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Det stöd för samma WAF-funktioner i alla integrerade plattformar?

För närvarande stöds ModSec CRS 2.2.9 och CRS 3.0 regler endast med WAF vid Programgatewayen. Hastighetsbegränsande geo-filtrering och Azure hanterade standard regeluppsättning regler stöds endast med WAF på Azure ytterdörren.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS protection är integrerat med åtkomsten? 

Globalt distribuerad i Azure-nätverk kanter, Azure ytterdörren absorbera och geografiskt isolera stort attacker. Du kan skapa anpassade WAF-principen som automatiskt block och pris gränsen http (s)-attacker som har visat signaturer. Ytterligare mer, kan du aktivera DDoS Protection Standard på det virtuella nätverket där din-servrar har distribuerats. Azure DDoS Protection Standard-kunder får ytterligare fördelar, till exempel kostnaden skydd, SLA-garantin och tillgång till experter från DDoS snabb Svarsgrupp för omedelbar hjälp under ett angrepp. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure waf](waf-overview.md).
- Läs mer om [Azure ytterdörren](front-door-overview.md).
