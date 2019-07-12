---
title: Security Center-integrering med Azure Security-produkter | Microsoft Docs
description: Detta avsnitt visar Azure säkerhetsprodukter som har integrerats med Azure Security Center.
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
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571743"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Security Center-integrering med Azure Security-produkter i ASC

Security Center erbjuder kunderna med ytterligare Microsoft-licenser för att publicera sina resultat till Security Center och visa dem i en konsoliderad sätt.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webbprogram som alltmer omfattas av skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway WAF är baserad på Core Rule ange (CRS) 3.0 eller 2.2.9 från den öppna OWASP Web Application Security Project (). WAF uppdateras automatiskt för att skydda mot nya säkerhetsrisker med ingen ytterligare konfiguration krävs. Aviseringar som genereras av WAF strömmas till Security Center. Mer information om aviseringar som genereras av WAF finns i den här [artikeln](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Distribuerade attacker denial of service (DDoS) har visat sig vara enkelt att köra. De har därför blivit en bra säkerhetsproblematik för kunder som migrerar sina program till molnet. 

En DDoS-attack syftar till att göra slut på ett programs resurser, så att programmet blir otillgängligt för behöriga användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås via Internet.

Azure DDoS protection, tillsammans med programmet Metodtips för design, skapa skydd mot DDoS-attacker. Azure DDoS protection erbjuder olika tjänstnivåer. Mer information finns i [översikt över Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection Standard kan minimera följande typer av attacker:

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Överför-attack upptäcktes**|Den här attack målet är att översvämma nätverksnivå med en betydande mängd till synes legitima trafik. Den innehåller UDP översvämningar, förstärkning översvämning och andra falska paket översvämningar. DDoS Protection Standard minskar risken för potentiella flera gigabyte angrepp genom att absorbera och Skrubba dem, med Azures globala nätverk skala automatiskt.|
|**Protokoll-attack upptäcktes**|Dessa attacker, kanske ett mål användas, genom att utnyttja en svaghet i layer 3 och layer 4-protokollstacken. Den innehåller, SYN-översvämning attacker, reflektionsattacker och andra protokoll-attacker. DDoS Protection Standard minskar risken för sådana attacker så skilja mellan skadlig och legitima trafik genom att interagera med klienten och blockera skadlig trafik.|
|**Resurs (program) layer-attack upptäcktes**|Dessa attacker rikta web application paket, för att störa överföring av data mellan värdar. Attackerna omfattar HTTP protokollbrott SQL injection, cross site scripting och andra layer 7-attacker. Använda Azure Application Gateway waf, med DDoS Protection Standard för att skydda mot angrepp. Det finns även andra leverantörers webb-programmet brandväggen erbjudanden på Azure Marketplace.|
