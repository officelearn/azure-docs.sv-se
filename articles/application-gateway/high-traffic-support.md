---
title: Stöd för Application Gateway hög trafik volym
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar Azure Application Gateway som stöd för stora nätverks trafiks volym scenarier.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 48730d03e9a578fb26b691577fa033e5f7bb4d19
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397492"
---
# <a name="application-gateway-high-traffic-support"></a>Stöd för höga trafikvolymer i Application Gateway

>[!NOTE]
> I den här artikeln beskrivs några rekommendationer som hjälper dig att konfigurera din Application Gateway för att hantera extra trafik på grund av hög trafik volym som kan uppstå på grund av COVID kris.

Du kan använda Application Gateway med brand vägg för webbaserade program (WAF) för en skalbar och säker metod för att hantera trafik till dina webb program.

Följande rekommendationer hjälper dig att konfigurera Application Gateway med WAF för att hantera extra trafik.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Använd v2-SKU över v1 för dess funktioner för automatisk skalning och prestanda för delar
V2-SKU: n erbjuder automatisk skalning för att säkerställa att Application Gateway kan skalas upp när trafiken ökar. Det ger också andra betydande prestanda för delar, till exempel 5x bättre TLS-avlastning, snabbare distribution och uppdaterings tider, zon redundans och mer jämfört med v1. Mer information finns i vår [v2-dokumentation](./application-gateway-autoscaling-zone-redundant.md). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ange maximalt antal instanser till högsta möjliga (125)
 
Förutsatt att du har en Application Gateway v2-SKU, ställer du in maximalt antal instanser på det maximala möjliga värdet 125, vilket gör att Application Gateway kan skalas upp efter behov. Detta gör det möjligt för IT att hantera den möjliga ökningen av trafiken till dina program. Du debiteras bara för de kapacitets enheter (CUs) som du använder.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Ange minsta antal instanser baserat på din genomsnittliga CU-användning

Förutsatt att du har en Application Gateway v2-SKU tar det sex till sju minuter för automatisk skalning att skala ut. Med ett högre minsta antal instanser kan Application Gateway bättre hantera trafiken när belastningen ökar, eftersom en insamling i trafik inte kräver en automatisk skalnings åtgärd.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Avisera om ett visst mått överskrider 75% av genomsnittligt CU-användning 
I [dokumentationen för Application Gateway statistik](./application-gateway-metrics.md#metrics-visualization) finns en detaljerad förklaring av våra mått och andra genom gångar. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exempel: Konfigurera en avisering på 75% av genomsnittligt CU-användning

Det här exemplet visar hur du använder Azure Portal för att ställa in en avisering när 75% av genomsnittligt CU-användning uppnås. 
1. Navigera till din **Application Gateway**.
2. På den vänstra panelen väljer du **mått** på fliken **övervakning** . 
3. Lägg till ett mått för **Genomsnittligt antal aktuella beräknings enheter**. 
![Konfigurera WAF-mått](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Om du har angett att det minsta antalet instanser ska vara den genomsnittliga CU-användningen kan du gå vidare och ange en avisering när 75% av de minsta instanserna används. Om den genomsnittliga användningen till exempel är 10 CUs, ställer du in en avisering på 7,5 CUs. Detta varnar dig om användningen ökar och ger dig tid att svara. Du kan öka det lägsta om du tror att den här trafiken kommer att behållas för att varna dig om att trafiken kan öka. 
![Konfigurera WAF-avisering](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Du kan ställa in aviseringen på ett lägre eller högre antal CU i procent beroende på hur känsliga du vill ha till gång till potentiella trafik toppar.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Konfigurera WAF med skydd mot filter för att stoppa attacker
Om du vill ha ett extra säkerhets lager framför ditt program, använder du Application Gateway WAF_v2 SKU för WAF-funktioner. Du kan konfigurera v2-SKU: n så att den endast tillåter åtkomst till dina program från ett specifikt land/region eller länder/regioner. Du ställer in en anpassad WAF-regel för att explicit tillåta eller blockera trafik baserat på den lokala platsen. Mer information finns i avsnittet om att [filtrera anpassade regler](../web-application-firewall/ag/geomatch-custom-rules.md) och [hur du konfigurerar anpassade regler på Application Gateway WAF_v2 SKU via PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Aktivera bot-skydd för att blockera kända dåliga robotar. Detta bör minska mängden trafik som får till ditt program. Mer information finns i [bot-skydd med konfigurera instruktioner](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Aktivera diagnostik på Application Gateway och WAF

Med diagnostikloggar kan du Visa brand Väggs loggar, prestanda loggar och åtkomst loggar. Du kan använda dessa loggar i Azure för att hantera och felsöka programgatewayer. Mer information finns i vår [diagnostiska dokumentation](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurera en TLS-princip för extra säkerhet
Se till att du använder den senaste TLS-[AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)(TLS policy version). Detta tillämpar TLS 1,2 och starkare chiffer. Mer information finns i [Konfigurera TLS-principinställningar och cipher-paket via PowerShell](./application-gateway-configure-ssl-policy-powershell.md).