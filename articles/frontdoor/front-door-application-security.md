---
title: Azure ytterdörren Service – layer programsäkerhet | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren Service gör det möjligt att skydda och säkra dina serverdelar
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
ms.openlocfilehash: e5714f60b7fdd790f3af8e31250c41038110fd08
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047917"
---
# <a name="application-layer-security-with-front-door"></a>Säkerhet för lager med ytterdörren
Azure ytterdörren Service tillhandahåller web application protection möjligheten att skydda dina webbappar mot nätverksattacker och sårbarheter säkerhetsrisker som SQL Injection eller Cross Site Scripting (XSS). Aktiverat för http (s) av klientdelar, distribueras globalt Front dörren layer programsäkerhet och alltid på, stoppar skadliga attacker på Azures network edge, långt från serverdelen. Med ökad säkerhet och prestandaoptimering ytterdörren ger snabb och säker webbupplevelse för dina slutanvändare.

## <a name="application-protection"></a>Programskydd
Front dörren programskydd konfigureras på varje edge-miljö i hela världen, i enlighet med program, och blockerar automatiskt icke-HTTP-trafik från att nå dina webbprogram. Vår distribuerad arkitektur med flera innehavare kan globala skydd i stor skala utan att offra prestanda. För http (s)-arbetsbelastningar, Front dörren webbtjänsten programmet protection tillhandahåller en omfattande regelmotor för anpassade regler, förkonfigurerade ruleset mot vanliga attacker och detaljerad loggning för alla begäranden som matchar en regel. Flexibla åtgärder inklusive tillåta, blockera eller log bara som stöds.

## <a name="custom-access-control-rules"></a>Anpassade regler för åtkomstkontroll
- **IP-svart- och vitlistor:** du kan konfigurera anpassade regler för att styra åtkomsten till dina webbprogram baserat på listan över IP-adresser för klienten. Både IP v4 och v6 IP stöds
- **Geografisk åtkomstkontroll:** du kan konfigurera anpassade regler för att styra åtkomsten till ditt webbprogram utifrån landskod klientens IP-adress är från
- **HTTP-parametrar som filtrering:** kan du konfigurera regler för anpassad åtkomst baserat på matchning parametrarna som http (s), inklusive rubriker, URL: en, och fråga efter strängar

## <a name="azure-managed-rules"></a>Azure-hanterade regler
- En förkonfigurerad uppsättning regler mot vanliga säkerhetsproblem för övre OWASP är aktiverat som standard. Förhandsversionen innehåller uppsättningen regler sqli och xss-begäranden som kontrollerar. Ytterligare regler kommer att läggas till. Du kan välja att starta med endast log-åtgärd att validera förinställda regler som fungerar som förväntat för dina program 

## <a name="rate-limiting"></a>Hastighetsbegränsningar
- En regel för kontroll av priset är att begränsa onormalt hög trafik från klientens IP-adress.  Du kan ange ett tröskelvärde för många begäranden tillåts av klientens IP-adress under en tid för en minut.

## <a name="centralized-protection-policy"></a>Centraliserad skyddsprincip
- Du kan definiera flera skyddsregler och lägga till dem i en princip i prioritetsordning. Anpassade regler har högre prioritet än hanterade RuleSet-metod att tillåta undantag. En enda princip är kopplad till ditt webbprogram.  Samma webb-princip för programskydd replikeras till alla edge-servrar på alla platser, se till att konsekvent säkerhetsprincip i alla regioner

## <a name="configuration"></a>Konfiguration
- I förhandsversionen kan du använda REST API: er, PowerShell eller CLI att skapa och distribuera Front dörren programmet protection regler och principer. Portalåtkomst stöds innan tjänsten är allmänt tillgänglig. 


## <a name="monitoring"></a>Övervakning
Ytterdörren ger möjlighet att övervaka webbprogram mot attacker med hjälp av i realtid mätvärden som är integrerade med Azure Monitor för att spåra aviseringar och enkelt övervaka trender.

## <a name="pricing"></a>Prissättning
Front dörren lager säkerhet är kostnadsfri under förhandsversionen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en ytterdörren](quickstart-create-front-door.md).
- Lär dig [hur ytterdörren fungerar](front-door-routing-architecture.md).
