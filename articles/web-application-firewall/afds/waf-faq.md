---
title: Brandvägg för Azure Web Application – Vanliga frågor och svar
description: Den här artikeln innehåller svar på vanliga frågor om brandvägg för webbprogram på Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460644"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Vanliga frågor och svar om Azure Web Application-brandväggen i Azure Front Door Service

Den här artikeln besvarar vanliga frågor om WAF-funktioner (Azure Web Application Firewall) . 

## <a name="what-is-azure-waf"></a>Vad är Azure WAF?

Azure WAF är en brandvägg för webbprogram som hjälper till att skydda dina webbprogram från vanliga hot som SQL-insprutning, skript över flera webbplatser och andra webbexploater. Du kan definiera en WAF-princip som består av en kombination av anpassade och hanterade regler för att styra åtkomsten till dina webbprogram.

En Azure WAF-princip kan tillämpas på webbprogram som finns på Application Gateway eller Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>Vad är WAF på Azure Ytterdörr? 

Azure Front Door är ett mycket skalbart, globalt distribuerat nätverk för program- och innehållsleverans. Azure WAF, när den är integrerad med Ytterdörren, stoppar denial-of-service och riktade programattacker på Azure-nätverkskanten, nära attackkällor innan de kommer in i ditt virtuella nätverk, ger skydd utan att offra prestanda.

## <a name="does-azure-waf-support-https"></a>Stöder Azure WAF HTTPS?

Ytterdörren erbjuder SSL avlastning. WAF är inbyggt integrerat med ytterdörren och kan inspektera en begäran efter att den har dekrypterats.

## <a name="does-azure-waf-support-ipv6"></a>Stöder Azure WAF IPv6?

Ja. Du kan konfigurera IP-begränsning för IPv4 och IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hur aktuella är de hanterade regeluppsättningarna?

Vi gör vårt bästa för att hänga med i det föränderliga hotlandskapet. När en ny regel har uppdaterats läggs den till i standardregeluppsättningen med ett nytt versionsnummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Vad är spridningstiden om jag gör en ändring av min WAF-policy?

Distribuera en WAF-princip globalt tar vanligtvis cirka 5 minuter och slutförs ofta tidigare.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kan WAF-politik vara olika för olika regioner?

När den är integrerad med ytterdörren är WAF en global resurs. Samma konfiguration gäller på alla platser i ytterdörren.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hur begränsar jag åtkomsten till min back-end till att endast komma från ytterdörren?

Du kan konfigurera IP Access Control List i backend för att endast tillåta utgående IP-adressintervall för ytterdörren och neka all direkt åtkomst från Internet. Tjänsttaggar stöds för att du ska kunna använda i det virtuella nätverket. Dessutom kan du kontrollera att http-huvudfältet X-Forwarded-Host är giltigt för webbprogrammet.

## <a name="which-azure-waf-options-should-i-choose"></a>Vilka Azure WAF-alternativ ska jag välja?

Det finns två alternativ när du använder WAF-principer i Azure. WAF med Azure Front Door är en globalt distribuerad, edge-säkerhetslösning. WAF med Application Gateway är en regional, dedikerad lösning. Vi rekommenderar att du väljer en lösning baserat på dina övergripande prestanda- och säkerhetskrav. Mer information finns i [Belastningsutjämning med Azures programsaldopaket](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Stöder du samma WAF-funktioner i alla integrerade plattformar?

För närvarande stöds ModSec CRS 2.2.9,CRS 3.0 och CRS 3.1-reglerna endast med WAF på Application Gateway. Hastighetsbegränsande, geofiltrering och Azure-hanterade standardregeluppsättningsregler stöds endast med WAF på Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Är DDoS-skydd integrerat med ytterdörren? 

Azure Front Door distribueras globalt i Azure-nätverkskanter och kan absorbera och isolera stora volymattacker geografiskt. Du kan skapa en anpassad WAF-princip för att automatiskt blockera och begränsa http-attacker som har kända signaturer. Dessutom kan du aktivera DDoS Protection Standard på det virtuella nätverket där dina back-ends distribueras. Azure DDoS Protection Standard-kunder får ytterligare fördelar, inklusive kostnadsskydd, SLA-garanti och åtkomst till experter från DDoS Rapid Response Team för omedelbar hjälp under en attack.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Varför skickas ytterligare begäranden över tröskelvärdet som konfigurerats för min hastighetsgränsregel till min servergrupp?

En hastighetsbegränsningsregel kan begränsa onormalt hög trafik från valfri klient-IP-adress. Du kan konfigurera ett tröskelvärde för antalet webbbegäranden som tillåts från en klient-IP-adress under en en minuts eller fem minuters varaktighet. För detaljerad hastighetskontroll kan hastighetsbegränsning kombineras med ytterligare matchningsvillkor som HTTP(S)-parametermatchning. 

Förfrågningar från samma klient kommer ofta till samma frontdörrserver. I så fall ser du ytterligare begäranden ovanför tröskelvärdet blockeras omedelbart. 

Det är dock möjligt att begäranden från samma klient kan komma fram till en annan frontdörrsserver som inte har uppdaterat räknaren för hastighetsgräns ännu. Klienten kan till exempel öppna en ny anslutning för varje begäran och tröskelvärdet är lågt. I det här fallet skulle den första begäran till den nya frontdörrservern passera hastighetsbegränsningskontrollen. En tröskelvärde för hastighetsbegränsning ställs vanligtvis högt för att skydda mot dos-attacker från alla klient-IP-adresser. För en mycket låg tröskel kan du se ytterligare begäranden ovanför tröskelvärdet komma igenom.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Web Application Firewall](../overview.md).
- Läs mer om [Azure Front Door](../../frontdoor/front-door-overview.md).
