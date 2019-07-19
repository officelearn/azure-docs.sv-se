---
title: Konfigurera bot-skydd för brand vägg för webbaserade program med Azures front dörr (för hands version)
description: Lär dig mer om brand vägg för webbaserade program (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846347"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurera bot-skydd för brand vägg för webbaserade program (för hands version)
Den här artikeln visar hur du konfigurerar skydds regeln för bot i Azure Web Application Firewall (WAF) för front dörren med hjälp av Azure CLI, Azure PowerShell eller Azure Resource Manager mall.

En regel uppsättning för hanterad bot-skydd kan aktive ras för WAF för att vidta anpassade åtgärder på begär Anden från kända skadliga IP-adresser. IP-adresserna har en källa från Microsoft Threat Intelligence-flödet. [Intelligent Security graphs](https://www.microsoft.com/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Skapa en grundläggande WAF-princip för front dörren genom att följa anvisningarna i [skapa en WAF-princip för Azure-frontend med hjälp av Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera regel uppsättning för bot-skydd

1. På sidan grundläggande princip som du skapade i föregående avsnitt, under **Inställningar**, klickar du på **regler**.
2. På sidan information under avsnittet **Hantera regler** går du till den nedrullningsbara menyn och markerar kryss rutan framför regeln **BotProtection-Preview-0,1**och väljer sedan **Spara** ovan.
    
   ![Skydds regel för bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar WAF](waf-front-door-monitor.md).
