---
title: Azure Ytterdörr - Programlager säkerhet | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door gör det möjligt att skydda och skydda dina programserveringar
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471803"
---
# <a name="application-layer-security-with-front-door"></a>Säkerhet för applikationslagret med ytterdörr
Azure Front Door tillhandahåller webbprogramskyddsfunktioner för att skydda dina webbprogram från nätverksattacker och vanliga webbsårbarheter som SQL Injection eller Cross Site Scripting (XSS). Front Door's programlagersäkerhet är aktiverat för http(s) front-ends och är globalt distribuerad och alltid på, vilket stoppar skadliga attacker på Azures nätverkskant, långt borta från dina backends. Med ökad säkerhets- och prestandaoptimering levererar Front Door snabba och säkra webbupplevelser till dina slutanvändare.

## <a name="application-protection"></a>Programskydd
Front Doors programskydd är konfigurerat på varje kantmiljö runt om i världen, i linje med program, och blockerar automatiskt icke-http-trafik från att nå dina webbapplikationer. Vår distribuerade arkitektur med flera innehavare möjliggör globalt skydd i stor skala utan att offra prestanda. För http/er-arbetsbelastningar tillhandahåller Front Doors webbprogramskyddstjänst en omfattande regelmotor för anpassade regler, förkonfigurerade regeluppsättning mot vanliga attacker och detaljerad loggning för alla begäranden som matchar en regel. Flexibla åtgärder, inklusive endast tillåt, blockera eller logga, stöds.

## <a name="custom-access-control-rules"></a>Regler för anpassad åtkomstkontroll
- **LISTA och blocklista för IP-tillåta:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webbprogram baserat på en lista över klient-IP-adresser. Både IP v4 och IP v6 stöds
- **Geografiskt baserad åtkomstkontroll:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webbprogram baserat på landskod som en klient-IP kommer från
- **HTTP-parametrars filtrering:** Du kan konfigurera anpassade åtkomstregler baserat på matchande parametrar för http(s) begäran, inklusive rubriker, URL och frågesträngar

## <a name="azure-managed-rules"></a>Azure-hanterade regler
- En förkonfigurerad uppsättning regler mot vanliga vanliga OWASP-sårbarheter är aktiverad som standard. Vid förhandsversionen innehåller uppsättningen regler sqli- och xss-begäranden kontroll. Ytterligare regler kommer att läggas till. Du kan välja att börja med endast loggåtgärd för att validera förkonfigurerade regler som förväntat för dina program 

## <a name="rate-limiting"></a>Frekvensbegränsning
- En hastighetskontrollregel är att begränsa onormal hög trafik från en klient-IP.  Du kan ange ett tröskelvärde för antalet webbbegäranden som tillåts av en klient-IP under en minuts varaktighet.

## <a name="centralized-protection-policy"></a>Centraliserad skyddspolitik
- Du kan definiera flera skyddsregler och lägga till dem i en princip i prioritetsordning. Anpassade regler har högre prioritet än hanterade regeluppsättning för att tillåta undantag. En enskild princip är kopplad till webbprogrammet.  Samma princip för webbprogramskydd replikeras till alla kantservrar på alla platser, vilket säkerställer en konsekvent säkerhetsprincip i alla regioner

## <a name="configuration"></a>Konfiguration
- Under förhandsversionen kan du använda REST-API:er, PowerShell eller CLI för att skapa och distribuera Front Doors programskyddsregler och principer. Portalåtkomst kommer att stödjas innan tjänsten är allmänt tillgänglig. 


## <a name="monitoring"></a>Övervakning
Ytterdörren ger möjlighet att övervaka webbprogram mot attacker med hjälp av mätvärden i realtid som är integrerade med Azure Monitor för att spåra aviseringar och enkelt övervaka trender.

## <a name="pricing"></a>Prissättning
Front Doors programlagersäkerhet är gratis under förhandsgranskningen.


## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
