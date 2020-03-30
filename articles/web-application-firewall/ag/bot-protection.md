---
title: Konfigurera botskydd för Azure Web Application Firewall (WAF)
description: Lär dig hur du konfigurerar botskydd för brandvägg för webbprogram (WAF) på Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516869"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurera botskydd för brandvägg för webbprogram på Azure Application Gateway (förhandsversion)

Den här artikeln visar hur du konfigurerar en botskyddsregel i Azure Web Application Firewall (WAF) för Application Gateway med Hjälp av Azure-portalen. 

Du kan aktivera en hanterad bot-skyddsregeluppsättning för din WAF för att blockera eller logga begäranden från kända skadliga IP-adresser. IP-adresserna kommer från Microsoft Threat Intelligence-feeden. Intelligent Security Graph driver Microsofts hotinformation och används av flera tjänster, inklusive Azure Security Center.

> [!NOTE]
> Botskyddsregeluppsättningen är för närvarande i offentlig förhandsversion och har ett förhandsversionsavtal på tjänstnivå. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i de [kompletterande användarvillkoren för Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="prerequisites"></a>Krav

Skapa en grundläggande WAF-princip för Application Gateway genom att följa instruktionerna som beskrivs i [Skapa brandväggsprinciper för webbprogram för Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera botskyddsregeluppsättning

1. Välj **Regler**under **Inställningar**på sidan **Grundläggande** princip som du skapade tidigare.  

2. På informationssidan, under avsnittet **Hantera regler,** markerar du kryssrutan för botskyddsregeln på den nedrullningsbara menyn på den nedrullningsbara menyn och väljer sedan **Spara**.

> [!div class="mx-imgBorder"]
> ![Robotskydd](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Nästa steg

Mer information om anpassade regler finns i [Anpassade regler för brandvägg för webbprogram v2 på Azure Application Gateway](custom-waf-rules-overview.md).