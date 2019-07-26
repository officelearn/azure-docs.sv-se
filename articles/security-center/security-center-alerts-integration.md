---
title: Integrering av Azures säkerhets produkter i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras Azure-säkerhetsprodukter som har integrerats med Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 847748d1c56221119d8f74a2aee716ee08448e28
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335812"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integrering av Azures säkerhets produkter i Azure Security Center

Security Center erbjuder kunder ytterligare Microsoft-licenser för att publicera sina resultat för att Security Center och visa dem på ett konsoliderat sätt.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen (DATORISERAt) 3,0 eller 2.2.9 från det öppna webb programmet säkerhets projekt (OWASP). WAF uppdateras automatiskt för att skydda mot nya sårbarheter, utan ytterligare konfiguration som behövs. Aviseringar som genereras av WAF strömmas till Security Center. Mer information om de aviseringar som genererats av WAF finns i den här [artikeln](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

DDoS-attacker (distributed denial of Service) är kända för att vara lätta att köra. Därför har de blivit ett bra säkerhets problem för kunder som flyttar sina program till molnet. 

En DDoS-attack syftar till att göra slut på ett programs resurser, så att programmet blir otillgängligt för behöriga användare. DDoS-attacker kan rikta in sig på alla slut punkter som kan nås via Internet.

Azure DDoS Protection, kombinerat med bästa praxis för program design, ger ett försvar mot DDoS-attacker. Azure DDoS Protection tillhandahåller olika tjänst nivåer. Mer information finns i [Azure DDoS Protection översikt](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection standard kan minimera följande typer av attacker:

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**En volym attack upptäcktes**|Det här angrepps målet är att överbelasta nätverks lagret med en stor mängd som verkar vara legitim trafik. Den innehåller UDP-översvämmare, förstärknings belastningar och andra översvämmade paket belastningar. DDoS Protection standard minskar risken för angrepp med flera gigabyte genom att absorbera och rensa dem, med Azures globala nätverks skalning automatiskt.|
|**Protokoll attack identifierad**|Dessa attacker återger ett mål som inte kan nås genom att utnyttja en svaghet i skikt 3-och lager 4-protokollstacken. Den innehåller, SYN översvämnings attacker, reflektions attacker och andra protokoll attacker. DDoS Protection standard minimerar dessa attacker, skiljer sig från skadlig och legitim trafik, genom att interagera med klienten och blockera skadlig trafik.|
|**Resurs (program) lager attack har identifierats**|Dessa angrepp riktar sig mot webb program paket, för att avbryta överföring av data mellan värdar. Vid angrepp ingår HTTP-protokollfel, SQL-inmatning, skript körning över flera webbplatser och andra nivå 7-attacker. Använd brand väggen för webbaserade Azure Application Gateway, med DDoS Protection standard, för att skydda dig mot dessa attacker. Det finns även brand Väggs erbjudanden från tredje part som är tillgängliga på Azure Marketplace.|
