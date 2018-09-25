---
title: Automatisk skalning och zonredundant Application Gateway i Azure (offentlig förhandsversion) | Microsoft Docs
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantagslistor i Application Gateway med Azure-portalen.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 5d051ed049964af708056e2963f04cb478c15906
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951516"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)

Application Gateway och Web Application Firewall (WAF) finns nu i offentlig förhandsversion under en ny SKU som ger prestandaförbättringar och lägger till stöd för viktiga nya funktioner som automatisk skalning, redundans och stöd för statiska virtuella IP-adresser. Befintliga funktioner under den allmänt tillgängliga SKU: N fortfarande användas i den nya SKU: N med några undantag som visas i avsnittet om kända begränsningar. De nya SKU: er inkluderar följande förbättringar:

- **Automatisk skalning**: Application Gateway eller WAF-distributioner under autoskalning SKU kan skala upp eller ned baserat på Ändra trafik läsa in mönster. Automatisk skalning tar också bort behovet av att välja en storlek eller instans antalet under etableringen. Därför erbjuder SKU: N SANT elasticitet. I den nya SKU: N kan Application Gateway fungerar både i fast kapacitet (automatisk skalning inaktiverad) samt i läget för automatisk skalning aktiverat. Fast kapacitet läge är användbart för scenarier med konsekventa och förutsägbara arbetsbelastningar. Läget för automatisk skalning är bra i program som finns ser en massa avvikelse i programtrafiken.
   
   > [!NOTE]
   > Automatisk skalning är inte tillgänglig för WAF SKU. Konfigurera WAF med fast kapacitet läge, i stället för läget för automatisk skalning.
- **Zon redundans**: An Application-Gateway eller WAF-distribution kan sträcka sig över flera Tillgänglighetszoner, ta bort behovet av att etablera och skapa separata Application Gateway-instanser i varje zon med Traffic Manager. Du kan välja en enskild zon eller flera zoner där Application Gateway-instanser har distribuerats, därmed säkerställa fel zonelasticitet. Serverdelspoolen för program kan distribueras på samma sätt i olika tillgänglighetszoner.
- **Prestandaförbättringar**: autoskalning SKU ger upp till 5 X bättre SSL-avlastning prestanda jämfört med den allmänt tillgängliga SKU: N.
- **Snabbare distribution och uppdatering** autoskalning SKU ger distribution och uppdatering snabbare jämfört med den allmänt tillgängliga SKU: N.
- **Statisk VIP**: VIP application gateway stöder nu statiska VIP-typ exklusivt. Detta säkerställer att VIP-Adressen som är associerade med application gateway inte ändras även efter en omstart.

> [!IMPORTANT]
> Automatisk skalning och zonredundant application gateway SKU är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regioner som stöds
Autoscaling SKU är tillgängligt i östra USA 2, centrala USA, västra usa2, Frankrike, centrala, Västeuropa och Sydostasien.

## <a name="pricing"></a>Prissättning
I förhandsversionen är gratis. Du kommer att debiteras för andra resurser än Programgateway, till exempel Key Vault, virtuella datorer och så vidare. 

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

|Problem|Information|
|--|--|
|Fakturering|Det finns ingen fakturering för närvarande.|
|FIPS-läge, WebSocket|Dessa stöds inte för närvarande.|
|ILB läge|Detta stöds för närvarande inte. Offentliga och ILB-läget tillsammans stöds.|
|Web application firewall automatisk skalning|WAF har inte stöd för läget för automatisk skalning. Fast kapacitet läge stöds.|

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure brandvägg](../firewall/overview.md). 

