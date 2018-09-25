---
title: Anpassa brandväggsregler för webbaserade program i Azure Application Gateway – Azure-portalen | Microsoft Docs
description: Den här artikeln innehåller information om hur du anpassar web application brandväggsregler i Application Gateway med Azure-portalen.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: 30df26dc3a9697d3435779f91c32b2d99a747b88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990475"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Anpassa brandväggsregler för webbaserade program via Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Dessa skydd tillhandahålls av OWASP Open Web Application Security Project () Core regeln ange (CRS). Vissa regler kan leda till falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regelgrupper och -regler. Läs mer på den specifika regelgrupper och regler, [lista över web application firewall tillhandahållna CRS-regelgrupper och -regler](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Om din application gateway inte använder WAF-nivån, visas möjlighet att uppgradera application gateway WAF-nivån i den högra rutan. 

![Aktivera WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

**Visa regelgrupper och -regler**
   1. Bläddra till application gateway och välj sedan **Brandvägg för webbaserade program**.  
   2. Välj **avancerad Regelkonfiguration**.  
   Den här vyn visar en tabell på sidan alla regelgrupper som medföljer valda regeluppsättningen. Alla regelns kryssrutorna är markerade.

![Konfigurera inaktiverade regler][1]

## <a name="search-for-rules-to-disable"></a>Sök efter regler för att inaktivera

Den **brandväggsinställningar för webbprogram** bladet ger dig möjlighet att filtrera regler via en textsökning. Resultatet visar endast regelgrupper och regler som innehåller den text du söker efter.

![Sök efter regler][2]

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regelgrupper och -regler

När din är inaktiverar regler kan du kan inaktivera en för hela regelgruppen eller särskilda regler under en eller flera regelgrupper. 

**Inaktivera regelgrupper eller särskilda regler**

   1. Sök efter regler eller regelgrupper som du vill inaktivera.
   2. Avmarkera kryssrutorna för de regler som du vill inaktivera. 
   2. Välj **Spara**. 

![Spara ändringar][3]

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler för kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
