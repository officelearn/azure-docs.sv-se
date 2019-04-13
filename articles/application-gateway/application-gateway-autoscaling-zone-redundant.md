---
title: Automatisk skalning och zonredundant Application Gateway i Azure (offentlig förhandsversion)
description: Den här artikeln introducerar v2-SKU Azure-program som innehåller funktioner för automatisk skalning och zonredundant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548736"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)

Application Gateway och Web Application Firewall (WAF) finns nu i offentlig förhandsversion under en ny v2-SKU som ger prestandaförbättringar och lägger till stöd för viktiga nya funktioner som automatisk skalning, redundans och stöd för statiska virtuella IP-adresser. Befintliga funktioner under den allmänt tillgängliga SKU: N fortfarande användas i den nya v2-SKU, med några undantag som anges i avsnitt kända begränsningar. De nya v2-SKU: er inkluderar följande förbättringar:

- **Automatisk skalning**: Application Gateway eller WAF-distributioner under autoskalning SKU kan skala upp eller ned utifrån ändrade trafikmönster belastningen. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Den här SKU: N erbjuder SANT elasticitet. I den nya SKU: N kan Application Gateway fungerar både fast kapacitet (automatisk skalning inaktiverat) och i läget för automatisk skalning aktiverat. Fast kapacitet läge är användbart för scenarier med konsekventa och förutsägbara arbetsbelastningar. Läget för automatisk skalning är bra i program som finns ser många variationer i programtrafiken.

- **Zon redundans**: En Application Gateway eller WAF-distribution kan sträcka sig över flera Tillgänglighetszoner, ta bort behovet av att etablera och skapa separata Application Gateway-instanser i varje zon med Traffic Manager. Du kan välja en enskild zon eller flera zoner där Application Gateway-instanser har distribuerats, därmed säkerställa fel zonelasticitet. Serverdelspoolen för program kan distribueras på samma sätt i olika tillgänglighetszoner.
- **Prestandaförbättringar**: Autoskalning SKU: N erbjuder upp till 5 X bättre SSL-avlastning prestandan jämfört med den allmänt tillgängliga SKU: N.
- **Snabbare distribution och uppdatering** autoskalning SKU ger distribution och uppdatering snabbare jämfört med den allmänt tillgängliga SKU: N.
- **Statisk VIP**: VIP-Adressen för application gateway stöder nu statiska VIP-typ som är exklusivt. Detta säkerställer att VIP-Adressen som är associerade med application gateway inte ändras även efter en omstart.

> [!IMPORTANT]
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> Automatisk skalning och zonredundant application gateway SKU: N stöder nu [standard hälsoavsökning](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) att automatiskt övervaka hälsotillståndet för alla resurser i dess backend-poolen och markera dessa backend-medlemmar som anses feltillstånd. Standard hälsotillstånd avsökningen ska konfigureras automatiskt för de serverdelar som du inte har konfigurerat någon konfiguration för anpassad avsökning. Mer information finns i [hälsoavsökningar i application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Jämförelse mellan SKU: N v1 och v2-SKU

I följande tabell jämförs funktionerna med varje SKU.

|                                                   | V1-SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisk skalning                                       |          | &#x2713; |
| Redundans                                   |          | &#x2713; |
| &nbsp;Statisk VIP&nbsp;&nbsp;                      |          | &#x2713; |
| URL-baserad routning                                 | &#x2713; | &#x2713; |
| Värd för flera platser                             | &#x2713; | &#x2713; |
| Trafik-omdirigering                               | &#x2713; | &#x2713; |
| Brandvägg för webbaserade program (WAF)                    | &#x2713; | &#x2713; |
| Secure Sockets Layer-avslutning (SSL)            | &#x2713; | &#x2713; |
| Slutpunkt till slutpunkt SSL-kryptering                         | &#x2713; | &#x2713; |
| Sessionstillhörighet                                  | &#x2713; | &#x2713; |
| Anpassade felsidor                                | &#x2713; | &#x2713; |
| Skriv om rubriker för HTTP (S)                           |          | &#x2713; |
| WebSocket-stöd                                 | &#x2713; | &#x2713; |
| Stöd för HTTP/2                                    | &#x2713; | &#x2713; |
| Anslutningstömning                               | &#x2713; | &#x2713; |
| Azure Kubernetes Service (AKS) Ingress-kontrollant |          | &#x2713; |

## <a name="supported-regions"></a>Regioner som stöds

Autoskalning SKU är tillgänglig i följande regioner: Norra centrala USA, södra centrala USA, västra USA, västra USA 2, östra USA, östra USA 2, centrala USA, Nordeuropa, Västeuropa, Sydostasien, Frankrike, centrala, Storbritannien, västra, östra Japan, västra Japan.

## <a name="pricing"></a>Prissättning

I förhandsversionen är gratis. Du debiteras för andra resurser än Programgateway, till exempel Key Vault, virtuella datorer, och så vidare.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

|Problem|Information|
|--|--|
|Certifikat för serverautentisering|Stöds ej.<br>Mer information finns i [översikt över slutpunkt till slutpunkt-SSL med Programgateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Blanda Standard_v2 och Standard Application Gateway i samma undernät|Stöds inte|
|Användardefinierad väg (UDR) i Application Gateway-undernät|Stöds inte|
|NSG för inkommande portintervall| -65200 till 65535 för Standard_v2 SKU<br>-65503 till 65534 för Standard-SKU.<br>Mer information finns i den [vanliga frågor och svar](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Prestandaloggar i Azure-diagnostik|Stöds ej.<br>Du bör använda Azure-mått.|
|Fakturering|Det finns ingen fakturering för närvarande.|
|FIPS-läge|Dessa stöds inte för närvarande.|
|ILB läge|Detta stöds för närvarande inte. Offentliga och ILB-läget tillsammans stöds.|
|NetWatcher-integrering|Stöds inte i den offentliga förhandsversionen.|

## <a name="next-steps"></a>Nästa steg
- [Skapa en automatisk skalning, zonen redundant Programgateway med en reserverad virtuell IP-adress med hjälp av Azure PowerShell](tutorial-autoscale-ps.md)
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure brandvägg](../firewall/overview.md).
