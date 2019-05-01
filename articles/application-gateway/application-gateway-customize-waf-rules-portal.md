---
title: Anpassa brandväggsregler för webbaserade program i Azure Application Gateway – Azure-portalen
description: Den här artikeln innehåller information om hur du anpassar web application brandväggsregler i Application Gateway med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720394"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Anpassa brandväggsregler för webbaserade program via Azure portal

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

Den **brandväggsinställningar för webbprogram** sidan ger dig möjlighet att filtrera regler via en textsökning. Resultatet visar endast regelgrupper och regler som innehåller den text du söker efter.

![Sök efter regler][2]

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regelgrupper och -regler

> [!IMPORTANT]
> Var försiktig när du inaktiverar regelgrupper eller regler. Detta kan medföra att du till ökade säkerhetsrisker.

När du inaktiverar regler kan inaktivera du en för hela regelgruppen eller särskilda regler under en eller flera regelgrupper. 

**Inaktivera regelgrupper eller särskilda regler**

   1. Sök efter regler eller regelgrupper som du vill inaktivera.
   2. Avmarkera kryssrutorna för de regler som du vill inaktivera. 
   2. Välj **Spara**. 

![Spara ändringar][3]

## <a name="mandatory-rules"></a>Obligatoriska regler

I följande lista innehåller villkor som gör att WAF för att blockera begäran i Förhindringsläge. I identifieringsläge, är användaren inloggad som undantag.

Dessa kan inte vara konfigurerad eller inaktiverad:

* Det gick inte att parsa begärandetexten resulterar i begäran blockeras, såvida inte brödtext inspektion stängs av (XML, JSON, formulärdata)
* Datalängden för begäran brödtext (utan att några filer) är större än den konfigurerade gränsen
* Brödtext (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x specifika:

* Inkommande avvikelseidentifiering poäng överskred tröskeln

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler för kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
