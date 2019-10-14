---
title: Azure Web Application-brandvägg – vanliga frågor och svar
description: Den här sidan innehåller svar på vanliga frågor om Azure-tjänsten för front dörr
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f6e0ea7ca5b9b131a8d7d7c2c6546130a7d020b3
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302841"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Vanliga frågor och svar om Azure Web Application-brandvägg

I den här artikeln besvaras vanliga frågor om funktioner och funktioner i Azure Web Application Firewall (WAF). 

## <a name="what-is-azure-waf"></a>Vad är Azure WAF?

Azure WAF är en brand vägg för webbaserade program som hjälper till att skydda dina webb program mot vanliga hot som SQL-inmatning, skript körning över flera webbplatser och andra webbutnyttjande. Du kan definiera en WAF-princip som består av en kombination av anpassade och hanterade regler för att kontrol lera åtkomsten till dina webb program.

En Azure WAF-princip kan tillämpas på webb program som finns på Application Gateway eller Azures frontend-tjänster.

## <a name="what-is-waf-for-azure-front-door-service"></a>Vad är WAF för Azures frontend-tjänst? 

Azures front dörr är ett mycket skalbart, globalt distribuerat program och Content Delivery Network. När Azure-WAF är integrerat med Azures frontend stoppas tjänsten för DOS-attacker och mål program vid gränsen i Azure-nätverket. Det här skyddet sker nära källan till attackerna innan de har möjlighet att ange ditt vitrual-nätverk. WAF för Azures frontend-tjänst ger skydd utan att offra prestanda.

## <a name="does-azure-waf-support-https"></a>Stöder Azure WAF HTTPS?

Frontend-tjänsten erbjuder SSL-avlastning. WAF är inbyggt integrerat med en front dörr och kan inspektera en begäran när den har dekrypterats.

## <a name="does-azure-waf-support-ipv6"></a>Stöder Azure WAF IPv6?

Ja. Du kan konfigurera IP-begränsning för IPv4 och IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hur aktuella är de hanterade regel uppsättningarna?

Vi gör våra bästa för att hålla oss på att ändra hotets landskap. När en ny regel har uppdaterats läggs den till i standard regel uppsättningen med ett nytt versions nummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Vad är spridnings tiden om jag gör en ändring i WAF-principen?

Att distribuera en WAF-princip globalt tar vanligt vis cirka 5 minuter och slutförs ofta tidigare.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kan WAF-principer vara olika för olika regioner?

När det är integrerat med frontend-tjänsten är WAF en global resurs. Samma konfiguration gäller för alla platser i front dörren.
 

## <a name="which-azure-waf-options-should-i-choose"></a>Vilka alternativ för Azure-WAF ska jag välja?

Det finns två alternativ när du tillämpar WAF-principer i Azure. WAF med Azures frontend är en globalt distribuerad lösning för Edge-säkerhet. WAF med Application Gateway är en regional, dedikerad lösning. Vi rekommenderar att du väljer en lösning som baseras på dina övergripande prestanda-och säkerhets krav. Mer information finns i [belastnings utjämning med Azures program leverans Suite](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Stöder du samma WAF-funktioner på alla integrerade plattformar?

För närvarande stöds endast ModSec BOKNINGs-2.2.9 och datoriserade bokning 3,0-regler med WAF på Application Gateway. Hastighets begränsning, geo-filtrering och Azure Managed standard regler för regel uppsättning stöds bara med WAF på Azures front dörr.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Är DDoS-skydd integrerat med front dörren? 

I globalt distribuerat i Azures nätverks gränser kan Azures front dörr absorbera och geografiskt isolera stora volym attacker. Du kan skapa en anpassad WAF-princip för automatisk blockering och hastighets begränsning av http (s)-attacker som har kända signaturer. Fler fler kan du aktivera DDoS Protection standard på det virtuella nätverk där dina Server delar distribueras. Azure DDoS Protection standard kunder får ytterligare fördelar, inklusive kostnads skydd, SLA-garanti och åtkomst till experter från DDoS Rapid Response-teamet för omedelbar hjälp vid ett angrepp. 

Vi rekommenderar att du låser upp dina Server delar i produktions miljön för att minska risken för DDoS-attacker. Se [Hur gör jag för att Lås upp åtkomsten till min server del till endast Azures front dörr?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure WebApplication-brandväggen](waf-overview.md).
- Lär dig mer om [Azures front dörr](front-door-overview.md).
