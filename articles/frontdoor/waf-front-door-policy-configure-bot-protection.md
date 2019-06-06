---
title: Konfigurera bot skydd för Brandvägg för webbaserade program med Azure ytterdörren (förhandsversion)
description: Läs om Brandvägg för webbaserade program (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481631"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurera bot skydd för Brandvägg för webbaserade program (förhandsversion)
Den här artikeln visar hur du konfigurerar bot regeln för skydd i Azure web application firewall (WAF) för åtkomsten med hjälp av Azure CLI, Azure PowerShell eller Azure Resource Manager-mall.

En hanterad regeluppsättning för Bot-skydd kan aktiveras för din WAF vidtar åtgärder för anpassade på begäranden från kända skadliga IP-adresser. IP-adresser kommer från Microsoft Hotinformationen feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Driver Microsofts hotintelligens och används av flera tjänster, inklusive Azure Security Center.

> [!IMPORTANT]
> Regeluppsättning för bot-skydd är för närvarande i offentlig förhandsversion och ingår ett serviceavtal för förhandsversionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Skapa en grundläggande WAF-princip för åtkomsten genom att följa anvisningarna som beskrivs i [skapa en WAF-princip för Azure åtkomsten med hjälp av Azure portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera regeluppsättningen för bot-skydd

1. På sidan grundläggande princip som du skapade i föregående avsnitt, under **inställningar**, klickar du på **regler**.
2. På informationssidan under den **hantera regler** avsnittet från den nedrullningsbara menyn, markerar du kryssrutan framför regeln **BotProtection-förhandsversion – 0.1**, och välj sedan **spara**ovan.
    
   ![Regeln för skydd av bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervaka WAF](waf-front-door-monitor.md).
