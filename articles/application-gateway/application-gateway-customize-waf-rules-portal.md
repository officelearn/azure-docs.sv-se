---
title: "Anpassa web application brandväggsregler i Azure Application Gateway - Azure-portalen | Microsoft Docs"
description: "Den här artikeln innehåller information om hur du anpassar web application brandväggsregler i Programgateway med Azure-portalen."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 406e491aa54806b3534ef0f500d6aea03434e2c2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Anpassa web application brandväggsregler via Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Den Azure Programgateway brandväggen för webbaserade program (Brandvägg) ger skydd för webbprogram. Dessa skydd tillhandahålls genom att öppna Web Application säkerhet projekt (OWASP) Core regeln ange (CR). Vissa regler kan leda till falska positiva identifieringar och blockera verkliga trafik. Därför ger Programgateway möjlighet att anpassa regelgrupper och regler. Mer information om specifik regelgrupper och regler finns [listan över web brandväggen CR regeln programgrupper och regler](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Om din Programgateway inte använder Brandvägg nivån, visas alternativet att uppgradera Programgateway Brandvägg nivån i den högra rutan. 

![Aktivera Brandvägg][fig1]

## <a name="view-rule-groups-and-rules"></a>Visa grupper av regeln och regler

**Visa regelgrupper och regler**
   1. Bläddra till programgatewayen och välj sedan **Brandvägg för webbaserade program**.  
   2. Välj **avancerade regelkonfigurationen**.  
   Den här vyn visas en tabell på sidan alla regelgrupper som ingår i den valda regeluppsättningen. Alla regelns kryssrutorna är markerade.

![Konfigurera inaktiverade regler][1]

## <a name="search-for-rules-to-disable"></a>Sök efter regler ska inaktiveras

Den **Web application brandväggsinställningar** bladet ger dig möjlighet att filtrera regler via textsökning. Resultatet visar endast regelgrupper och regler som innehåller den text du söker efter.

![Sök efter regler][2]

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regelgrupper och regler

När din är inaktivera regler, du kan inaktivera en hel grupp eller särskilda regler under en eller flera regelgrupper. 

**Inaktivera regelgrupper eller särskilda regler**

   1. Sök efter regler eller regelgrupper som du vill inaktivera.
   2. Avmarkera kryssrutorna för de regler som du vill inaktivera. 
   2. Välj **Spara**. 

![Spara ändringar][3]

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler du lära dig hur du visar Brandvägg loggarna. Mer information finns i [Programgateway diagnostik](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
