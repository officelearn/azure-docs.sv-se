---
title: Konfigurera botskydd för brandvägg för webbprogram med Azure Front Door (preview)
description: Lär dig brandvägg för webbprogram (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934660"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurera botskydd för brandvägg för webbprogram (förhandsgranskning)
Den här artikeln visar hur du konfigurerar botskyddsregel i Azure Web Application Firewall (WAF) för Ytterdörren med hjälp av Azure-portalen. Bot-skyddsregel kan också konfigureras med cli-, Azure PowerShell- eller Azure Resource Manager-mallen.

> [!IMPORTANT]
> Bot protection regeluppsättning är för närvarande i offentlig förhandsversion och är försedd med en förhandsvisning servicenivå avtal. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Skapa en grundläggande WAF-princip för Ytterdörren genom att följa instruktionerna som beskrivs i [Skapa en WAF-princip för Azure Front Door med hjälp av Azure-portalen](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Aktivera botskyddsregeluppsättning

På sidan **Hanterade regler** när du skapar en brandvägg för webbprogram söker du först efter avsnittet **Hanterad regeluppsättning,** markerar kryssrutan framför regeln **Microsoft_BotManager_1.0** på den nedrullningsbara menyn och väljer sedan **Granska + Skapa**.

   ![Bot skyddsregel](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [övervakar WAF](waf-front-door-monitor.md).
