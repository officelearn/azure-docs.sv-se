---
title: Konfigurera bot-skydd för brand vägg för webbaserade program med Azures front dörr (för hands version)
description: Lär dig mer om brand vägg för webbaserade program (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512475"
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
    
   ![Skydds regel för bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar WAF](waf-front-door-monitor.md).
