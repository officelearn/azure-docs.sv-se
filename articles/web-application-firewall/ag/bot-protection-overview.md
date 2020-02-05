---
title: Översikt över WAF för Azure Application Gateway robot
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över brand vägg för webbaserade program (WAF) på Application Gateway bot-skydd
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027100"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Översikt över Azure Web Application-brandvägg på Azure Application Gateway-skydd

Ungefär 20% av all Internet trafik kommer från felaktiga robotar. De gör sådant som att kassera, skanna och söka efter sårbarheter i ditt webb program. När dessa robotar stoppas i brand väggen för webbaserade program (WAF) kan de inte angripa dig. De kan inte heller använda sig av dina resurser och tjänster, t. ex. dina Server delar och annan underliggande infrastruktur.

Du kan aktivera en hanterad bot-skydds regel uppsättning för din WAF för att blockera eller logga förfrågningar från kända skadliga IP-adresser. IP-adresserna har en källa från Microsoft Threat Intelligence-flödet. Microsoft Threat Intelligence använder Intelligent Security Graph som också används av flera olika tjänster, inklusive Azure Security Center.

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna för mer information.

## <a name="use-with-owasp-rulesets"></a>Använd med OWASP rulesets

Du kan använda bot Protection-ruleset tillsammans med alla OWASP-rulesets (2.2.9, 3,0 och 3,1). Endast en OWASP-ruleset kan användas vid en specifik tidpunkt. Ruleset för bot-skydd innehåller en ytterligare regel som visas i sin egen ruleset. Den heter **Microsoft_BotManagerRuleSet_0**1 och du kan aktivera eller inaktivera den som de andra OWASP-reglerna.

![Bot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Ruleset-uppdatering

Ruleset i bot-listan över kända felaktiga IP-adresser uppdateras flera gånger per dag från Microsoft Threat Intelligence-flödet för att hållas synkroniserat med robotar. Dina webb program skyddas kontinuerligt även när robot angrepps metoderna ändras.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera bot-skydd för brand vägg för webbaserade program på Azure Application Gateway (för hands version)](bot-protection.md)
