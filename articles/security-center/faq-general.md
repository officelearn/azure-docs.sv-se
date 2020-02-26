---
title: Azure Security Center vanliga frågor och svar – allmänna frågor
description: Vanliga frågor och svar om Azure Security Center, en produkt som hjälper dig att förhindra, upptäcka och reagera på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b13e5e0000cdd8e33f459400cc4c24d107ccaba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604787"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Vanliga frågor och svar – allmänna frågor om Azure Security Center

## <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center hjälper dig att förhindra, identifiera och svara på hot med ökad insyn i och kontroll över säkerheten för dina resurser. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Detaljerad information finns i [data insamling i Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Hur får jag Azure Security Center?
Azure Security Center aktive ras med din Microsoft Azure-prenumeration och nås från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). För att få åtkomst till den [loggar du in på portalen](https://portal.azure.com), väljer **Bläddra**och bläddrar till **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VM) (inklusive [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Skalningsuppsättningar för virtuella datorer
* Azure Virtual Networks
* Azure SQL-tjänst
* Azure-lagringskonto
* Azure-Web Apps (i [App Service-miljön](../app-service/environment/intro.md))
* Partnerlösningar integreras med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer och på App Service Environment

Dessutom kan icke-Azure-datorer (inklusive lokala) också övervakas av Azure Security Center (både [Windows-datorer](./quick-onboard-windows-computer.md) och [Linux-datorer](./quick-onboard-linux-computer.md) stöds)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hur kan jag se det aktuella säkerhetstillståndet för Mina Azure-resurser?
På sidan **Security Center översikt** visas den övergripande säkerhets position i miljön, uppdelad efter beräkning, nätverk, lagring & data och program. Varje resurstyp har en indikator som visar om eventuella säkerhetsrisker som har identifierats. Om du klickar på varje panel visas en lista över säkerhetsproblem som identifieras av Security Center tillsammans med en förteckning över resurser i din prenumeration.



## <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip för?
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom den angivna prenumerationen. I Azure Security Center ställer in du principer för dina Azure-prenumerationer utifrån företagets säkerhetskrav och typ av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration.

Säkerhetsprinciperna i Azure Security Center enhet säkerhetsrekommendationer och övervakning. Mer information om säkerhets principer finns [i övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Vem som kan ändra en säkerhetsprincip för?
Om du vill ändra en säkerhetsprincip måste du vara en administratör eller ägare eller deltagare i den aktuella prenumerationen.

Information om hur du konfigurerar en säkerhets princip finns [i ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När eventuella säkerhetsproblem identifieras visas skapas rekommendationer. Via rekommendationerna får du genom processen att konfigurera nödvändig kontroll. Några exempel:

* Etablering av program mot skadlig kod för att identifiera och ta bort skadlig programvara
* [Nätverks säkerhets grupper](../virtual-network/security-overview.md) och regler för att styra trafik till virtuella datorer
* Etablering av en brandvägg för webbaserade program för att skydda mot hot mot dina webbprogram
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Här visas bara de rekommendationer som är aktiverade i säkerhetsprinciper.



## <a name="what-triggers-a-security-alert"></a>Vad utlöser en säkerhetsvarning?
Azure Security Center automatiskt samlar in, analyserar och Smältsäkringar loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* Avancerad skadlig kod har identifierats med hjälp av Windows Felrapportering
* nyckelsökningsangrepp mot virtuella datorer
* Säkerhetsaviseringar från integrerade partnerlösningar säkerhetslösningar som skadlig eller brandväggar för webbprogram


## Varför har säkra Poäng värden ändrats? <a name="secure-score-faq"></a>
Från och med februari 2019 har Security Center justerat poängen för några få rekommendationer, så att det bättre passar deras allvarlighets grad. Till följd av den här ändringen kan det uppstå ändringar i övergripande säkra Poäng värden.  Mer information om säkra Poäng finns i [säker Poäng beräkning](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot identifieras och rapporteras på av Microsoft Security Response Center jämfört med Azure Security Center?
Microsoft Security Response Center (MSRC) utför väljer säkerhetsövervakning för Azure-nätverk och infrastruktur och tar emot threat intelligence och missbruk klagomål från tredje part. När MSRC blir medveten om att kunddata har använts av en obehörig part eller att kundens användning av Azure inte uppfyller villkoren för godkända använder en incident säkerhetshanteraren meddelar kunden. -Meddelande sker vanligtvis genom att skicka ett e-postmeddelande till säkerhetskontakter som angetts i Azure Security Center eller Azure-prenumerationsägare om en säkerhetskontakt inte har angetts.

Security Center är en Azure-tjänst som ständigt övervakar kundens Azure-miljön och gäller analys för att automatiskt identifiera en mängd eventuellt skadlig aktivitet. Dessa identifieringar är anslutna som säkerhetsaviseringar i Security Center-instrumentpanelen.