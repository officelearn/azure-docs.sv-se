---
title: Automatisk skalning och zonredundant Application Gateway i Azure (offentlig förhandsversion)
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantagslistor i Application Gateway med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/10/2019
ms.author: victorh
ms.openlocfilehash: f5885fd2ac76550990c9a56a1d200bbe11555918
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213764"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)

Application Gateway och Web Application Firewall (WAF) finns nu i offentlig förhandsversion under en ny v2-SKU som ger prestandaförbättringar och lägger till stöd för viktiga nya funktioner som automatisk skalning, redundans och stöd för statiska virtuella IP-adresser. Befintliga funktioner under den allmänt tillgängliga SKU: N fortfarande användas i den nya v2-SKU, med några undantag som anges i avsnitt kända begränsningar. De nya v2-SKU: er inkluderar följande förbättringar:

- **Automatisk skalning**: Application Gateway eller WAF-distributioner under autoskalning SKU kan skala upp eller ned utifrån ändrade trafikmönster belastningen. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Därför erbjuder SKU: N SANT elasticitet. I den nya SKU: N kan Application Gateway fungerar både fast kapacitet (automatisk skalning inaktiverat) och i läget för automatisk skalning aktiverat. Fast kapacitet läge är användbart för scenarier med konsekventa och förutsägbara arbetsbelastningar. Läget för automatisk skalning är bra i program som finns ser många variationer i programtrafiken.
   
   > [!NOTE]
   > Automatisk skalning är inte tillgänglig för WAF SKU. Konfigurera WAF med fast kapacitet läge, i stället för läget för automatisk skalning.
- **Zon redundans**: En Application Gateway eller WAF-distribution kan sträcka sig över flera Tillgänglighetszoner, ta bort behovet av att etablera och skapa separata Application Gateway-instanser i varje zon med Traffic Manager. Du kan välja en enskild zon eller flera zoner där Application Gateway-instanser har distribuerats, därmed säkerställa fel zonelasticitet. Serverdelspoolen för program kan distribueras på samma sätt i olika tillgänglighetszoner.
- **Prestandaförbättringar**: Autoskalning SKU: N erbjuder upp till 5 X bättre SSL-avlastning prestandan jämfört med den allmänt tillgängliga SKU: N.
- **Snabbare distribution och uppdatering** autoskalning SKU ger distribution och uppdatering snabbare jämfört med den allmänt tillgängliga SKU: N.
- **Statisk VIP**: VIP-Adressen för application gateway stöder nu statiska VIP-typ som är exklusivt. Detta säkerställer att det VIP som är associerat med Application Gateway inte ändras även efter en omstart.

> [!IMPORTANT]
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regioner som stöds
Autoscaling SKU är tillgängligt i östra USA 2, centrala USA, USA, västra 2, norra centrala USA, västra USA, södra centrala USA, Frankrike, centrala, Västeuropa, Nordeuropa, Storbritannien, västra, Asien, sydöstra och Japan, östra.

## <a name="pricing"></a>Prissättning
I förhandsversionen är gratis. Du debiteras för andra resurser än Programgateway, till exempel Key Vault, virtuella datorer och så vidare. 

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

|Problem|Information|
|--|--|
|Certifikat för serverautentisering|Stöds ej.<br>Mer information finns i [översikt över slutpunkt till slutpunkt-SSL med Programgateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Blanda Standard_v2 och Standard Application Gateway i samma undernät|Stöds ej.<br>Dessutom om autoskalning är aktiverat, kan ett undernät bara ha en application gateway.|
|Användardefinierad väg (UDR) i Application Gateway-undernät|Stöds inte|
|NSG för inkommande portintervall| -65200 till 65535 för Standard_v2 SKU<br>-65503 till 65534 för Standard-SKU.<br>Mer information finns i den [vanliga frågor och svar](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Prestandaloggar i Azure-diagnostik|Stöds ej.<br>Du bör använda Azure-mått.|
|Fakturering|Det finns ingen fakturering för närvarande.|
|FIPS-läge, WebSocket|Dessa stöds inte för närvarande.|
|ILB läge|Detta stöds för närvarande inte. Offentliga och ILB-läget tillsammans stöds.|
|Web application firewall automatisk skalning|WAF har inte stöd för läget för automatisk skalning. Fast kapacitet läge stöds.|

## <a name="next-steps"></a>Nästa steg
- [Skapa en automatisk skalning, zonen redundant Programgateway med en reserverad virtuell IP-adress med hjälp av Azure PowerShell](tutorial-autoscale-ps.md)
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure brandvägg](../firewall/overview.md). 

