---
title: Stöd för hög trafikvolym i Application Gateway
description: Den här artikeln innehåller vägledning för att konfigurera Azure Application Gateway till stöd för scenarier med hög nätverkstrafikvolym.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617203"
---
# <a name="application-gateway-high-traffic-support"></a>Stöd för höga trafikvolymer i Application Gateway

>[!NOTE]
> I den här artikeln beskrivs några förslag till riktlinjer som hjälper dig att konfigurera programgatewayen för att hantera extra trafik på grund av hög trafikvolym som kan uppstå på grund av COVID-19-krisen.

Du kan använda Application Gateway with Web Application Firewall (WAF) för ett skalbart och säkert sätt att hantera trafik till dina webbprogram.

Följande förslag hjälper dig att konfigurera Application Gateway med WAF för att hantera extra trafik.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Använd v2 SKU över v1 för dess funktioner för automatisk skalning och prestandafördelar
V2 SKU erbjuder automatisk skalning för att säkerställa att programgatewayen kan skalas upp när trafiken ökar. Det erbjuder också andra betydande prestandafördelar, till exempel 5x bättre TLS-avlastningsprestanda, snabbare driftsättnings- och uppdateringstider, zonredundans och mer jämfört med v1. Mer information finns i vår [v2-dokumentation](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ange maximalt antal instanser till högsta möjliga (125)
 
Förutsatt att du har en Application Gateway v2 SKU, ange det maximala antalet instanser till det högsta möjliga värdet av 125 tillåter Application Gateway att skala ut efter behov. Detta gör det möjligt att hantera den möjliga ökningen av trafiken till dina program. Du debiteras endast för de kapacitetsenheter som du använder.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Ange minsta antal instanser baserat på din genomsnittliga CU-användning

Förutsatt att du har en Application Gateway v2 SKU tar automatisk skalning sex till sju minuter att skala ut. Med ett högre minsta antal instanser kan Application Gateway bättre hantera din trafik när belastningen ökas, eftersom en ökning av trafiken inte kräver en automatisk skalning.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Varning om ett visst mått överträffar 75% av genomsnittlig CU utnyttjande 
Se dokumentationen för mätvärden för [programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) för en detaljerad förklaring av våra mått och andra genomgångar. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exempel: Ställa in en avisering på 75 % av den genomsnittliga CU-användningen

Det här exemplet visar hur du använder Azure-portalen för att ställa in en avisering när 75 % av den genomsnittliga CU-användningen har uppnåtts. 
1. Navigera till **programgatewayen**.
2. Välj **Mått** under fliken **Övervakning** på den vänstra panelen. 
3. Lägg till ett mått för **genomsnittliga aktuella beräkningsenheter**. 
![Ställa in WAF-mått](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Om du har angett att antalet instanser som är din minsta instans ska vara din genomsnittliga CU-användning kan du ange en avisering när 75 % av dina minsta instanser används. Om din genomsnittliga användning till exempel är 10 CUs ställer du in en avisering på 7,5 CUs. Detta varnar dig om användningen ökar och ger dig tid att svara. Du kan höja minimum om du tror att denna trafik kommer att upprätthållas för att varna dig om att trafiken kan öka. 
![Ställa in WAF-avisering](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Du kan ställa in aviseringen så att den ska ske med en lägre eller högre CU-utnyttjandeprocent beroende på hur känslig du vill vara för potentiella trafiktoppar.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Konfigurera WAF med geofiltrering och botskydd för att stoppa attacker
Om du vill ha ett extra säkerhetslager framför programmet använder du funktionerna Application Gateway WAF_v2 SKU for WAF. Du kan konfigurera v2 SKU så att den endast tillåter åtkomst till dina program från ett visst land eller länder. Du ställer in en anpassad WAF-regel för att uttryckligen tillåta eller blockera trafik baserat på geolokalisering. Mer information finns i [geofiltrera anpassade regler](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) och [hur du konfigurerar anpassade regler för Application Gateway WAF_v2 SKU via PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Aktivera botskydd för att blockera kända dåliga robotar. Detta bör minska mängden trafik som kommer till ditt program. Mer information finns i [botskydd med ställa in instruktioner](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Aktivera diagnostik på Application Gateway och WAF

Med diagnostikloggar kan du visa brandväggsloggar, prestandaloggar och åtkomstloggar. Du kan använda dessa loggar i Azure för att hantera och felsöka Application Gateways. Mer information finns i [dokumentationen för diagnostik](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurera en TLS-princip för extra säkerhet
Se till att du använder den senaste TLS-principversionen[(AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Detta upprätthåller TLS 1.2 och starkare chiffer. Mer information finns i [konfigurera TLS-principversioner och chiffersviter via PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
