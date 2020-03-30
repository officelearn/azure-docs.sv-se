---
title: WAF på Azure Application Gateway bot skydd översikt
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över brandvägg för webbprogram (WAF) om botskydd för Application Gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027100"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Web Application-brandväggen på botskyddsöversikt för Azure Application Gateway

Ungefär 20% av all Internettrafik kommer från dåliga robotar. De gör saker som skrapning, skanning och letar efter sårbarheter i ditt webbprogram. När dessa robotar stoppas vid brandväggen för webbprogram (WAF) kan de inte attackera dig. De kan inte heller använda dina resurser och tjänster, till exempel dina backends och annan underliggande infrastruktur.

Du kan aktivera en hanterad bot-skyddsregeluppsättning för din WAF för att blockera eller logga begäranden från kända skadliga IP-adresser. IP-adresserna kommer från Microsoft Threat Intelligence-feeden. Intelligent Security Graph driver Microsofts hotinformation och används av flera tjänster, inklusive Azure Security Center.

> [!IMPORTANT]
> Botskyddsregeluppsättningen är för närvarande i offentlig förhandsversion och har ett förhandsversionsavtal på tjänstnivå. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i de [kompletterande användarvillkoren för Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="use-with-owasp-rulesets"></a>Använd med OWASP-regeluppsättningar

Du kan använda botskyddsregleruppsättningen tillsammans med någon av OWASP-regeluppsättningarna (2.2.9, 3.0 och 3.1). Endast en OWASP-regeluppsättning kan användas vid en given tidpunkt. Botskyddsregleruppsättningen innehåller ytterligare en regel som visas i den egna regeluppsättningen. Det heter **Microsoft_BotManagerRuleSet_0.1**, och du kan aktivera eller inaktivera det som de andra OWASP-reglerna.

![Bot regler](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Uppdatering av regeluppsättningen

Listan över botreducerande regeluppsättningar över kända felaktiga IP-adresser uppdateras flera gånger per dag från Microsoft Threat Intelligence-flödet för att vara synkroniserad med robotarna. Dina webbapplikationer skyddas kontinuerligt även när botattackvektorerna ändras.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera botskydd för brandvägg för webbprogram på Azure Application Gateway (förhandsversion)](bot-protection.md)
