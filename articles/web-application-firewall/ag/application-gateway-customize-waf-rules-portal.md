---
title: Anpassa regler med portalen - Brandvägg för Azure Web Application
description: Den här artikeln innehåller information om hur du anpassar webbprogrambrandväggsregler i Application Gateway med Azure-portalen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048367"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Anpassa brandväggsregler för webbprogram med Azure-portalen

Azure Application Gateway Web Application Firewall (WAF) skyddar webbprogram. Dessa skydd tillhandahålls av OWASP-huvudregeluppsättningen (Open Web Application Security Project) (CRS). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför tillhandahåller Application Gateway möjligheten att anpassa regelgrupper och regler. Mer information om specifika regelgrupper och regler finns i [Lista över CRS-regelgrupper och regler för webbprogram brandvägg.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Om programgatewayen inte använder WAF-nivån visas alternativet för att uppgradera programgatewayen till WAF-nivån i den högra rutan. 

![Aktivera WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

**Så här visar du regelgrupper och regler**
1. Bläddra till programgatewayen och välj sedan **brandvägg för webbprogram**.  
2. Välj **WAF-policy**.
2. Välj **Hanterade regler**.

   I den här vyn visas en tabell på sidan för alla regelgrupper som medföljer den valda regeluppsättningen. Alla kryssrutor för regeln är markerade.

## <a name="disable-rule-groups-and-rules"></a>Inaktivera regelgrupper och regler

> [!IMPORTANT]
> Var försiktig när du inaktiverar regelgrupper eller regler. Detta kan utsätta dig för ökade säkerhetsrisker.

När du inaktiverar regler kan du inaktivera en hel regelgrupp eller specifika regler under en eller flera regelgrupper. 

**Så här inaktiverar du regelgrupper eller specifika regler**

   1. Sök efter de regler eller regelgrupper som du vill inaktivera.
   2. Markera kryssrutorna för de regler som du vill inaktivera. 
   3. Markera åtgärden högst upp på sidan (aktivera/inaktivera) för de valda reglerna.
   2. Välj **Spara**. 

![Spara ändringar][3]

## <a name="mandatory-rules"></a>Tvingande regler

Följande lista innehåller villkor som gör att WAF blockerar begäran i förebyggande läge. I identifieringsläge loggas de som undantag.

Dessa kan inte konfigureras eller inaktiveras:

* Om begäran inte tolkas resulterar det i att begäran blockeras, såvida inte kroppsinspektionen är inaktiverad (XML, JSON, formulärdata)
* Datalängden för begäran (utan filer) är större än den konfigurerade gränsen
* Begärantexten (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x specifik:

* Inkommande avvikelsepoäng överskred tröskelvärdet

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina inaktiverade regler kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [Diagnostik för Programgateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
